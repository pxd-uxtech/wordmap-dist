# Contributing

## ⚠️ Do NOT edit dist files directly

This repository contains compiled output only. All JavaScript files under `dist/` are generated automatically from the source repository.

**Source repo:** [pxd-uxtech/affinitybubble-library](https://github.com/pxd-uxtech/affinitybubble-library) — `wordmap-force-library.js`

## Correct workflow

1. Make changes in the source repo (`affinitybubble-library/wordmap-force-library.js`)
2. Build both bundles:

```
esbuild wordmap-force-library.js --bundle --format=esm \
  --outfile=dist/force-bubble.esm.js --platform=browser --external:d3

esbuild force-bubble.standalone.entry.js --bundle --format=esm \
  --outfile=dist/force-bubble.standalone.js --platform=browser
```

3. Copy `dist/*` to this repo
4. Commit and push

```
affinitybubble-library  →  esbuild  →  force-bubble-dist
     (source)                              (dist only)
```
