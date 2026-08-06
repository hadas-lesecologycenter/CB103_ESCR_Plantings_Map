# CB103_ESCR_Plantings_Map
A map of the trees planted in Manhattan's CB 103 in 2019-2024 to compensate for trees lost due to ESCR.

## Maps in this repo

All maps are single self-contained HTML files under `docs/`, served by GitHub Pages.

| File | What it shows |
|---|---|
| `docs/index.html` | NYC street-tree age by neighborhood, with a live all-trees pull |
| `docs/street-trees.html` | Street-tree-only age map |
| `docs/tract-map.html` | CB3 census-tract choropleth |
| `docs/cb3-street-trees.html` | CB3 street/all-trees toggle |
| `docs/thrift-map.html` | Vintage, thrift and secondhand shops in the LES & East Village, with walking routes (single self-contained file) |

The thrift map reads its shops from a Google Sheet (falling back to `docs/shops.csv`).
Setup and upkeep: **[`docs/THRIFT_MAP_SETUP.md`](docs/THRIFT_MAP_SETUP.md)**.
