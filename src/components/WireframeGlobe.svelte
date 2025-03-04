<script>
    import { onMount } from 'svelte';
    import * as THREE from 'three';

    let canvasContainer;
    let isInitialized = false;
    let animationFrameId;
    let renderer, scene, camera;

    // Variables for mouse rotation
    let targetRotationX = 0;
    let targetRotationY = 0;
    let currentRotationX = 0;
    let currentRotationY = 0;
    let globeGroup; // Group to hold all globe elements for unified rotation

    onMount(() => {
        if (typeof window !== 'undefined' && !isInitialized) {
            isInitialized = true;
            initGlobe();

            return () => {
                // Clean up three.js resources
                if (animationFrameId) {
                    cancelAnimationFrame(animationFrameId);
                }

                // Remove event listener
                window.removeEventListener('mousemove', handleMouseMove);

                cleanup();
            };
        }
    });

    // Function to safely dispose of Three.js resources
    function safeDispose(object) {
        if (object) {
            if (object.geometry) object.geometry.dispose();

            if (object.material) {
                // Handle both single material and material array
                if (Array.isArray(object.material)) {
                    object.material.forEach(material => {
                        if (material.dispose) material.dispose();
                    });
                } else if (object.material.dispose) {
                    object.material.dispose();
                }
            }
        }
    }

    function cleanup() {
        if (renderer) renderer.dispose();

        // Clear scene
        if (scene) {
            scene.traverse(object => {
                safeDispose(object);
            });
        }
    }

    function handleMouseMove(event) {
        // Calculate normalized mouse position (-1 to 1)
        const mouseX = (event.clientX / window.innerWidth) * 2 - 1;
        const mouseY = (event.clientY / window.innerHeight) * 2 - 1;

        // Set target rotation based on mouse position
        // Limited rotation range for subtle effect
        targetRotationY = mouseX * 0.5;
        targetRotationX = mouseY * 0.3;
    }

    function initGlobe() {
        const mainColor = new THREE.Color(0xc79900);

        // Scene setup
        scene = new THREE.Scene();
        camera = new THREE.PerspectiveCamera(50, 1, 0.1, 1000);

        renderer = new THREE.WebGLRenderer({
            alpha: true,
            antialias: true
        });

        renderer.setSize(canvasContainer.clientWidth, canvasContainer.clientHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        canvasContainer.appendChild(renderer.domElement);

        // Camera position
        camera.position.z = 2.5;

        // Create a group to hold all globe elements
        globeGroup = new THREE.Group();
        scene.add(globeGroup);

        // Initial slight rotation to the side
        globeGroup.rotation.y = Math.PI * 0.25;

        // Create globe
        const radius = 1;
        const segments = 16;

        // Globe wireframe
        const globeGeometry = new THREE.SphereGeometry(radius, segments, segments);
        const wireframe = new THREE.WireframeGeometry(globeGeometry);
        const globeLine = new THREE.LineSegments(wireframe);

        // Material settings
        globeLine.material = new THREE.LineBasicMaterial({
            color: mainColor,
            transparent: true,
            opacity: 0.8,
            linewidth: 1
        });

        globeGroup.add(globeLine);

        // Create points/vertices
        const pointsGeometry = new THREE.SphereGeometry(radius, segments/4, segments/4);
        const pointsMaterial = new THREE.PointsMaterial({
            color: mainColor,
            size: 0.03,
            transparent: true,
            opacity: 0.8
        });

        const points = new THREE.Points(pointsGeometry, pointsMaterial);
        globeGroup.add(points);

        // Add glow
        const glowGeometry = new THREE.SphereGeometry(radius * 1.05, segments/2, segments/2);
        const glowMaterial = new THREE.MeshBasicMaterial({
            color: mainColor,
            transparent: true,
            opacity: 0.15
        });

        const glow = new THREE.Mesh(glowGeometry, glowMaterial);
        globeGroup.add(glow);

        // Add mouse move event listener
        window.addEventListener('mousemove', handleMouseMove);

        // Animation
        const animate = () => {
            // Smooth rotation toward target
            currentRotationX += (targetRotationX - currentRotationX) * 0.05;
            currentRotationY += (targetRotationY - currentRotationY) * 0.05;

            // Apply the rotation while preserving the initial side rotation
            globeGroup.rotation.x = currentRotationX;
            globeGroup.rotation.y = Math.PI * 0.15 + currentRotationY;

            // Add a very slight continuous rotation for when mouse isn't moving
            globeGroup.rotation.y += 0.020;

            // Subtle up and down movement
            const time = Date.now() * 0.001;
            globeGroup.position.y = Math.sin(time) * 0.06;

            renderer.render(scene, camera);
            animationFrameId = requestAnimationFrame(animate);
        };

        animate();

        // Handle resize
        const handleResize = () => {
            const width = canvasContainer.clientWidth;
            const height = canvasContainer.clientHeight;

            renderer.setSize(width, height);
            camera.aspect = width / height;
            camera.updateProjectionMatrix();
        };

        window.addEventListener('resize', handleResize);

        // Return a cleanup function for event listeners
        return () => {
            window.removeEventListener('resize', handleResize);
            window.removeEventListener('mousemove', handleMouseMove);
        };
    }
</script>

<div class="globe" bind:this={canvasContainer}>
    <div class="pulse"></div>
</div>

<style>
    .globe {
        width: 100%;
        height: 100%;
        position: relative;
    }

    .pulse {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 70%;
        height: 70%;
        border-radius: 50%;
        background: rgba(111, 107, 224, 0.1); /* Match the globe color */
        animation: pulse 3s infinite;
        z-index: -1;
    }

    @keyframes pulse {
        0% {
            transform: translate(-50%, -50%) scale(0.8);
            opacity: 0.6;
        }
        50% {
            transform: translate(-50%, -50%) scale(1);
            opacity: 0.3;
        }
        100% {
            transform: translate(-50%, -50%) scale(0.8);
            opacity: 0.6;
        }
    }
</style>