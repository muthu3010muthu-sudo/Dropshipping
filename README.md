# Mech DesignHub — Premium Dropshipping Storefront (Frontend-Only)

A fully static, frontend-only e-commerce storefront. No backend, no database, no login,
no payment gateway. Customers browse, generate a PDF order sheet, and send it to your
Instagram account for manual confirmation.

## How to run it

This is a plain static site — no build step required.

**Easiest:** double-click `index.html` to open it in your browser.
Note: a few features (live search suggestions across pages, wishlist syncing) work
best when served over `http://` rather than opened directly as a `file://` path,
because some browsers restrict local storage/scripts differently for local files.
If anything looks off when double-clicking, run a tiny local server instead:

```bash
# from inside this folder
python3 -m http.server 8080
# then open http://localhost:8080 in your browser
```

Or upload the whole folder to any static host (Netlify, Vercel, GitHub Pages,
Cloudflare Pages, or your own hosting) — it will work as-is.

**Internet connection required:** Google Fonts, Font Awesome icons, AOS, GSAP,
Swiper, and jsPDF are loaded from CDNs, so the browser needs an internet connection
the first time each page loads.

## File structure

```
index.html              Home page
products.html            Full catalog with filters/search/sort
product-details.html     Single product page (?id=MDH-xxxx)
categories.html          All categories
about.html / contact.html / faq.html
privacy.html / terms.html
404.html
order-success.html       PDF + Instagram confirmation screen
order-success.html reads the last order from localStorage ("mdh_last_order")

css/style.css             All styling (design system, components, responsive rules)
js/products-data.js       156 generated demo products (edit or regenerate this)
js/main.js                Navbar, search, wishlist, toasts, scroll effects, counters
js/render.js              Builds product cards + populates home/listing/details pages
js/order-form.js          "Buy Now" modal, validation, order ID/invoice generation
js/pdf-generator.js       Builds the downloadable order-sheet PDF with jsPDF
```

## Regenerating or expanding the product catalog

`tools/generate_products.py` is the script that produced the 156 demo products
in `js/products-data.js`. It's plain Python with no dependencies.

```bash
cd tools
python3 generate_products.py
```

It writes a fresh `js/products-data.js`. To add more variety, open the script
and:
- add more entries to any category's `"items"` list (product type names),
- add more brand names to a category's `"brands"` list,
- add a brand-new category by copying an existing block in the `CATEGORIES`
  dict at the top of the file.

Re-run the script any time after editing — it's deterministic (seeded) so
re-runs without edits produce the same catalog.

`tools/build_*.py` are the templating scripts used to assemble the 11 HTML
pages from shared header/footer/navbar markup (`build_common.py`) plus one
file per page. They're included for reference if you want to add a new page
using the same chrome — run `python3 build_all.py` from inside `tools/` after
editing to regenerate every HTML file at once. If you'd rather hand-edit the
HTML files directly (simpler for small tweaks), that works too — the build
scripts are optional.

## Customizing

- **Instagram handle / links:** search all files for `mech_designhub` and
  `https://www.instagram.com/mech_designhub/` and replace with your handle.
- **Products:** edit `js/products-data.js` directly, or regenerate the whole
  catalog with different categories/brands using the generator described below.
- **Colors/fonts:** all design tokens are CSS variables at the top of
  `css/style.css` (`:root { --blue-600: ...; }`) — change once, applies everywhere.
- **Real product photos:** product images currently use placeholder graphics
  (`placehold.co`) so the site works immediately with zero setup. Swap the
  `image` / `images` fields in `js/products-data.js` for real photo URLs (or a
  `assets/` folder of your own images) whenever you're ready.
- **Company logo in the PDF:** the PDF header currently draws a simple "MD"
  mark in code (see `buildOrderPDF` in `js/pdf-generator.js`). If you have a
  real logo file, jsPDF's `doc.addImage()` can drop it in — happy to wire that
  up if you share the logo.

## Notes on a couple of implementation choices

- Custom CSS was used instead of Bootstrap/Tailwind so every component (cards,
  modals, filters, PDF) could get a distinct, premium look rather than a
  default framework appearance — all class names are plain and easy to restyle.
- The order PDF is built directly with jsPDF's vector drawing/text API rather
  than rendering an HTML template through html2canvas — this keeps the PDF
  text crisp and small in file size instead of a rasterized screenshot.
- Wishlist and "recently viewed" persist locally per-browser via
  `localStorage` (no backend) — this is expected static-site behavior, not a
  bug, and resets if the customer clears browser data.
