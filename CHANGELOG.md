# Changelog

All notable changes to BES AirGap UI are recorded here for GitHub releases.

## 3.0.1 - 2026-06-11

### Changed
- Used the final `bfgather` URL segment for generated `AirgapResponse` filenames, falling back to a trimmed site name only when the URL segment is unavailable.

## 3.0.0 - 2026-06-11

### Changed
- Preserved each site's generated `AirgapResponse` with a site-specific filename for cache and download processing.

## 2.3.0 - 2026-06-11

### Changed
- Improved sitelist search so users can find entries without typing the full exact name.
- Added token-based fuzzy matching for split terms, prefixes, contained terms, and small typos.
- Example supported search: `DISA Windows 20202` can match `DISA STIGS Checklist for Windows 2022`.
- Changed the preview area to show all selected content options, even while the sitelist is filtered by search.
