<script>
    import { onMount } from 'svelte';

    let scrollY = 0;
    let innerHeight = 0;
    let isDebugVisible = true;

    onMount(() => {
        const handleScroll = () => {
            scrollY = window.scrollY;
        };

        window.addEventListener('scroll', handleScroll);
        innerHeight = window.innerHeight;

        return () => {
            window.removeEventListener('scroll', handleScroll);
        };
    });

    function toggleDebug() {
        isDebugVisible = !isDebugVisible;
    }
</script>

{#if isDebugVisible}
    <div class="debug-panel">
        <div class="debug-header">
            <h4>Debug Panel</h4>
            <button on:click={toggleDebug}>Hide</button>
        </div>
        <div class="debug-content">
            <p>Scroll Position: {scrollY}px</p>
            <p>Viewport Height: {innerHeight}px</p>
            <p>Trigger Point (80%): {Math.round(innerHeight * 0.8)}px from top</p>

            <div class="section-debug">
                <h5>Section Visibility:</h5>
                <ul>
                    <li>Hero: Visible</li>
                    <li>About: {scrollY > 300 ? 'Should be visible' : 'Not in view yet'}</li>
                    <li>Projects: {scrollY > 900 ? 'Should be visible' : 'Not in view yet'}</li>
                    <li>Contact: {scrollY > 1500 ? 'Should be visible' : 'Not in view yet'}</li>
                </ul>
            </div>

            <button on:click={() => { document.body.style.overflow = 'visible' }}>
                Fix Scroll Lock
            </button>
        </div>
    </div>
{:else}
    <button class="debug-toggle" on:click={toggleDebug}>Debug</button>
{/if}

<style>
    .debug-panel {
        position: fixed;
        bottom: 20px;
        right: 20px;
        background: rgba(0, 0, 0, 0.8);
        color: white;
        padding: 15px;
        border-radius: 5px;
        z-index: 9999;
        font-size: 14px;
        max-width: 300px;
        font-family: monospace;
    }

    .debug-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 10px;
    }

    h4, h5 {
        margin: 0;
        font-size: 16px;
    }

    .debug-content p {
        margin: 5px 0;
    }

    .section-debug {
        margin-top: 10px;
    }

    ul {
        margin: 5px 0;
        padding-left: 20px;
    }

    .debug-toggle {
        position: fixed;
        bottom: 20px;
        right: 20px;
        z-index: 9999;
        padding: 5px 10px;
    }

    button {
        background: #666;
        border: none;
        color: white;
        padding: 3px 8px;
        border-radius: 3px;
        cursor: pointer;
    }
</style>