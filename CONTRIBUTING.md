# Contributing

## ⚠️ Do NOT edit dist files directly

This repository contains compiled output only. All JavaScript files under `dist/` are generated automatically from the source repository.

**Source repo:** [pxd-uxtech/wordmap](https://github.com/pxd-uxtech/wordmap) — `wordmap.js`

## Correct workflow

1. Make changes in the source repo (`wordmap/wordmap.js`)
2. Build both bundles:

```
esbuild wordmap.js --bundle --format=esm \
  --outfile=dist/wordmap.esm.js --platform=browser --external:d3

esbuild wordmap.standalone.js --bundle --format=esm \
  --outfile=dist/wordmap.standalone.js --platform=browser
```

3. Copy `dist/*` to this repo
4. Commit and push

```
wordmap        →  esbuild  →  wordmap-dist
  (source)                      (dist only)
```
