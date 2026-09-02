# YSL Slim Jeans — Product Page

A static recreation of the Saint Laurent product page for the
[Slim Jeans in Carbon Black Denim](https://www.ysl.com/en-gb/pr/slim-jeans-in-carbon-black-denim-800332Y16PG1290.html)
(product code `800332Y16PG1290`).

## Running

No build step — it is a single self-contained page:

```sh
open index.html          # macOS
# or serve it:
npx http-server .
```

## Product data sources

`ysl.com` blocks datacenter traffic, so product data was cross-referenced from
retailers stocking the exact SKU:

- Name, fit, composition, sizes, GBP price (£620): The Fashion Square (UK)
- Details, features, product photos: Gaudenzi Boutique (product `800332Y16PG1290`)

Product photography is hot-linked from Gaudenzi Boutique's CDN and remains the
property of its owner; it is used here for demonstration only.

## Notes

- Plain HTML/CSS/JS, no dependencies.
- Responsive (single-column below 900px), keyboard-accessible size selector and
  accordions, `aria-live` feedback on the add-to-bag action.
