# Halal Certifier Check

A static web app that compares halal certification bodies by how they slaughter poultry, goat/lamb and beef, and whether they allow stunning. Every answer links to the source it came from.

Countries covered:

| Country | Certifiers | Basis for the list |
|---|---|---|
| Australia | 19 | The 18 Approved Islamic Organisations on the Department of Agriculture's halal red-meat export list, plus AFIC |
| Canada | 7 | Certifiers active in Canada. Canada does not approve certifiers; CFIA only requires every halal claim to name one |

## Project layout

```
index.html            The whole app: markup, styles and script in one file
data/countries.json   Country menu: which countries have data, and which are planned
data/ca.json          Canada dataset
data/au.json          Australia dataset
netlify.toml          Netlify settings (static publish, cache headers)
```

There is no build step and no dependencies.

## Run locally

The page loads its data with `fetch`, which browsers block for pages opened straight from disk. Serve the folder instead:

```sh
npx serve .
# or
python3 -m http.server 8000
```

Then open the address the command prints.

## Deploy to Netlify

1. In Netlify, choose **Add new site → Import an existing project** and pick this repository.
2. Leave the build command empty. The publish directory is the repository root (already set in `netlify.toml`).
3. Deploy. Pull requests get deploy previews automatically.

## Adding a country

1. Create `data/<code>.json` using the schema below, with `meta.country` set to the two-letter code (for example `"GB"`).
2. Add an entry to `countries` in `data/countries.json`:
   ```json
   { "code": "GB", "name": "United Kingdom", "file": "gb.json", "count": 12 }
   ```
   and remove the code from `planned` if it is listed there.

To preview a dataset before committing it, use **Load data file** in the app. The file stays in your browser only.

## Data schema (v1.1.0)

Each country file has a `meta` object and a `certifiers` array.

```jsonc
{
  "meta": {
    "schema_version": "1.1.0",
    "country": "CA",
    "last_verified": "2026-11-18",
    "license": "CC-BY-4.0",
    "about_government_approval": "…",   // shown in the footer
    "red_meat_note": "…",               // optional, shown in the footer
    "poultry_note": "…",                // optional, shown in the footer
    "disclaimer": "…"
  },
  "certifiers": [
    {
      "id": "hma",
      "name": "Halal Monitoring Authority",
      "acronym": "HMA",
      "headquarters": "Scarborough, ON",
      "website": "https://hmacanada.org/",
      "government_recognition": [
        { "authority": "…", "authority_country": "CA", "source_url": "…" }
      ],
      "poultry": { /* species block */ },
      "goat":    { /* species block */ },
      "cow":     { /* species block */ },
      "contact": { "email": "…", "contact_form_url": "…", "phone": "…" },
      "logo_url": "…",
      "notes": "…"
    }
  ]
}
```

A species block:

```jsonc
{
  "hand_slaughter_required": "yes | no | unknown",
  "basis": "certifier_policy | certifier_statement_in_press | plant_document | government_rule | industry_practice | none",
  "stunning": {
    "status": "not_allowed | post_cut_only | likely_not_allowed | conditional | reversible_allowed | allowed | unknown",
    "details": "Plain-language explanation"
  },
  "evidence": [
    { "title": "…", "url": "…", "publisher": "…", "type": "official | government | news | third_party", "excerpt": "Short paraphrase or quote" }
  ]
}
```

`unknown` means no reliable public source was found. It does not mean the practice is absent.

## Licensing

- **Code** (`index.html` and other source files): MIT. See [LICENSE](LICENSE).
- **Data** (`data/*.json`): Creative Commons Attribution 4.0 International (CC BY 4.0). See [LICENSE-DATA](LICENSE-DATA).
- **Certifier names and logos** are trademarks of their owners. The app links to logos hosted on the certifiers' own sites; they are not stored in or licensed by this repository.
- **Linked sources and short excerpts** belong to their respective owners and are included for reference and attribution.

## Accuracy

The data was compiled from public sources on the `last_verified` date in each file. Certifier policies change, and many publish no written slaughter standard. Confirm with the certifier before relying on any entry. Corrections are welcome as issues or pull requests; please include a source link.
