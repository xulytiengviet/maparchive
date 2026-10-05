# GitHub Pages

GitHub Pages serves a standalone static WebGIS from `index.html`.

This is intentional: the main SvelteKit application uses the Cloudflare adapter
and server routes, which GitHub Pages cannot execute. The static entry point
therefore uses MapLibre GL JS + PMTiles directly in the browser.

Historical layer:
`https://tiles.maparchive.vn/overlay/l7014-20260913.pmtiles`

Default camera:
TP.HCM, `10.79985, 106.16616, z9.06, rotation 0.0`.

The same `index.html` is also copied to `docs/index.html` so the site works
whether repository Pages is configured for the repository root or the `/docs`
folder.
