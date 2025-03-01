<script>
    export let name = '';
    export let size = '24px';
    export let color = 'currentColor';

    const icons = {
        home: `<path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z" />`,
        work: `<path d="M10 10.5h4v-3h-4v3zm0 4.5h4v-3h-4v3zM20 7v12c0 1.1-.9 2-2 2H6c-1.1 0-2-.9-2-2V7c0-1.1.9-2 2-2h1V3h2v2h6V3h2v2h1c1.1 0 2 .9 2 2z" />`,
        about: `<path d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z" />`,
        contact: `<path d="M20 4H4c-1.1 0-1.99.9-1.99 2L2 18c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm-5 14h-2v-2h2v2zm0-4h-2V8h2v6z" />`
    };
</script>

<svg
        xmlns="http://www.w3.org/2000/svg"
        width={size}
        height={size}
        viewBox="0 0 24 24"
        fill={color}
        stroke="none"
        aria-hidden="true"
>
    {@html icons[name] || ''}
</svg>