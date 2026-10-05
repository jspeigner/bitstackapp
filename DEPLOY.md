# Deploy bitstack.app (nosflare fork) to Cloudflare

This repo is a fork of [Spl0itable/nosflare](https://github.com/Spl0itable/nosflare) customized for **bitstack.app**, currently running nosflare **7.9.33** and updated here to **7.9.45**.

## Repositories

| Repo | Purpose |
|------|---------|
| [github.com/jspeigner/bitstackapp](https://github.com/jspeigner/bitstackapp) | GitHub fork Cloudflare can connect to |
| Origin `cs-s-team/bitstackapp` | Cursor / team working copy |

## Update the live Worker (recommended: reconnect Git)

Cloudflare Workers Git integration supports **GitHub**, not Origin. Use the GitHub fork:

1. Open [Cloudflare Dashboard](https://dash.cloudflare.com) → **Workers & Pages** → the existing worker serving `bitstack.app`.
2. Note the current **Worker name** and **D1** binding (`RELAY_DATABASE` → UUID). Put that UUID into `wrangler.toml` (`database_id`) and match `name` to the existing worker.
3. **Settings → Build** (or **Triggers / Git** depending on UI): disconnect the old source if linked to upstream `Spl0itable/nosflare`, then connect **`jspeigner/bitstackapp`**, branch **`main`**.
4. Keep existing bindings: D1 `RELAY_DATABASE`, Durable Object `RELAY_WEBSOCKET`, custom domain `bitstack.app`.
5. Deploy / save. Confirm NIP-11 shows `"version":"7.9.45"`:
   ```bash
   curl -s https://bitstack.app -H 'Accept: application/nostr+json' | jq .version,.software
   ```

## Alternative: Wrangler CLI deploy

```bash
npm install
npm run build
# Edit wrangler.toml: set name + database_id to the EXISTING worker/D1
npx wrangler login   # or set CLOUDFLARE_API_TOKEN
npx wrangler deploy
```

Do **not** create a new D1 database — reuse the live one so paid pubkeys and events are preserved.

## After deploy checklist

- [ ] `https://bitstack.app` landing page loads and shows **420 sats**
- [ ] NIP-11 version is `7.9.45` and software points at this fork
- [ ] Custom domain still points at the same worker
- [ ] Existing paid access still works (same D1)
