# SureCart Image Migration Checker

A browser-based tool that audits and migrates product images in [SureCart](https://surecart.com/) stores. It identifies where each product image is hosted and helps you move them to the correct domain.

## What it does

- **Audits all products** via the SureCart API and classifies each image as:
  - **On Live Domain** — image URL matches your target domain (no action needed)
  - **Wrong Domain** — image is in a WordPress media library but on a different domain (e.g. staging/dev)
  - **SureCart CDN** — image is still hosted on SureCart's CDN and needs migrating to WordPress
  - **WP-Managed** — image is managed directly through the WordPress plugin (not exposed via API)

- **Fixes domain URLs** — rewrites product media URLs from one domain to another via the SureCart `product_medias` API (e.g. migrating from a staging site to a live site)

- **Migrates CDN images** — downloads images from SureCart's CDN, uploads them to your WordPress media library via the WP REST API, and updates the SureCart product media URL

- **Exports CSV reports** — downloads a full report of all products with their current and expected image URLs

## How to use

### 1. Serve the file locally

The tool makes API calls from the browser, so it needs to be served over HTTP (not opened as a `file://`):

```bash
cd /path/to/this/directory
python3 -m http.server 8080
```

Then open: **http://localhost:8080/surecart-image-checker.html**

### 2. Run an audit

1. Enter your **SureCart Secret API Key** (starts with `st_...`) — find this in your SureCart dashboard under Settings > API Keys
2. Enter your **target domain** (e.g. `mysite.com`) — the domain you want all images pointing to
3. Click **Check Products**

The tool will fetch all products and categorise their images.

### 3. Fix domain URLs

If products have images on the wrong domain (e.g. still pointing to a staging site):

1. Click **Fix Domain URLs** — no WordPress credentials needed
2. The tool rewrites each product's media URL to use your target domain via the SureCart API

### 4. Migrate CDN images to WordPress

If products still have images on SureCart's CDN:

1. Enter your **WordPress username** and **Application Password**
2. Click **Migrate CDN to WordPress**

The tool will download each image, upload it to your WP media library, and update the SureCart product.

#### Generating a WordPress Application Password

1. Go to `yoursite.com/wp-admin/users.php`
2. Click on your user
3. Scroll to **Application Passwords**
4. Enter a name (e.g. "SureCart Migration") and click **Add New Application Password**
5. Copy the generated password

## Requirements

- A SureCart secret API key with product read/write access
- For CDN migration: a WordPress user with upload permissions and an application password
- A modern browser (Chrome, Firefox, Safari, Edge)

## Tech

Single HTML file — no build tools, no dependencies, no server-side code. Uses the SureCart REST API (`/v1/products`, `/v1/product_medias`, `/v1/medias`) and optionally the WordPress REST API (`/wp-json/wp/v2/media`).
