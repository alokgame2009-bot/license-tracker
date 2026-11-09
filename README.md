# License & Product Tracker

A responsive GitHub Pages app inspired by your spreadsheet screenshot. It has editable records, 50 rows per page, next/previous paging, search, delete confirmation, CSV export, totals and expiry status.

## 1) Run locally
Open `index.html` in a browser. Records are saved in that browser's localStorage.

## 2) Publish on GitHub Pages
1. Create a new GitHub repository.
2. Upload `index.html` to the repository root (you may upload the whole project).
3. Open **Settings → Pages**.
4. Under **Build and deployment**, select **Deploy from a branch**, choose `main` and `/ (root)`, then Save.
5. Wait for GitHub Pages to publish. Your site will be at `https://YOUR-USERNAME.github.io/YOUR-REPO/`.

## 3) Enable reliable Discord notifications (Cloudflare Worker)
GitHub Pages is static and cannot run a reliable background timer when nobody has the page open. This project includes a Cloudflare Worker + KV scheduler so expiry alerts can be sent while the page is closed.

1. Create a free Cloudflare account and install Wrangler (`npm install -g wrangler`).
2. From the `worker` folder run `wrangler login`.
3. Create KV: `wrangler kv namespace create RECORDS`. Copy the returned ID into `worker/wrangler.toml` replacing `REPLACE_WITH_KV_NAMESPACE_ID`.
4. Set secrets:
   - `wrangler secret put API_KEY` — enter a long random private key.
   - `wrangler secret put DISCORD_WEBHOOK_URL` — paste the Discord webhook URL for your log channel.
5. Deploy from the `worker` folder: `wrangler deploy`.
6. Copy the deployed Worker URL. In your GitHub Pages site click **Discord settings**, paste the Worker URL and the same API key, then **Save settings**.
7. Add/edit records in the page and click **Sync records**. Repeat sync after changes so the cloud scheduler has the latest list.

The Worker checks hourly and sends a Discord embed in the first check window after the 10-hour-before-expiry point. Expiry is interpreted as the end of the selected date in India Standard Time (IST). The webhook URL and API key are never included in the public site source; the API key is stored only in that browser's localStorage. Use a strong API key and keep it private.

## Important notes
- Browser-only records do not automatically appear on another device. Use **Sync records** after changes to store the current list in Cloudflare KV.
- If you change or delete records, sync again. The Worker uses record IDs to avoid sending the same reminder twice.
- A notification can be up to roughly one hour later than the exact 10-hour mark because the scheduled check runs hourly.
- Do not commit Discord webhook URLs, API keys, customer secrets, or private records to a public GitHub repository.
- This is a lightweight tracker, not a multi-user database with authentication/roles. Keep your GitHub repository private if your source/configuration needs to remain private.
