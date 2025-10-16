# notion-ops

Automation repo for Notion (REST API) aligned to **Notion-Version: 2025-09-03** and the new **data sources** model.

## What this repo does
- Runs **CI jobs** (dryrun/apply) against Notion using an **Internal Integration** token.
- Uses **governance-by-scope**: the integration can only touch pages/teamspaces you explicitly connect in Notion.
- Keeps **Cursor MCP** separate for interactive edits (MCP is OAuth as a user; no token stored here).

## Quick start
1. Create a Notion **Internal Integration**, enable `Read/Update/Insert content`, copy the `ntn_…` token.
2. In Notion, open each target top-level page/teamspace → **Add connections** → choose your integration.
3. In this GitHub repo: **Settings → Secrets → Actions**  
   - `NOTION_TOKEN` = `ntn_…`  
   - `NOTION_VERSION` = `2025-09-03`
4. Run the smoke test: **Actions → notion-smoke → Run workflow**.

## Versioning changes (2025-09-03)
- Use header `Notion-Version: 2025-09-03`.
- Discover the database’s **data_source_id** and use it as the **parent** when creating pages.
- Update relation writes to use `data_source_id` as required.

### Minimal Node helper (example)
```ts
import { Client } from "@notionhq/client";
const notion = new Client({
  auth: process.env.NOTION_TOKEN,
  notionVersion: process.env.NOTION_VERSION || "2025-09-03",
});
export async function getDataSourceId(databaseId: string) {
  const db = await notion.request({ method: "get", path: `databases/${databaseId}` });
  return db.data_sources?.[0]?.id;
}
