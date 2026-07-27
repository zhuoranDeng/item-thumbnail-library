# Item thumbnail library + Thumbnail loader

Portable catalog PNG thumbnails and a Cursor skill that fills placeholder item tiles in any project.

Clone once → install the skill → use **Thumbnail loader** in Cursor. Assets ship in the same repo.

## Install

```bash
git clone https://github.com/zhuoranDeng/item-thumbnail-library.git ~/Documents/item-thumbnail-library
mkdir -p ~/.cursor/skills
ln -sfn ~/Documents/item-thumbnail-library/skills/thumbnail-loader ~/.cursor/skills/thumbnail-loader
```

Clone anywhere else if you prefer; keep the skill symlink pointed at that clone’s `skills/thumbnail-loader`. Optionally set:

```bash
export ITEM_THUMBNAIL_LIBRARY=/path/to/item-thumbnail-library
# or point directly at the assets folder:
# export ITEM_THUMBNAIL_LIBRARY=/path/to/item-thumbnail-library/assets
```

Restart Cursor (or reload the window) so the skill appears.

## Repo layout

```
item-thumbnail-library/
├── README.md
├── assets/                 ← all PNGs + manifest (library root)
│   ├── manifest.json
│   ├── shirts/
│   ├── sweaters/
│   ├── avatars/
│   └── …
├── skills/
│   └── thumbnail-loader/
│       └── SKILL.md
└── references/
    └── marketplace-home.png
```

| Path | Purpose |
|------|---------|
| `assets/` | Catalog PNGs by category + `manifest.json` |
| `skills/thumbnail-loader/SKILL.md` | Cursor **Thumbnail loader** skill |
| `references/marketplace-home.png` | Visual target for marketplace home rows |

## Marketplace home reference

`references/marketplace-home.png` is the layout target when filling a marketplace home feed:

![Marketplace home — Recommended for you, Trending styles, Trending item](references/marketplace-home.png)

1. **Recommended for you** — 1:1 mixed catalog items  
2. **Trending styles** — 2:3 full-body looks from `avatars/`  
3. **Trending item** — 1:1 mixed catalog items  

## Product hierarchy (for RFY / browse)

Paths below are relative to **`assets/`**. Major categories each have an RFY page that **mixes** child subcategory thumbnails:

- **Bodies** — Full bodies, Hair, Heads → `fullbodies/`, `hair/`, `heads/`
- **Clothing** — Shirts, T-shirts, Sweaters, Jackets, Pants, Dresses & Skirts, Bodysuits, Shorts, Shoes
  - Shirts → `shirts/`
  - T-shirts → `t-shirts/`
  - Sweaters → `sweaters/`
  - Other / legacy mixed → `clothing/` (until more subtype folders exist)
- **Accessories** — Head, Face, Neck, Shoulder, Front, Back, Waist, Gear → `accessories/`
- **Backgrounds** → `backgrounds/` — selecting a tile updates the avatar preview panel background
- **Animations** — Bundles, Emotes → `animation/`
- **Makeup** — Eyes, Lips, Faces, Eyelashes, Eyebrows
  - Eyes → `eyes/`
  - Lips → `lips/`
  - Faces / Eyelashes / Eyebrows — (no library folders yet)

Also: `profile-photos/` (headshots), `avatars/` (2:3 full-body character tiles).

## Folders (under `assets/`)

| Folder | Use | Count |
|--------|-----|-------|
| `fullbodies/` | Full bodies / character bundles (Bodies subcategory) | 17 |
| `hair/` | Hair and hair+hat sets (Bodies subcategory) | 20 |
| `heads/` | Dynamic heads / faces (Bodies subcategory) | 20 |
| `shirts/` | Shirts (Clothing subcategory) | 14 |
| `t-shirts/` | T-shirts (Clothing subcategory) | 17 |
| `sweaters/` | Sweaters / hoodies (Clothing subcategory) | 20 |
| `clothing/` | Mixed clothing (legacy / other subtypes) | 20 |
| `backgrounds/` | Avatar preview scene backgrounds | 13 |
| `eyes/` | Eyes makeup (Makeup subcategory) | 17 |
| `lips/` | Lips makeup (Makeup subcategory) | 18 |
| `profile-photos/` | Profile photos / headshots | 11 |
| `avatars/` | Full-body avatar characters (**2:3** taller tiles) | 28 |
| `accessories/` | Hats, bags, jewelry, pets, props | 20 |
| `animation/` | Emotes, animation packs | 20 |

## Tile ratios

| Ratio | Assets |
|-------|--------|
| **2:3** (taller) | `avatars/` only |
| **1:1** (square) | All other folders |

## Naming

```
01-short-description.png
```

Prefer **~420×420** PNGs for 1:1 catalog items. Avatar assets are full-body and meant for **2:3** taller tiles.

## Use assets without the skill

```bash
LIB=~/Documents/item-thumbnail-library/assets   # or your clone’s assets/
cp "$LIB/clothing/01-black-polka-dot-dress.png" /path/to/project/public/design/items/1.png
# or symlink the assets folder:
ln -s "$LIB" /path/to/project/public/thumbnails
```

## Cursor skill behavior

Say **Thumbnail loader** (or “fill placeholders”, “load thumbnails”) in Cursor. The skill:

- Infers category from UI context; asks only when ambiguous
- Major RFY → mixes child subcategory thumbs; subcategory page → one folder
- **2:3** tiles → `avatars/`; **1:1** → other categories
- Copies library PNGs into project placeholders (does not invent images)

See `skills/thumbnail-loader/SKILL.md` for full rules.
