# OD KnowledgeDesk

A self-contained, single-file web application for building and managing a shared IT knowledge base. Designed for help desk teams at Ogletree Deakins to document resolutions, cite articles on Cherwell tickets, and share a live library across analysts.

## Features

### Articles
- Create, edit, and delete knowledge base articles with rich HTML content
- Auto-assigned KB numbers (e.g. `KB-0001`)
- Categories and comma-separated tags for organization
- Full-text search across titles, content, tags, and KB numbers
- Sort by top rated, recently updated, newest, title, or most cited

### Sharing and Storage
The app supports three storage modes, in priority order:

| Mode | Description |
|------|-------------|
| **File** | Connects to a shared JSON file on a network drive via the File System Access API (Chrome/Edge). Auto-reloads when other analysts save changes. |
| **Browser** | Persists to `window.storage` (available in Claude preview environments). |
| **Memory** | No persistence; data lives only for the current session. Export/import JSON to back up. |

On reload, the app offers to reconnect to the last-used shared file without requiring a file picker.

### Rich Text Editor
- Bold, italic, underline
- Headings (H2, H3), blockquotes, code blocks
- Bulleted and numbered lists
- Inline images (paste or upload; stored as base64 data URIs)
- Internal article links (`§ KB-XXXX`) via an article picker
- External hyperlinks
- Paste from Word/Excel, including tables, with formatting preserved

### File Attachments
- Attach file references to articles (stored path: `K:\Attachments\`)
- Browse or drag-and-drop to add file entries
- In the Electron desktop build: open files and show in Explorer directly
- In browser: download from a connected Attachments folder, or copy the path to clipboard

### Ratings
- Each analyst rates articles 1-5 stars (one rating per device)
- Average score displayed on cards and in the article reader
- Smoothed Bayesian score used for the "Top rated" sort

### Favorites
- Per-device bookmarks stored in IndexedDB
- Filter the list to favorites only

### Cite in Cherwell
- Copies a formatted resolution note for pasting into a Cherwell ticket
- Configurable template with placeholders: `{id}`, `{title}`, `{category}`, `{date}`, `{tags}`
- Optionally includes the full article body
- Increments a resolution counter on the article

### Find in Article
- In-article search with match count and prev/next navigation (`Ctrl+F` / `Cmd+F`)

### Image Lightbox
- Click any image in an article to view it full-screen

### Themes
- Light and dark mode; auto-detects `prefers-color-scheme` on load, toggleable via the toolbar

### Layout
- Side-by-side (list left, reader right) or stacked (list top, reader bottom)
- Resizable pane divider; double-click to reset
- Responsive for mobile: switches to a single-pane view with back navigation

## Running the App

The entire application is a single HTML file with no external dependencies.

```
knowledge-desk.html
```

Open it directly in Chrome or Edge. No build step, no server, no installation required.

For a shared team library, open the file in Chrome/Edge and use **Settings > Storage > Connect to shared file** to link to a JSON file on a network drive that all analysts can access.

## Electron Desktop Build

When running inside the Electron wrapper (`window.kbNative` is present), the app gains:
- Direct read/write to the shared JSON file without browser permission prompts
- File watcher that reloads the library when another analyst saves
- "Open" and "Show in Folder" buttons for attachments

## Export and Import

Use **Settings > Export all (JSON)** to download a full backup. Use **Import** to restore or bulk-load articles from a previously exported file.

The JSON format:

```json
{
  "app": "OD KnowledgeDesk",
  "version": 2,
  "nextNum": 42,
  "settings": { "template": "Resolved using Knowledge Article {id}: {title}" },
  "articles": [ ... ]
}
```

## Access Control

When a shared file is connected as read-only (network permissions), the app automatically switches to view-only mode: the New Article button is hidden and a "View only" badge is shown. Existing data is never overwritten.
