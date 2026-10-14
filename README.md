# Vehicle Data Crawling with n8n

An educational n8n workflow demonstrating a small HTML-to-Excel pipeline for used-vehicle listings. It is intended as a learning/portfolio project, not a production-scale crawler.

## What it currently does

1. Reads search URLs from a local Excel input file.
2. Requests a search page with n8n's HTTP Request node.
3. Finds unique vehicle-detail links in the returned HTML.
4. Requests each detail page.
5. Extracts a title, total payment price, model year, mileage, and repair-history label using JavaScript.
6. Formats records into these Excel columns: `Brand`, `Title`, `URL`, `Price`, `Mileage`, `ModelYear`, `Repair History`, and `Sales Area`.
7. Converts the records into `vehicle_scrape_results.xlsx`.
8. Includes a Gmail node configured to attach the binary field named `data`. You must connect your own Gmail credential after importing the workflow.

## Current limitations

- The workflow is currently a **limited proof of concept**. It extracts at most **3 unique vehicle links per search page** as a test guard.
- It processes search URLs that are supplied in the input workbook. It does **not** yet discover every brand, every model, or all pagination pages automatically.
- HTML parsing is based on the current page markup and regular expressions. If the website changes its HTML, extraction may be incomplete and must be retested.
- The brand mapping is intentionally limited; unmatched brands can produce a blank `Brand`.
- `Sales Area` is inferred from the title text, so it may be blank or inaccurate.
- The workflow does not include the user's input/output spreadsheets, OAuth client secret, OAuth tokens, or n8n credential database.

## Requirements

- A running self-hosted n8n instance.
- An `.xlsx` input workbook named `input_urls.xlsx`.
- The workbook must have a header row containing a `URL` column. `Notes` is optional.
- A Gmail OAuth credential configured privately inside your own n8n instance if you want to use the email step.

## Input file setup

The workflow's **Read Input Excel** node uses this path by default:

```text
/home/node/.n8n-files/input_urls.xlsx
```

Place your input workbook at that path **inside the environment where n8n runs**, or edit the node's `File(s) Selector` to the permitted path used by your own installation. When using Docker, the host folder must be mounted into the container and the path must be allowed by n8n's file-access restrictions.

Example workbook structure:

| URL | Notes |
|---|---|
| `https://example.com/authorized-search-page` | Replace with a URL you are allowed to access |

The example URL is a placeholder, not a live search page.

## Import and configure

1. Download `vehicle-data-crawling.workflow.json`.
2. In n8n, use the workflow menu to **Import from File**.
3. Open **Read Input Excel** and set the correct path to your local `input_urls.xlsx`.
4. Test the upstream nodes with one authorized search URL.
5. Open **Create Excel Report**. Its file name is set to `vehicle_scrape_results.xlsx`, with a header row and sheet name `Vehicles`.
6. Open **Send Email** and select/create your own Gmail OAuth credential in n8n.
7. Replace the placeholder `you@example.com` in `To` with your own address for testing.
8. Confirm the Send Email attachment field is `data`, which matches the Convert to File node's output binary field.
9. Execute a small test, and check the generated workbook and email before changing any test limits.

## Security

- **Never commit OAuth Client Secrets, access tokens, refresh tokens, app passwords, `.env` files, n8n's `.n8n` data folder, database files, or the `N8N_ENCRYPTION_KEY`.**
- n8n credentials should be created and stored in your local n8n credential manager. This public JSON intentionally omits the credential binding so it cannot reference the author's private Gmail credential.
- Input/output workbooks are ignored by `.gitignore`; do not upload real vehicle data or private recipient information.
- The workflow is not intended to bypass logins, access controls, rate limits, or other restrictions.

## Responsible use

This repository is published for learning purposes. Only use it on websites and datasets where you have permission to automate access and reuse the content. Review the target website's current terms and applicable law before collection or redistribution. For Kuruma-EX, obtain written permission before automated collection or reuse as its terms may restrict copying/reuse of site content.

## Roadmap

- Add automated brand and model discovery where explicitly authorized.
- Add authorized pagination with stop conditions and rate limiting.
- Add stronger deduplication, retries, and error reporting.
- Expand brand and region extraction, and validate each extracted field against the source page.
- Store progress so a run can resume safely.
