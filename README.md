# ResourceDelay

Light‑weight (≈1.7 kB gzipped) helper that shows a full‑screen pre‑loader **and** lazy‑loads heavy CSS / JS / web‑fonts *in parallel*.

```html
<script src="resource-delay.min.js"></script>
```

---

## Why?

* Render‑blocking CSS, large JS bundles and custom fonts slow the first paint.
* You often want to show a branded splash‑screen, finish loading assets in the background, then start the app.
* Framework‑agnostic: pure ES module / UMD, zero dependencies.

---

## Quick start (parallel load)

```html
<script src="resource-delay.min.js"></script>
<script>
ResourceDelay.delay({
  html: '<h2 style="font:24px system-ui">Loading…</h2>',
  css:  [
    'https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css'
  ],
  js:   [
    'https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js'
  ],
 fonts: [
    ['Inter', 'https://webapps1.chicago.gov/cdn/Fonts/glyphicons-halflings-regular.woff2']
]
}).then(initApp);
</script>
```

All listed URLs are fetched **simultaneously**; the promise resolves when every request finishes (or fails). The splash screen is automatically removed.

---

## Sequential loading (when order matters)

Need CSS first, JS after? Call `delay()` twice:

```js
ResourceDelay.delay({ css:['/css/app.css'] })
  .then(() => ResourceDelay.delay({ js:['/js/app.js'] }))
  .then(startApp);
```

You can also mix the helpers:

```js
await ResourceDelay.loadCSS('/critical.css');
await Promise.all([
  ResourceDelay.loadJS('/analytics.js'),
  ResourceDelay.loadFont('Inter','/fonts/Inter.woff2')
]);
```

---

## API

| Method               | Description                                                                                                                                          |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `delay(opts)`        | Show pre‑loader, load all resources in **parallel**, return `Promise<void>`. `opts` keys: `css[]`, `js[]`, `fonts[]`, `html` (custom splash markup). |
| `loadCSS(url)`       | Append `<link rel="stylesheet">`, resolve on `onload`.                                                                                               |
| `loadJS(url)`        | Append `<script async>`, resolve on `onload`.                                                                                                        |
| `loadFont(name,url)` | Use `FontFace` API if available, else fallback to CSS link.                                                                                          |

`delay()` internally deduplicates URLs: same file requested twice loads only once.

---

## File size

```
resource-delay.min.js •  2.8 kB raw  •  ≈1.7 kB gzipped
```

---

## License

MIT © 2025
