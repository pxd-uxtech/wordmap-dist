# Wordmap

A map-like visualization for exploring words grouped by topic and meaning.
주제별로 묶인 단어들을 지도처럼 펼쳐 보여주는 시각화 라이브러리.

![Wordmap](./docs/example.png)

Originally created by [@taekie](https://github.com/taekie).
Copyright (c) 2026 UXtechLab. Licensed under [BUSL-1.1](https://mariadb.com/bsl11/).

A sibling of [voronoi-bubble](https://github.com/pxd-uxtech/voronoi-bubble-dist): where VoronoiBubble lays out hierarchical text as **angular Voronoi cells**, Wordmap lays it out as **smooth force-packed cluster blobs** — a 2-level (c2 → c1) force simulation with word collision, density-centered labels, and concave-looking hull regions.

## Install / Import

Two builds are published under `dist/`:

| File | d3 | Use case |
|---|---|---|
| `wordmap.standalone.js` | **bundled in** | Drop-in — no separate d3 needed |
| `wordmap.esm.js` | external | Observable / bundlers that already provide d3 v7 |

### Standalone (recommended — d3 included)

```js
const { createWordmap } = await import(
  "https://cdn.jsdelivr.net/gh/pxd-uxtech/wordmap-dist@<commit>/dist/wordmap.standalone.js"
);
createWordmap(container, flat, { extras: { positions, c1Order, c2Order } });
```

### ESM with external d3 (Observable)

```html
<script src="https://cdn.jsdelivr.net/npm/d3@7"></script>
```
```js
const { createWordmap } = await import(
  "https://cdn.jsdelivr.net/gh/pxd-uxtech/wordmap-dist@<commit>/dist/wordmap.esm.js"
);
createWordmap(container, flat, { d3: window.d3, extras: { positions, c1Order, c2Order } });
```

> Pin a specific `@<commit>` hash for production — `@main` is cached by jsDelivr for up to 12h.
> `createForceBubble` is kept as an alias of `createWordmap` for backward compatibility.

## Data format

Wordmap renders a **3-level hierarchy** (대분류 → 소분류 → 단어):

```
c2   대분류 (top group)      e.g. "서비스 시스템"
└─ c1   소분류 (sub-group)    e.g. "서비스 디자인 기초"
   └─ word  단어 (leaf)       개별 단어 — 빈도(size)로 크기 결정
```

The top group (`c2`) becomes a colored cluster blob, each sub-group (`c1`) gets a hull + label inside it, and individual words are packed by frequency.

Input is a **flat array of leaf items** — every word carries its own `c1`/`c2` labels — plus cluster start positions:

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

createWordmap(container, flat, {
  extras: { positions, c1Order: labels, c2Order: bigLabels },
});
```

Call `chart.destroy()` before re-rendering. Sentiment coloring:
`sentiment: { domain:[1,3,5], range:["#f69f8f","#ffe9a9","#88CD8B"], scoreFallback:3 }` + `extras.scores: { c1, c2 }`.

## Built-in settings panel

Pass `settingsPanel: true` to embed a gear icon + slide-in panel with **3 plain-language sliders**. Each slider expresses an *outcome* (not a force variable) and drives several internal options at once. The middle (50%) reproduces the library defaults. Changes debounce (400ms) → `localStorage` → self-rerender.

```js
createWordmap(container, flat, {
  settingsPanel: true,
  extras: { positions, c1Order, c2Order },
});
```

| 슬라이더 | 의미 (왼쪽 ↔ 오른쪽) | 내부적으로 조정하는 옵션 |
|---|---|---|
| **단어 빽빽함** | 여유롭게 ↔ 촘촘하게 | `clusterCohesion` + `clusterCollidePad` |
| **그룹 또렷함** | 섞이게 ↔ 또렷하게 | `clusterC2Collide` + `clusterC2CollidePad` |
| **펼침 정도** | 모아서 ↔ 펼쳐서 | `clusterPad` + `clusterC2Compact` |

> 슬라이더는 비전문가도 결과를 보고 조정할 수 있도록 의미 축으로 묶여 있습니다. 개별 force 변수를 직접 세팅하려면 위 옵션을 `createWordmap(...)` 호출에 직접 넘기면 됩니다.

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
