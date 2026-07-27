---
name: thumbnail-loader
description: >-
  Thumbnail loader — fills placeholder thumbnails from the bundled
  item-thumbnail-library (`assets/` next to this skill) into projects and
  mocks. Smart-loads by UI context: marketplace assets, avatars, profile
  photos, backgrounds, makeup, animations, and game-thumbnails/ (default
  **16:9**, or **1:1** when the user asks — never substitute backgrounds/).
  Placement: 2:3 → avatars/ contain+inset; 1:1 products → ~90% transparent +
  Shift.200 + contain+inset; bleed (backgrounds + makeup + game thumbs) →
  edge-to-edge object-cover, no inset; selecting a background fills the avatar
  preview pane edge-to-edge. Infers folder and auto-applies; asks only when
  ambiguous or folder empty. Use when the user says Thumbnail loader,
  thumbnail-loader, load thumbnails, fill placeholders, RFY, backgrounds,
  makeup, game thumbnails, or apply catalog assets.
---

# Thumbnail loader

Fill **placeholder** thumbnail images in the current project using the bundled asset library. Do not invent images — only use library files.

## Library (canonical)

Resolve the **library root** (folder that contains `manifest.json` + category dirs) in this order (first match wins):

1. **`assets/` next to this skill’s repo** — when this file lives at `skills/thumbnail-loader/SKILL.md`, use `<repo>/assets/` if it contains `manifest.json`
2. **`ITEM_THUMBNAIL_LIBRARY`** env var, if set:
   - If that path has `manifest.json`, use it
   - Else if `<path>/assets/manifest.json` exists, use `<path>/assets/`
3. Fallback: `~/Documents/item-thumbnail-library/assets/`, then `~/Documents/item-thumbnail-library/`

The library root contains `manifest.json` and category folders (`shirts/`, `sweaters/`, `avatars/`, …). Manifest `relativePath` values are relative to this root (e.g. `sweaters/01-….png`).

Read `manifest.json` for ids, categories, and `relativePath`.

## Product category hierarchy

Major categories each have an **RFY** (Recommended For You) page whose grid mixes thumbnails from that major’s **child subcategories** (not a single subtype).

```
Bodies
  - Full bodies
  - Hair
  - Heads
Clothing
  - Shirts
  - T-shirts
  - Sweaters
  - Jackets
  - Pants
  - Dresses & Skirts
  - Bodysuits
  - Shorts
  - Shoes
Accessories
  - Head
  - Face
  - Neck
  - Shoulder
  - Front
  - Back
  - Waist
  - Gear
Backgrounds
Animations
  - Bundles
  - Emotes
Makeup
  - Eyes
  - Lips
  - Faces
  - Eyelashes
  - Eyebrows
```

UI-only (not majors above): `profile-photos/`, `avatars/`, `game-thumbnails/`.

### RFY pages (hard rule)

| Page context | What to fill |
|--------------|--------------|
| Major-category RFY (e.g. Clothing RFY, Bodies RFY) | **Random mix** of that major’s child subcategory assets across the grid |
| Subcategory page / filter (e.g. Jackets, Emotes) | Prefer assets matching that **one** subcategory |
| Unlabeled generic RFY | Ask, or mix only within the nearest clear major |

Do **not** fill an RFY grid with only one subcategory when the page is the major’s RFY.

**Random mix (not patterned):** Pool eligible subcategory assets, **dedupe by visual** (same PNG path, or near-identical A/B frames of the same subject), then shuffle. Do **not** round-robin or interleave in a fixed pattern (e.g. body → hair → head → body → hair → head). Do **not** inflate the grid by repeating the same catalog across every subcategory slot.

**Duplicates when supply is short:** Prefer **unique visuals first**. Only reuse when there are more slots than unique assets. When reusing, space duplicates by at least a full row when possible (~6 tiles in a 6-col grid; never adjacent). Near-identical assets (e.g. two sleeping-kitten frames) count as the **same visual** for dedupe and spacing.

### Map hierarchy → library folders

| Product category | Library folder | Notes |
|------------------|----------------|-------|
| Bodies → Full bodies | `fullbodies/` | Full-body / character bundles |
| Bodies → Hair | `hair/` | |
| Bodies → Heads | `heads/` | Dynamic heads (catalog item, not profile) |
| Clothing → Shirts | `shirts/` | Dedicated shirts subcategory |
| Clothing → T-shirts | `t-shirts/` | Dedicated t-shirts subcategory |
| Clothing → Sweaters | `sweaters/` | Sweaters / hoodies |
| Clothing → other subtypes | `clothing/` | Legacy mixed clothing until split into subtype folders |
| Accessories → * (all slots) | `accessories/` | Head/Face/Neck/…/Gear |
| Animations → Bundles, Emotes | `animation/` | Grey rig poses |
| Backgrounds | `backgrounds/` | Avatar-preview environments (bleed media) |
| Makeup → Eyes | `eyes/` | Eye cosmetics (bleed media) |
| Makeup → Lips | `lips/` | Lip cosmetics (bleed media) |
| Makeup → other subtypes | — | Faces, Eyelashes, Eyebrows — no folders yet |
| (UI) Profile headshots | `profile-photos/` | Not a catalog major above |
| (UI) Full-body avatar tiles | `avatars/` | **2:3** only; not a catalog RFY item type |
| (UI) Game / experience cards | `game-thumbnails/` | Default **16:9**; **1:1** only when user asks |

When filling Clothing RFY: mix across Clothing subtype folders (`shirts/`, `t-shirts/`, `sweaters/`, `clothing/`, and future subtype folders).  
When filling Shirts page: use `shirts/` only.  
When filling T-shirts page: use `t-shirts/` only.  
When filling Sweaters page: use `sweaters/` only.  
When filling Bodies RFY: mix Full bodies + Hair + Heads (`fullbodies/` + `hair/` + `heads/`).  
When filling Accessories RFY: mix across `accessories/`.  
When filling Animations RFY: mix bundles + emotes from `animation/`.  
When filling Backgrounds RFY / page: use `backgrounds/` (bleed + preview sync).  
When filling Makeup RFY: mix Makeup subtype folders (`eyes/`, `lips/`, and future faces/eyelashes/eyebrows) — bleed.  
When filling Eyes page: use `eyes/` only.  
When filling Lips page: use `lips/` only.  
When filling game / experience cards: use `game-thumbnails/` only (never `backgrounds/`).

### Game thumbnails ≠ backgrounds (hard rule)

These are **different product types**. Do not conflate them.

| | **Backgrounds** (`backgrounds/`) | **Game thumbnails** (`game-thumbnails/`) |
|--|----------------------------------|------------------------------------------|
| Purpose | Environment behind the avatar in the **customize / try-on preview** | Experience / game **discovery cards** (Charts, Home, continue playing) |
| UI cues | Category “Backgrounds”, avatar preview pane backdrop, “scene”, “skybox” | “Games”, “Experiences”, game card / tile, experience name + player count |
| Aspect | Usually **1:1** square tiles | **Default 16:9** landscape; use **1:1** only when the user explicitly asks |
| Placement | **Bleed** — edge-to-edge cover; selection updates preview backdrop | **Bleed** — edge-to-edge cover in the tile (no product inset / no 90% shrink) |
| Library | `backgrounds/` | `game-thumbnails/` (assets present) |

**Aspect rule:** Prefer **16:9** for game thumbnails. Switch to **1:1** only if the user says 1:1 / square / “crop square”. When serving 1:1, center-crop the 16:9 master (cover) — do not letterbox. Never fill game-thumbnail slots from `backgrounds/`.

Masters live in `game-thumbnails/` as **16:9** (e.g. 1280×720). Do not store a separate 1:1 library copy unless the user asks to generate one.

### Bleed media + avatar preview (hard rule)

**Bleed categories (ALWAYS edge-to-edge):** `backgrounds/`, Makeup (`eyes/`, `lips/`, …), and **`game-thumbnails/`**.

These tiles must **always fill the entire thumbnail chrome** — no inner padding, no inset, no letterbox, no 90% product shrink, no Shift.200 “frame” around the art. This is mandatory every time these categories are loaded.

| Surface | Rule |
|---------|------|
| Bleed **tile** | PNG edge-to-edge; CSS `absolute inset-0 h-full w-full object-cover object-center` (optional tiny `scale-[1.02]` to kill subpixel gaps). **Never** `inset-2`, `p-*`, or `object-contain` on these. Selection ring overlays the image. |
| Product / avatar **tile** | Still contain + inset (see placement table) — do **not** apply bleed rules there. |
| **Avatar preview backdrop** | Only when the user selects a **background** tile: set the preview pane’s background to that **same** asset (or its full-res twin). CSS `absolute inset-0 h-full w-full object-cover object-center` so the scene fills the pane with **no** top/bottom/side gaps. |

When copying bleed assets into a project: do **not** run product 1:1 normalize (~90%). Keep masters edge-filled; for games keep **16:9** masters (center-crop to **1:1** only if the user asked).

Background select checklist:

1. Tile grid uses `backgrounds/` (bleed display — always full-bleed).
2. On select / active: preview `src` / `background-image` = that background.
3. Preview stays in sync with the selected tile id — never leave a default/unrelated backdrop while a background tile is active.
4. Do not use makeup or game thumbs as the avatar preview environment.

## Tile aspect ratio (hard rule)

| Tile ratio | Category |
|------------|----------|
| **2:3** (taller portrait) | Always `avatars/` |
| **16:9** (landscape) | Default for `game-thumbnails/` |
| **1:1** (square) | Catalog items (Bodies, Clothing, Accessories, Animations, Makeup, Backgrounds), `profile-photos/`, and `game-thumbnails/` **only when user asks for 1:1** |

How to detect ratio:

- CSS / layout: `aspect-ratio: 2/3` or `2:3`, taller card frames, portrait avatar slots
- Explicit sizes (e.g. 200×300, 240×360) → avatars; square (e.g. 200×200, 420×420) → non-avatar
- Component names like `AvatarTile`, `AvatarCard` with portrait crop → avatars

**2:3 taller tile → use an avatar thumbnail.** Do not put square catalog items in 2:3 slots, and do not put avatars in 1:1 slots unless the user overrides.

### Thumbnail placement (hard rule)

Pick the **placement mode** from category first, then normalize + CSS accordingly.

#### Calibrated defaults

| Mode | Categories | Canvas | Content fit | CSS display |
|------|------------|--------|-------------|-------------|
| **Avatar** | `avatars/` | 840×1260 (or any 2:3) | Character height ~**88%**; shared top pad; center X | `object-contain object-center` + `inset-2` (~8px) |
| **Product** | Clothing, Bodies, Accessories, Animations, `profile-photos/`, … | 420×420 **transparent** PNG | Longer side ~**90%**; center X/Y | Tile fill = Foundation **Shift.200** (`rgba(208,217,251,0.08)`); img `object-contain` + `inset-2` |
| **Bleed** | `backgrounds/`, Makeup (`eyes/`, `lips/`, …), `game-thumbnails/` — **ALWAYS** | Edge-to-edge (16:9 masters for games; 1:1 when asked) | Fill canvas — **no** 90% shrink, **no** inner pad | `inset-0 object-cover` only — **never** contain/inset/p-* |

**Why:** Product thumbs need a gap from the 2px selection ring (~90% + 8px inset). Bleed art must read as a continuous photo — padding or contain letterboxing looks broken, especially when the same file drives the avatar preview backdrop.

#### Display rules (CSS / component)

- **Product / avatar:** `object-contain` + light inset inside the selection ring. Never `object-cover` on these.
- **Bleed tiles:** `object-cover` + zero inset. Ring draws on top.
- **Preview backdrop:** full-pane `object-cover` (see bleed hard rule). Never leave transparent / Shift.200 bands at the top or bottom of the preview.
- Do not stack heavy normalize **and** large CSS inset on product thumbs.

#### Normalize from library originals

- Always normalize from the **canonical library file** (or a fresh copy), not from an already-shrunk project PNG.
- Run normalize **after** `cp` into the project path; overwrite that path in place.
- Optional one-shot Python (Pillow) is fine.

#### 2:3 avatar normalize steps

1. RGBA; trim content bbox (alpha ≳ 8). Fully opaque black with no alpha edge → treat full frame as content.
2. Canvas **2:3** (e.g. 840×1260), transparent bg.
3. Scale so character height is ~**88%** of canvas height (same for every tile in the row).
4. Center X; top pad ≈ `(1 − 0.88) × 0.55` so feet/baselines align.
5. If width > ~92% of canvas, scale down further and keep vertical band centering.
6. Paste and save over the project placeholder.

#### 1:1 product normalize steps

1. RGBA; knock near-black luma ≲ 30 to **transparent** (do not bake black into the PNG).
2. Content bbox = skip alpha ≲ 8 (after knock-out).
3. Canvas **1:1** 420×420, **fully transparent**. Tile UI shows **Shift.200** behind the image. (Shift.300 = 12% is press/active only.)
4. Scale so `max(contentW, contentH)` fits ~**90%** of canvas.
5. Center X/Y; save as transparent PNG over the project placeholder.

#### 1:1 bleed normalize steps

1. Start from the library original (already near edge-to-edge).
2. **Do not** apply the 90% product shrink or knock-out-to-transparent plate.
3. Scale with **cover** into 420×420 (or the project’s square size); center-crop. Keep the frame filled.
4. Prefer opaque (or fully filled) pixels at the edges so CSS `object-cover` in the preview cannot show letterbox bands.
5. Save over the project placeholder.

#### Quality check

- **Product / avatar:** full subject visible with a **small** gap from tile edge and selection outline; neighbors similarly sized
- **Bleed tiles:** image touches all four sides of the thumb; no Shift.200 “frame” around the photo
- **Preview backdrop:** selected background reaches every edge of the preview pane (no top/bottom bars)
- No product/avatar pixels under or touching the active selection stroke
- Not sparse: if product thumbs look “lost”, content fit is too low or CSS inset too large — prefer calibrated defaults
- Catalog **product** PNGs are transparent outside the product; bleed PNGs are not product-style transparent plates

## When to run

- User asks to load/fill/apply thumbnails
- Mock or UI work that has **placeholder** item images
- RFY / recommended / category browse grids
- Paths like `items/1.png` … `items/N.png`, empty/`placeholder`/`grey`/`dummy` image slots

**Scope:** Placeholder images only — not redesigning real product art the user already chose.

## Workflow

### 1. Find targets

Search the project for thumbnail placeholders, e.g.:

- `public/**/items/*.png` (numbered stubs)
- `public/**/styles/*.png` when used as generic mocks
- Mock data: `image`, `thumbnail`, `src`, `thumb` pointing at stubs, `#`, empty string, or placeholder URLs
- UI using grey boxes / missing `src` for item cards
- RFY / category page sections

List each slot: **path or data key → major / subcategory (or RFY-mix) → library folder → placement mode (avatar / product / bleed) → confidence**.

### 2. Infer category

Use context around the slot (check **tile ratio first**, then hierarchy):

- **Tile ratio:** 2:3 taller → `avatars/`; **16:9** (or game/experience card) → `game-thumbnails/`; 1:1 → catalog / profile-photos (or game thumbs **only if user asked for 1:1**)
- **Page type:** major RFY → mix child subcategories; subcategory page → that subtype only
- Section/label copy (“Clothing”, “Recommended for you”, “Jackets”, “Emotes”, “Hair”, “Backgrounds”, “Games”)
- Filename / folder / route (`sweaters`, `t-shirts`, `shirts`, `lips`, `eyes`, `makeup`, `backgrounds`, `game-thumbnails`, `games`, `experiences`, `clothing`, `fullbodies`, `hair`, `heads`, `accessories`, `animation`, `rfy`)
- Component name (`ItemCard`, `ProfilePhoto`, `AvatarPreview`, `EmoteRow`, `RfyGrid`, `BackgroundTile`, `MakeupTile`, `GameCard`)
- Crop style: full garment → clothing subtypes; face-only catalog head → heads; profile headshot → profile-photos; full dressed character → avatars; grey posed rig → animation; avatar-preview environment → backgrounds; experience/game card → game-thumbnails; close-up eye/lip cosmetics → eyes/lips
- Do not mix `profile-photos/` with `avatars/`, or `backgrounds/` with `game-thumbnails/`
- Background tile select → sync preview backdrop (bleed hard rule)

**Auto-apply** when confidence is high.

**Ask before applying** when ambiguous. Prefer Cursor **`AskQuestion`** if available; otherwise one short chat question. Apply clear slots first; ask only for ambiguous / empty-folder ones.

### 3. Pick assets

For each clear slot:

1. Load category file list from `manifest.json` (or list the folder). If count is 0 (e.g. `game-thumbnails/`), skip and ask — do not substitute another folder.
2. Build the fill list from **unique visuals** first (dedupe by path; collapse near-identical A/B frames). Only then reuse if slots still remain.
3. **Major RFY / mixed category:** pool child-subcategory assets → **dedupe** → **shuffle randomly** (no round-robin / fixed subtype cadence, no cloning the same 20 assets into every slot). Apply duplicate spacing (~full row / 6+ tiles; never adjacent if avoidable).
4. **Subcategory page:** draw from that subtype only; still dedupe visuals; space any reuse.
5. Match vibe when context exists; else any shuffled pick is fine.
6. **Copy** the PNG into the placeholder path (overwrite stub), **or** update mock data to a served path under the project’s static dir.
7. **Avatar (2:3):** 2:3 normalize (~88% height); CSS contain + `inset-2`.
8. **Bleed (backgrounds / makeup / game-thumbnails):** bleed normalize (edge-to-edge); CSS `inset-0 object-cover`. Games default **16:9**; crop to **1:1** only if user asked. If background: also wire preview pane to the same asset, full-bleed cover.
9. **Product (other 1:1):** 1:1 product normalize (~90% transparent); Shift.200 tile fill; CSS contain + `inset-2`. Never re-normalize an already-shrunk project file.

Prefer **copy into the project’s public/static placeholder path** so the app keeps working without depending on the library path at runtime.

Example (replace `$LIB` with the resolved library root):

```bash
cp "$LIB/clothing/01-black-polka-dot-dress.png" public/design/items/1.png
```

If the project expects a catalog tree, copy or symlink the needed category folder once:

```bash
mkdir -p public/thumbnails
cp "$LIB/clothing/01-….png" public/thumbnails/…
# or: ln -s "$LIB" public/thumbnails
```

Then point mock `src` at `/thumbnails/clothing/01-….png`.

### 4. Wire references

If placeholders are referenced by number in code (`/design/items/1.png`), overwriting those files is enough.

If mock data lists titles/categories, align `image` paths with the files you wrote and keep names consistent.

For background tiles, confirm select → preview backdrop wiring and full-bleed CSS on both tile and pane.

### 5. Report

Brief summary only:

- How many slots filled, by major / subcategory (note RFY mixes and bleed vs product)
- Empty folders skipped (e.g. game thumbnails) / ambiguous slots waiting
- Paths changed; preview-backdrop sync if backgrounds were filled

## Asking rules

Ask **only** when:

- Category cannot be inferred
- Multiple majors could fit the same slot (especially **backgrounds vs game thumbnails**)
- Overwriting would replace a non-placeholder (looks like real unique art) — confirm first
- Library folder is empty or missing (Makeup → Faces / Eyelashes / Eyebrows)

Do **not** ask for confirmation on clear category matches. Do **not** dump the full asset list unless the user asks.

## Do not

- Generate AI images instead of library files
- Move or delete the canonical library
- Commit binary churn unless the user asks to commit
- Apply accessories into profile-photo slots (or mix categories) when context is clear
- Use `avatars/` for headshot/profile slots — those belong in `profile-photos/`
- Put avatars in 1:1 tiles, or non-avatar assets in 2:3 taller tiles, unless the user overrides
- Fill **game-thumbnail** slots from `backgrounds/` (or any other folder)
- Serve game thumbnails as **1:1** unless the user asked for square / 1:1
- Apply **product** placement (90% shrink / contain / inset) to **bleed** categories, or bleed cover to product/avatar thumbs
- Leave letterbox / padded bands on bleed tiles or on the avatar preview backdrop
- Drop raw library thumbs into a product grid without the calibrated product/avatar normalize
- Normalize from an already-shrunk project PNG (upsample) instead of the library original
- Stack aggressive shrink (e.g. ≤80% content) with large CSS inset (≥12px) on product thumbs
- Bake opaque black plates into **product** 1:1 PNGs — use transparent PNGs over Foundation Shift.200
- Fill a major RFY grid with only one subcategory when a mix is possible
- Pattern-interleave mixed RFY grids (round-robin subtypes) — shuffle instead
- Place duplicate or near-identical thumbnails adjacent (or closer than ~a full row) when another unique visual can separate them
- Inflate mixed RFY grids by repeating the same asset set once per subcategory slot
- Leave the avatar preview panel on a mismatched backdrop after a background tile is selected
- Leave inner padding / letterbox on backgrounds or makeup thumbs (bleed must be edge-to-edge)
