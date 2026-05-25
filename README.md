# Cubox Plugin for Obsidian

[中文文档](README.zh-CN.md)

The official Cubox plugin for Obsidian syncs articles and annotations from Cubox into your vault.

## Features

- **One-way sync**: Cubox → Obsidian only. Changes in Obsidian are not sent back to Cubox.
- **Automatic or manual sync**: Run on a schedule or trigger sync from the ribbon icon / command palette.
- **Filtering**: Filter by folder, type, tags, and status.
- **Templates**: Customize file names, front matter, and note body.
- **Annotation sync**: Export highlights and notes from Cubox into Obsidian.

## Installation

### Install from the Community

1. Open Obsidian settings
2. Navigate to the **Community plugins** tab
3. Click **Browse** and search for **Cubox**
4. Click **Install**, then **Enable**

### Manual Installation

1. Download the latest `main.js`, `manifest.json`, and `styles.css` files
2. Create a `.obsidian/plugins/obsidian-cubox` folder in your Obsidian vault
3. Copy the downloaded files into this folder
4. Enable the plugin in Obsidian settings

## Configuration

1. **Cubox server domain**: Select the domain you use (`cubox.cc` or `cubox.pro`)
2. **Cubox API key**: Enter your API key or paste the full key link from Cubox web settings under **Extensions & Automation → API Extension**

## Quick Start

1. Select the correct server domain and enter your API key.
2. Configure **all four filters** before the first sync:
   - Folder filter
   - Tag filter
   - Type filter
   - Status filter
3. Choose a target folder and review the default templates if needed.
4. Trigger sync manually (ribbon icon or **Sync now** command), or set an auto-sync interval.

> **Important:** Sync will not run until every filter category has at least one option selected. The default empty filter state is intentional—you must choose what to sync.

## How Sync Works

Understanding these rules prevents most confusion:

### One-way, create-only

- Sync direction is **Cubox → Obsidian only**.
- The plugin **creates new notes**. It does **not update** notes that already exist in your vault.
- If you add new highlights, edit a title, or change tags in Cubox after a note was synced, those changes **will not appear** in the existing Obsidian note.

### Each Cubox card is synced once

- Every synced note stores the Cubox card `id` in front matter (always included for deduplication).
- On later syncs, if a note with the same `id` already exists at the target path, that card is **skipped**.
- The completion notice may show `X new items, Y skipped`. **Skipped** means the card was already synced—not an error.

### Incremental fetching

- The plugin remembers where the last sync stopped and only fetches **new cards** on subsequent runs.
- Changing the **target folder** resets that cursor, so historical cards may be fetched again—but they are still subject to the one-time dedup rule above. Existing notes in the old folder are **not** updated.

### Annotations are a snapshot

- Highlights exported to Obsidian reflect what Cubox returned **at sync time**.
- Sync again after you finish annotating in Cubox. A longer interval or manual sync helps avoid exporting incomplete highlights.

### Default template does not include full article text

- The default content template includes title, description, links, and highlights.
- To sync the full article body, add `{{content}}` or `{{content_highlighted}}` to your **Content template** in settings.

## Filters

Only items that match **all** filter categories are synced:

| Filter | What it does |
|--------|----------------|
| **Folder** | Limit to selected Cubox folders, or choose **All items** |
| **Tag** | Limit to specific tags, **No tags**, or **All items** |
| **Type** | Article, Snippet, and/or **Others** (Memo, Image, Audio, Video, File) |
| **Status** | **All items**, or restrict to read / starred / annotated items |

Tips:

- If many items seem missing, review every filter—not just folders.
- Selecting multiple status options (e.g. read **and** starred) narrows results further.
- If you only need articles with highlights, use the annotated status filter—but remember that already-synced notes still will not receive new highlights later.

## Templates

Use plugin settings to customize:

- **File name template** — Mustache variables such as `{{{title}}}` and `{{{create_time}}}`
- **Metadata variables** — Front matter fields (the Cubox `id` is always written)
- **Content template** — Note body layout, including `{{#highlights}}` blocks
- **Date format** — Format used for date variables in templates

See the reference links on the settings page for full variable lists and examples.

## FAQ

### Why don't my new highlights show up in Obsidian?

The plugin does not update existing notes. New or edited highlights in Cubox stay in Cubox unless you edit the Obsidian note yourself.

There is no built-in "re-sync and overwrite" option. Workarounds:

- Manually update the note in Obsidian, or
- Delete the Obsidian note and sync again (only if you understand dedup and filter behavior)

### Can I sync only once, or does it keep syncing?

Both are possible:

- **Auto sync** runs on your chosen interval and picks up **new** Cubox cards.
- **Manual sync** (ribbon icon or command palette → **Sync now**) does the same on demand.
- Already-synced cards are skipped; the plugin is not limited to a single run.

### Why is nothing syncing?

Check these in order:

1. API key and server domain are correct
2. **All four filters** are configured (sync is blocked if any filter is empty)
3. Your Cubox items actually match the combined filter rules

### Why are many items missing?

Common causes:

1. **Filters too strict** — e.g. only annotated + specific tag + one folder
2. **Type filter** — **Others** not selected, so memos/files are excluded
3. **Tag filter** — only certain tags selected, so untagged items are excluded
4. **Default template** — no `{{content}}`, so notes look shorter than expected (metadata + highlights only)
5. **Synced too early** — highlights were still being edited in Cubox when the card was first exported

### What does "skipped" mean in the sync notice?

Those Cubox cards were already exported to Obsidian (matched by `id`). They were not synced again.

### Do Obsidian edits sync back to Cubox?

No. Sync is one-way: Cubox → Obsidian.

## Dependencies

- [Mustache](https://mustache.github.io/): Template rendering
- [Luxon](https://moment.github.io/luxon/#/formatting?id=table-of-tokens): Date and time handling

## License

This project is licensed under the [MIT License](https://choosealicense.com/licenses/mit/).
