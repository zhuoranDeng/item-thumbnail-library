---
name: thumbnail-loader
description: >-
  Thumbnail loader — fills placeholder thumbnails from the bundled
  item-thumbnail-library (`assets/` next to this skill) into projects and
  mocks. Uses product category hierarchy (Bodies, Clothing, Accessories,
  Backgrounds, Animations, Makeup) and RFY pages that mix child subcategory
  thumbs. Clothing→Shirts uses `shirts/`; Clothing→T-shirts uses `t-shirts/`;
  Clothing→Sweaters uses `sweaters/`. Makeup→Eyes uses `eyes/`; Makeup→Lips
  uses `lips/`. Backgrounds → `backgrounds/` — selecting a background tile
  updates the avatar preview panel background to match. Infers library folder
  and auto-applies; asks only when ambiguous. Tile ratio: 2:3 taller →
  avatars/ (normalize ~88% shared character height, object-contain + inset-2);
  1:1 → catalog (normalize ~90% on transparent PNG over Foundation Shift.200
  tile fill, object-contain + inset-2, clear of selection ring). Use when the
  user says Thumbnail loader, thumbnail-loader, load thumbnails, fill
  placeholders, RFY, backgrounds, makeup, or apply catalog assets; also when
  filling item cards, mock catalog images, or empty item PNGs.
  profile-photos/ = headshots; avatars/ = full-body characters. Marketplace home
  reference: references/marketplace-home.png (RFY 1:1 mix, Trending styles
  2:3 avatars/, Trending item 1:1 mix).
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

### RFY pages (hard rule)

| Page context | What to fill |
|--------------|--------------|
| Major-category RFY (e.g. Clothing RFY, Bodies RFY) | **Mix** assets from that major’s child subcategories across the grid |
| Subcategory page / filter (e.g. Jackets, Emotes) | Prefer assets matching that **one** subcategory |
| Unlabeled generic RFY | Ask, or mix only within the nearest clear major |

Do **not** fill an RFY grid with only one subcategory when the page is the major’s RFY.

### Marketplace home layout (image reference)

Canonical visual target: `references/marketplace-home.png` (repo root).

Match this composition when filling a marketplace **home / All** feed:

| Row | Header | Tile ratio | Library source |
|-----|--------|------------|----------------|
| 1 | Recommended for you | **1:1** | Mix catalog folders (clothing subtypes, accessories, etc.) — not avatars |
| 2 | Trending styles | **2:3** taller | `avatars/` only (full-body looks) |
| 3 | Trending item | **1:1** | Mix catalog folders again (accessories, clothing, …) |

Card chrome under each thumb (do not invent art): **title** (truncate with …) → **creator** (optional verified) → **Robux price**. Tile fill behind 1:1 / style media = Foundation **Shift.200**.

When the user says “match the reference”, “marketplace home”, or attaches this screenshot, prefer this row mapping over a generic RFY mix.

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
| Backgrounds | `backgrounds/` | Scene backdrops for avatar preview — **not** game thumbnails |
| Makeup → Eyes | `eyes/` | Eye makeup / eye-area cosmetics |
| Makeup → Lips | `lips/` | Lip makeup / lip cosmetics |
| Makeup → other subtypes | — | Faces, Eyelashes, Eyebrows — no folders yet |
| (UI) Profile headshots | `profile-photos/` | Not a catalog major above |
| (UI) Full-body avatar tiles | `avatars/` | **2:3** only; not a catalog RFY item type |
| (UI) Game / experience cards | `game-thumbnails/` | **Empty** — do not substitute `backgrounds/` |

When filling Clothing RFY: mix across Clothing subtype folders (`shirts/`, `t-shirts/`, `sweaters/`, `clothing/`, and future subtype folders).  
When filling Shirts page: use `shirts/` only.  
When filling T-shirts page: use `t-shirts/` only.  
When filling Sweaters page: use `sweaters/` only.  
When filling Bodies RFY: mix Full bodies + Hair + Heads (`fullbodies/` + `hair/` + `heads/`).  
When filling Accessories RFY: mix across `accessories/`.  
When filling Animations RFY: mix bundles + emotes from `animation/`.  
When filling Backgrounds RFY / page: use `backgrounds/`.  
When filling Makeup RFY: mix Makeup subtype folders (`eyes/`, `lips/`, and future faces/eyelashes/eyebrows).  
When filling Eyes page: use `eyes/` only.  
When filling Lips page: use `lips/` only.

### Backgrounds → avatar preview (hard rule)

Selecting a **background tile** must update the **avatar preview panel** background to the same asset (same image, or the full-res twin if the project keeps a separate preview file).

- Tile grid shows `backgrounds/` thumbs (1:1)
- On select / active state: set preview panel `background-image` (or equivalent) to that background
- Do not leave the preview on a default/unrelated backdrop when a background tile is selected
- Wire mocks so selected tile id ↔ preview background src stay in sync

## Tile aspect ratio (hard rule)

| Tile ratio | Category |
|------------|----------|
| **2:3** (taller portrait) | Always `avatars/` |
| **1:1** (square) | Catalog items (Bodies, Clothing, Accessories, Animations, Makeup, Backgrounds) and `profile-photos/` |

How to detect ratio:

- CSS / layout: `aspect-ratio: 2/3` or `2:3`, taller card frames, portrait avatar slots
- Explicit sizes (e.g. 200×300, 240×360) → avatars; square (e.g. 200×200, 420×420) → non-avatar
- Component names like `AvatarTile`, `AvatarCard` with portrait crop → avatars

**2:3 taller tile → use an avatar thumbnail.** Do not put square catalog items in 2:3 slots, and do not put avatars in 1:1 slots unless the user overrides.

### Thumbnail placement (hard rule — both ratios)

Library files are inconsistently framed. After every copy into a project, **normalize the asset** and **display with contain + light inset** so grids look organized, similarly sized, and clear of selection chrome.

#### Calibrated defaults (use these unless the user overrides)

| Slot | Canvas | Content fit | CSS display |
|------|--------|-------------|-------------|
| **2:3** `avatars/` | 840×1260 (or any 2:3) | Character height ~**88%** of canvas; shared top pad; center X | `object-contain object-center` + `inset-2` (~8px) |
| **1:1** catalog (clothing, bodies, accessories, animation, …) | 420×420 **transparent** PNG | Longer side ~**90%** of canvas; center X/Y | Tile fill = Foundation **Shift.200** dark (`rgba(208,217,251,0.08)`); image `object-contain object-center` + `inset-2` (~8px) |
| **1:1** **bleed** — `backgrounds/`, Makeup (`eyes/`, `lips/`, …) | 420×420 **edge-to-edge** (no content pad) | Content fills the canvas (cover/crop if needed) | `absolute inset-0 object-cover object-center` — **no** inset padding. Selection ring overlays the image. |

**Why these numbers:** ~80% content + `inset-3` looked too small for product thumbs; edge-flush / `object-cover` overlapped the **2px inset selection ring** for products. **90% + 8px inset** keeps presence without touching the stroke. **Backgrounds and makeup** are photographic / face-fill art — they must **fill the tile and the avatar preview pane** with no letterbox.

#### Display rules (CSS / component)

- Product / avatar thumbs: `object-contain object-center` + light inset — **never** `object-cover` on those.
- Backgrounds + makeup thumbs: `object-cover` + **zero inset** (`inset-0`).
- Avatar preview panel background: same asset as the selected background tile; CSS `absolute inset-0 h-full w-full object-cover` so it fills the pane edge-to-edge (no top/bottom letterbox).
- Inset (when used) must clear any inset selection ring (2px) — padding sits **inside** the ring, not under it.
- Avoid stacking heavy normalize **and** large CSS inset (double-shrink). If assets are already normalized to the table above, keep CSS inset light (`inset-2`).

#### Normalize from library originals

- Always normalize from the **canonical library file** (or a fresh copy), not from an already-shrunk project PNG (re-normalizing upscales and softens).
- Run normalize **after** `cp` into the project path; overwrite that path in place.
- Optional one-shot Python (Pillow) is fine.

#### 2:3 avatar normalize steps

1. RGBA; trim content bbox (alpha ≳ 8). Fully opaque black with no alpha edge → treat full frame as content.
2. Canvas **2:3** (e.g. 840×1260), transparent bg.
3. Scale so character height is ~**88%** of canvas height (same for every tile in the row).
4. Center X; top pad ≈ `(1 − 0.88) × 0.55` so feet/baselines align.
5. If width > ~92% of canvas, scale down further and keep vertical band centering.
6. Paste and save over the project placeholder.

#### 1:1 catalog normalize steps

1. RGBA; knock near-black luma ≲ 30 to **transparent** (do not bake black into the PNG).
2. Content bbox = skip alpha ≲ 8 (after knock-out).
3. Canvas **1:1** 420×420, **fully transparent** — never opaque black. The tile UI shows **Foundation Shift.200** dark (`rgba(208, 217, 251, 0.08)` / `#D0D9FB` @ 8%) behind the image. (Shift.300 = 12% is press/active, not the idle thumb fill.)
4. Scale so `max(contentW, contentH)` fits ~**90%** of canvas.
5. Center X/Y; save as transparent PNG over the project placeholder.

#### Quality check

- Full subject visible with a **small** clear gap from tile edge and selection outline
- Row/grid neighbors look **similarly sized** — not one edge-to-edge and one tiny in the middle
- **No** product/avatar pixels under or touching the active selection stroke
- Catalog item PNGs are **transparent** outside the product — no black plate; Shift.200 tile fill shows through
- Not sparse: if thumbs look “lost” in the frame, content fit is too low or CSS inset is too large — prefer the calibrated defaults above

## When to run

- User asks to load/fill/apply thumbnails
- Mock or UI work that has **placeholder** item images
- RFY / recommended / category browse grids
- Paths like `items/1.png` … `items/N.png`, empty/`placeholder`/`grey`/`dummy` image slots

**Scope:** Placeholder images only — not redesigning real product art the user already chose.

## Workflow

### 1. Locate placeholders

Search the project for thumbnail placeholders, e.g.:

- `public/**/items/*.png` (numbered stubs)
- `public/**/styles/*.png` when used as generic mocks
- Mock data: `image`, `thumbnail`, `src`, `thumb` pointing at stubs, `#`, empty string, or placeholder URLs
- UI using grey boxes / missing `src` for item cards
- RFY / category page sections

List each slot: **path or data key → major / subcategory (or RFY-mix) → library folder → confidence**.

### 2. Infer category

Use context around the slot (check **tile ratio first**, then hierarchy):

- **Tile ratio:** 2:3 taller → `avatars/`; 1:1 → catalog / profile-photos
- **Page type:** major RFY → mix child subcategories; subcategory page → that subtype only
- Section/label copy (“Clothing”, “Recommended for you”, “Jackets”, “Emotes”, “Hair”)
- Filename / folder / route (`sweaters`, `t-shirts`, `tshirts`, `shirts`, `lips`, `eyes`, `makeup`, `backgrounds`, `clothing`, `fullbodies`, `bodies`, `hair`, `heads`, `accessories`, `animation`, `rfy`)
- Component name (`ItemCard`, `ProfilePhoto`, `AvatarPreview`, `EmoteRow`, `RfyGrid`, `BackgroundTile`, `MakeupTile`)
- Crop style: full garment → clothing/shirts/t-shirts/sweaters; face-only catalog head → heads; profile headshot → profile-photos; full dressed character → avatars; grey posed rig → animation; scenic avatar-preview backdrop → backgrounds; experience/game card art → game-thumbnails (empty until uploaded); close-up eye cosmetics → eyes; close-up mouth cosmetics → lips
- Do not mix `profile-photos/` (headshots) with `avatars/` (full-body characters)
- For Backgrounds UI: selecting a tile **must** sync the avatar preview panel background (see hard rule above)

**Auto-apply** when confidence is high (clear major/subcategory, RFY-mix rule, folder, component, or **2:3 tile → avatar**).

**Ask before applying** when ambiguous (e.g. generic `items/1.png` with no category, or could be clothing vs accessories). Prefer Cursor **`AskQuestion`** if available; otherwise one short chat question. Do not block clear slots while waiting — apply clear ones first, ask only for ambiguous ones.

### 3. Pick assets

For each clear slot:

1. Load category file list from `manifest.json` (or list the folder).
2. Prefer unused assets in this pass (no duplicate thumbs in the same view unless supply runs out).
3. **RFY major page:** deliberately vary subcategories across slots (e.g. dress, jacket, shoes, pants — not eight jackets).
4. **Subcategory page:** match that subtype when filenames allow; else best-effort from the parent folder.
5. Match vibe when context exists (e.g. “dress” → dress file); else take next numbered file in category order.
6. **Copy** the PNG into the placeholder path (overwrite stub), **or** update mock data to a served path that points at a copied/symlinked file under the project’s static dir.
7. **If the slot is 2:3 / `avatars/`:** after copy from the library, run **Thumbnail placement** 2:3 normalize (~88% character height); CSS `object-contain object-center` + `inset-2`.
8. **If the slot is backgrounds or makeup (`eyes/`, `lips/`, …):** copy edge-to-edge (do **not** apply 90% catalog shrink). CSS `inset-0 object-cover`. For backgrounds, selecting a tile also sets the avatar preview panel background to the same asset, full-bleed `object-cover`.
9. **If the slot is other 1:1 catalog:** after copy from the library, run **Thumbnail placement** 1:1 normalize (~90% content on **transparent** 420×420); tile CSS background = Foundation **Shift.200**; image `object-contain` + `inset-2`. Never bake opaque black into the PNG. Never re-normalize an already-shrunk project file.

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

### 5. Report

Brief summary only:

- How many slots filled, by major / subcategory (note RFY mixes)
- Ambiguous slots skipped / waiting on answer
- Paths changed

## Asking rules

Ask **only** when:

- Category cannot be inferred
- Multiple majors could fit the same slot
- Overwriting would replace a non-placeholder (looks like real unique art) — confirm first
- Library folder is empty or missing (e.g. Makeup → Faces / Eyelashes / Eyebrows, or Game thumbnails → `game-thumbnails/`)
- Do **not** fill game-thumbnail slots from `backgrounds/` — those are different asset types

Do **not** ask for confirmation on clear category matches. Do **not** dump the full asset list unless the user asks.

## Do not

- Generate AI images instead of library files
- Move or delete the canonical library
- Commit binary churn unless the user asks to commit
- Apply accessories into profile-photo slots (or mix categories) when context is clear
- Use `avatars/` for headshot/profile slots — those belong in `profile-photos/`
- Put avatars in 1:1 tiles, or non-avatar assets in 2:3 taller tiles, unless the user overrides
- Use `object-cover` or zero-inset contain on **product/avatar** thumbs so the subject overlaps the selection outline (backgrounds/makeup **do** use cover + zero inset)
- Leave letterbox / padded backgrounds in the avatar preview panel — preview backdrop must be edge-to-edge `object-cover`
- Drop raw library thumbs into a multi-tile row/grid without normalizing to the calibrated defaults (2:3 ~88% height / 1:1 ~90% box)
- Normalize from an already-shrunk project PNG (upsample) instead of the library original
- Stack aggressive shrink (e.g. ≤80% content) with large CSS inset (≥12px) — thumbs look too small
- Bake opaque black (or any plate) into 1:1 catalog PNGs — use transparent PNGs over Foundation Shift.200 tile fill
- Fill a major RFY grid with only one subcategory when a mix is possible
- Leave the avatar preview panel on a mismatched backdrop after a background tile is selected
