# Human Atlas

An interactive 3D anatomy explorer built with React, Three.js, and shadcn/ui. Switch between **male and female reference anatomies** in the same application and branch.

| Atlas | Selectable meshes | Named concepts | Coverage |
|---|---:|---:|---|
| Male · BodyParts3D 4.0 | 2,234 | 3,432 | Adult male reference, 15 display systems |
| Female · Human Reference Atlas v1.5 | 888 | 1,073 | Body surface, selected organs and reproductive anatomy; partial skeleton/muscles |

[Upstream live demo](https://human-atlas-seven.vercel.app) — this is the upstream deployment, not a deployment of this fork's changes.

## Explore

- Choose Male anatomy or Female anatomy using the selector below the title. Switching resets the explorer and cancels the previous model's downloads.
- Orbit, zoom, and select structures directly on the body.
- Toggle individual systems or use skeleton and organ presets.
- Move from assembled anatomy to a spaced inventory of every visible piece.
- Search anatomical names and source identifiers.
- Isolate a selected structure and read its details.
- Use compact controls and detail panels on mobile.

## Run locally

Requires Node.js 22.13 or newer. No API keys or accounts are needed.

```sh
npm ci
npm run dev
```

Open http://localhost:3016. To build the static site, run `npm run build`; the output is in `dist/`.

## Validate

```sh
npm run check
node scripts/validate-atlas.mjs
node scripts/validate-interactions.mjs
npm run build
```

Validation covers mesh buffers, names and concept membership, nonoverlapping exploded layouts at desktop and mobile aspect ratios, search and inspection contracts, and tap-versus-drag handling. Browser interaction checks have exercised selection, system controls, search, isolation, rotation, and 390×844, 320×568, and 844×390 layouts. Phone controls stay clear of the exploded inventory, and isolated structures fit the space above or beside the detail panel. Physical-device performance and real multitouch hardware have not been tested.

## Anatomy data

The viewer includes **BodyParts3D 4.0** adult male anatomy and **Human Reference Atlas / HuBMAP, 3D Reference Organ Set for Female v1.5**, both attributed under **CC BY 4.0**. These independent collections have different coverage. Individual source meshes are distinct from named concepts, which may group multiple meshes. Descriptions distinguish general system context from individual organ explanations.

Geometry is simplified for browser performance while retaining every source mesh. Male geometry contains 2,288,268 triangles (~33 MB compressed); female geometry contains 1,810,038 triangles (~23.6 MB compressed). Only the selected atlas is loaded. Full credits, source links, and adaptation details are in [ATTRIBUTION.md](public/ATTRIBUTION.md).

The female collection includes 38 reproductive meshes, but only 16 muscular-category pieces (12 eye muscles, bilateral rectus femoris and quadriceps tendons). It is not a comprehensive female muscle atlas. Eight placenta/umbilical pieces are available in the optional Pregnancy reference layer; defaults and the All preset exclude that layer.

This is an educational explorer, not a diagnostic or surgical tool.

## How it works

Geometry is merged into batches. Per-structure GPU textures control translation, visibility, and selection, while component geometry supports accurate picking. Exploded layouts pack only the visible pieces. Rendering updates when the scene changes; orbit controls remain responsive without thousands of separate draw calls.

The optional WebMCP tools expose anatomy search and inspection in compatible browsers. The visible interface works without them.

## Rebuilding geometry

The repository includes browser-ready geometry. Rebuilding it is optional: obtain the official BodyParts3D OBJ archive and English metadata tables, prepare the joined concepts and display-system mappings, run `scripts/convert-anatomy.py`, then `node scripts/optimize-anatomy.mjs` and `node scripts/compress-models.mjs`. Simplification uses a 0.2% relative error limit per structure.

Female browser-ready assets and `scripts/convert-female.py` were restored from upstream commit `d72b4f6db42e41a8db84b1c19ff6d86ee7b65284`. The converter takes the pinned HRA v1.5 GLB and a separate `SOURCE_PARTS.json` mapping keyed by node index. That source metadata-generation step is not included, so a full upstream female rebuild is not yet reproducible from this repository alone. The packaged assets require no conversion to run.

## Deploy

Import this repository into Vercel as a Vite project. The included `vercel.json` configures `npm ci`, `npm run build`, and the `dist` output directory. It can also be served by a static host.

## License

Original application code is released under the [MIT License](LICENSE). **The anatomy data has its own CC BY 4.0 license**; preserve the attribution when redistributing it. Third-party dependencies retain their respective licenses.

Issues and pull requests are welcome. Please include reproduction steps and browser/device details for interaction problems.
