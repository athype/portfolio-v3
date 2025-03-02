<script>
    import { onMount } from 'svelte';
    import * as THREE from 'three';

    let canvasContainer;
    let isInitialized = false;
    let animationFrameId;
    let renderer, scene, camera;

    onMount(() => {
        if (typeof window !== 'undefined' && !isInitialized) {
            isInitialized = true;
            initGlobe();

            return () => {
                // Clean up three.js resources
                if (animationFrameId) {
                    cancelAnimationFrame(animationFrameId);
                }

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

    function initGlobe() {
        // Main color - purple (#6f6be0)
        const mainColor = new THREE.Color(0x6f6be0);
        const accentColor = new THREE.Color(0x8a87ff);

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

        // Create globe
        const radius = 1;
        const segments = 32; // Reduced from 48 for less density

        // Globe wireframe
        const globeGeometry = new THREE.SphereGeometry(radius, segments, segments);
        const wireframe = new THREE.WireframeGeometry(globeGeometry);
        const globeLine = new THREE.LineSegments(wireframe);

        // Material settings
        globeLine.material = new THREE.LineBasicMaterial({
            color: mainColor,
            transparent: true,
            opacity: 0.4,
            linewidth: 1
        });

        scene.add(globeLine);

        // Create points/vertices with less density
        const pointsGeometry = new THREE.SphereGeometry(radius, segments/4, segments/4);
        const pointsMaterial = new THREE.PointsMaterial({
            color: mainColor,
            size: 0.03,
            transparent: true,
            opacity: 0.8
        });

        const points = new THREE.Points(pointsGeometry, pointsMaterial);
        scene.add(points);

        // Add glow
        const glowGeometry = new THREE.SphereGeometry(radius * 1.05, segments/2, segments/2);
        const glowMaterial = new THREE.MeshBasicMaterial({
            color: mainColor,
            transparent: true,
            opacity: 0.15
        });

        const glow = new THREE.Mesh(glowGeometry, glowMaterial);
        scene.add(glow);

        // Add connections OUTSIDE the globe
        const connections = createConnections(radius);
        scene.add(connections);

        // Animation
        const animate = () => {
            // Rotate the globe
            globeLine.rotation.y += 0.002;
            points.rotation.y += 0.002;
            glow.rotation.y += 0.001;
            connections.rotation.y += 0.002;

            // Subtle up and down movement
            const time = Date.now() * 0.0005;
            globeLine.position.y = Math.sin(time) * 0.05;
            points.position.y = Math.sin(time) * 0.05;
            glow.position.y = Math.sin(time) * 0.05;
            connections.position.y = Math.sin(time) * 0.05;

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
        };
    }

    function createConnections(radius) {
        const group = new THREE.Group();

        // Create fewer connection lines (reduced from 15)
        for (let i = 0; i < 8; i++) {
            // Create two random points on the sphere
            const startPoint = randomPointOnSphere(radius);
            const endPoint = randomPointOnSphere(radius);

            // Make the curve go outside the globe by increasing the height factor
            // and using a larger radius for the curve control point
            const curveRadius = radius * 1.3; // Make curves extend further out

            const curveHeight = Math.random() * 0.8 + 0.7; // Random height between 0.7 and 1.5

            const curve = new THREE.QuadraticBezierCurve3(
                new THREE.Vector3(startPoint.x, startPoint.y, startPoint.z),
                new THREE.Vector3(
                    (startPoint.x + endPoint.x) * 0.5 * curveRadius,
                    (startPoint.y + endPoint.y) * 0.5 * curveRadius + curveHeight,
                    (startPoint.z + endPoint.z) * 0.5 * curveRadius
                ),
                new THREE.Vector3(endPoint.x, endPoint.y, endPoint.z)
            );

            const points = curve.getPoints(20);
            const geometry = new THREE.BufferGeometry().setFromPoints(points);

            const material = new THREE.LineBasicMaterial({
                color: 0x8a87ff, // Slightly different shade for contrast
                transparent: true,
                opacity: 0.5 + Math.random() * 0.3
            });

            const curveObject = new THREE.Line(geometry, material);
            group.add(curveObject);
        }

        return group;
    }

    function randomPointOnSphere(radius) {
        const u = Math.random();
        const v = Math.random();

        const theta = 2 * Math.PI * u;
        const phi = Math.acos(2 * v - 1);

        const x = radius * Math.sin(phi) * Math.cos(theta);
        const y = radius * Math.sin(phi) * Math.sin(theta);
        const z = radius * Math.cos(phi);

        return {x, y, z};
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
        background: rgba(111, 107, 224, 0.1); /* Updated to match new color */
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