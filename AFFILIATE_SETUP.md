# Combaine — Affiliate setup

How monetization works here: each suggested item shows up to **3 store buttons**,
chosen by the visitor's country. Right now they open the store's **search**. To
earn commission you only need to **enrol in the programs and paste your IDs** —
no other code change is required. Everything below lives in `index.html`
(`AFFILIATE_SETTINGS` and `BRANDS`).

## 1. Awin — one signup, most brands

Awin covers Mango, ASOS, Farfetch, Zalando, H&M, Dafiti and more (each program is
approved individually).

1. Create a **publisher** account at https://www.awin.com (there may be a small,
   usually refundable, signup deposit).
2. Copy your **Publisher ID** (a number) from the dashboard → paste into
   `AFFILIATE_SETTINGS.awinAffId`.
3. For each brand: Awin → **Advertisers** → search the brand → **Join programme**.
   After approval, open the advertiser and copy its **Advertiser ID (MID)** →
   paste into that brand's `awinMid` in `BRANDS`.
   - An advertiser can be region-specific (e.g. *Mango ES* and *Mango US* are
     different MIDs). Use the one that matches your audience.

When `awinAffId` **and** a brand's `awinMid` are both filled, the app
automatically wraps that brand's link in an Awin tracking deep link. No code edit.

## 2. Amazon Associates — for the Amazon button

1. Sign up at https://affiliate-program.amazon.com.
2. Copy your **tracking tag** (e.g. `combaine-20`) → paste into
   `AFFILIATE_SETTINGS.amazonTag`.

The app appends `?tag=...` to Amazon links automatically. (Amazon's *product API*
has extra rules — see the roadmap — but simple tagged links just need the tag.)

## 3. Brands on other networks

Some brands aren't on Awin (e.g. **Nordstrom** is usually CJ / Rakuten / Impact).
For those, either leave them as plain search links or extend `buildAffiliateUrl()`
with that network's deep-link format. **Zara/Inditex** has no affiliate program —
its link always stays a plain search.

## Checklist

Set `AFFILIATE_SETTINGS.awinAffId` once, then fill each brand you join:

| Brand     | Shown in (countries)   | Typical network        | Field to fill                 |
| --------- | ---------------------- | ---------------------- | ----------------------------- |
| Mango     | PT, ES, FR, IT, DE     | Awin                   | `BRANDS.mango.awinMid`        |
| Farfetch  | PT, ES, FR, IT, GB, BR | Awin                   | `BRANDS.farfetch.awinMid`     |
| Parfois   | PT                     | Awin (verify)          | `BRANDS.parfois.awinMid`      |
| ASOS      | DE, GB, US             | Awin                   | `BRANDS.asos.awinMid`         |
| H&M       | GB                     | Awin (region varies)   | `BRANDS.hm.awinMid`           |
| Zalando   | FR, IT, DE             | Awin                   | `BRANDS.zalando.awinMid`      |
| Dafiti    | BR                     | Awin (BR)              | `BRANDS.dafiti.awinMid`       |
| Amaro     | BR                     | verify                 | `BRANDS.amaro.awinMid`        |
| Renner    | (optional BR)          | verify                 | `BRANDS.renner.awinMid`       |
| Nordstrom | US                     | CJ / Rakuten           | extend `buildAffiliateUrl()`  |
| Amazon    | US                     | Amazon Associates      | `AFFILIATE_SETTINGS.amazonTag`|
| Zara      | ES (slot)              | none                   | — (no program)                |

To change which brands appear per country, edit `AFFILIATES_BY_COUNTRY` in
`index.html` (first 3 are shown).

## Roadmap — exact-product links (future)

Today the buttons open a store **search**. To point straight at the matching
product:

- On click (or on render), call the network's **Product Search API**
  (Awin / Sovrn / CJ) with the item's query, take the **1st result's product
  URL**, then wrap it with the network's **Link Builder API** for tracking.
- Keep API keys **server-side** — add a small `/api/product` serverless endpoint
  that does the lookup (never expose keys in the browser).
- **Cache** results and **fall back** to the search link when there's no match.

This is a bigger build (needs approved API access + a query→product matching
step), so it's planned for later. The current search-link + IDs approach already
earns from day one.
