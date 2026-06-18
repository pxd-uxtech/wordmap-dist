# ForceBubble

Force-directed clustered wordmap visualization powering the AffinityBubble.

Originally created by [@taekie](https://github.com/taekie).
Copyright (c) 2026 UXtechLab. Licensed under [BUSL-1.1](https://mariadb.com/bsl11/).

A sibling of [voronoi-bubble](https://github.com/pxd-uxtech/voronoi-bubble-dist): where VoronoiBubble lays out hierarchical text as **angular Voronoi cells**, ForceBubble lays it out as **smooth force-packed cluster blobs** — a 2-level (c2 → c1) force simulation with word collision, density-centered labels, and concave-looking hull regions.

## Install / Import

Two builds are published under `dist/`:

| File | d3 | Use case |
|---|---|---|
| `force-bubble.standalone.js` | **bundled in** | Drop-in — no separate d3 needed |
| `force-bubble.esm.js` | external | Observable / bundlers that already provide d3 v7 |

### Standalone (recommended — d3 included)

```js
const { createForceBubble } = await import(
  "https://cdn.jsdelivr.net/gh/pxd-uxtech/force-bubble-dist@<commit>/dist/force-bubble.standalone.js"
);
createForceBubble(container, flat, { extras: { positions, c1Order, c2Order } });
```

### ESM with external d3 (Observable)

```html
<script src="https://cdn.jsdelivr.net/npm/d3@7"></script>
```
```js
const { createForceBubble } = await import(
  "https://cdn.jsdelivr.net/gh/pxd-uxtech/force-bubble-dist@<commit>/dist/force-bubble.esm.js"
);
createForceBubble(container, flat, { d3: window.d3, extras: { positions, c1Order, c2Order } });
```

> Pin a specific `@<commit>` hash for production — `@main` is cached by jsDelivr for up to 12h.

## Data format

A flat array of leaf items + cluster start positions:

```js
const flat = cellData.map(d => ({
  text: d.text,
  size: d.size || 1,   // 생략 시 1 — autoAggregate가 합산
  c1: labels[d.c1],    // 소분류(2차) 라벨
  c2: bigLabels[d.c2], // 대분류(1차) 라벨
}));

// regionPos (depth1=c2, depth2=c1) → positions
const positions = { c1: {}, c2: {} };
for (const r of regionPos) (r.depth === 1 ? positions.c2 : positions.c1)[r.key] = { x: r.x, y: r.y };

createForceBubble(container, flat, {
  extras: { positions, c1Order: labels, c2Order: bigLabels },
});
```

Call `chart.destroy()` before re-rendering. Sentiment coloring:
`sentiment: { domain:[1,3,5], range:["#f69f8f","#ffe9a9","#88CD8B"], scoreFallback:3 }` + `extras.scores: { c1, c2 }`.

## Built-in settings panel

Pass `settingsPanel: true` to embed a gear icon + slide-in panel with 6 live sliders. Changes debounce (400ms) → `localStorage` → self-rerender.

```js
createForceBubble(container, flat, {
  settingsPanel: true,
  extras: { positions, c1Order, c2Order },
});
```

| Slider | Option | Default |
|---|---|---|
| c1 응집력 | `clusterCohesion` | 0.08 |
| c1 간격 | `clusterCollidePad` | 14 |
| c2 간격 | `clusterC2CollidePad` | 0 |
| c2 영역 | `clusterPad {x,y}` | 220 |
| c2 응집 | `clusterC2Compact` | 0.25 |
| c2 분리 | `clusterC2Collide` | 0.2 |

## Key options

| Option | Default | Description |
|---|---|---|
| `d3` | — | d3 v7 instance (esm build only; standalone bundles it) |
| `settingsPanel` | `false` | Embed gear + slider panel with self-rerender |
| `c2HierFloorMul` | `1.15` | Global hierarchy floor — every top (c2) label ≥ (max c1 fs) × this, so small clusters' titles never shrink below their sub-labels |
| `colors` / `regionColors` | — | Cluster color palette / per-key overrides |
| `title` / `caption` | — | Chart text |
| `onClick` | — | Item/cluster click callback |

Hull regions use `curveBasisClosed` over the densified convex hull for smooth, rounded (concave-looking) outlines.

## License

[BUSL-1.1](https://mariadb.com/bsl11/) — Copyright (c) 2026 UXtechLab.
