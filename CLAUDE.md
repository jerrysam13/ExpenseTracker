# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the app

Open `index.html` directly in a browser — no build step, no server, no dependencies. Double-click the file in Finder or drag it into a browser window.

## Architecture

The entire app lives in a single file: `index.html`. It is structured as three inline sections:

- **`<style>`** — all CSS, written with plain class selectors (no framework)
- **`<body>`** — three card-shaped `div.card` elements: total banner, add-expense form, and the filterable expense list
- **`<script>`** — all JavaScript, written as plain global functions (no modules, no framework)

### Data flow

State is held in two module-level variables:

```
let expenses = []      // array of {id, name, amount, category, date}
let currentFilter      // 'All' | category string
```

Every mutation (add, delete, filter change) calls `save()` then `render()`:

- `save()` — writes `expenses` to `localStorage` under the key `'expenses'`
- `render()` — rebuilds `#expense-list` innerHTML and updates `#total` from scratch

There is no virtual DOM or diffing; the list is fully redrawn on every change.

### Expense object shape

```js
{ id: Date.now(), name: string, amount: number, category: string, date: string }
```

`id` is a Unix timestamp (milliseconds). `date` is stored as a locale string from `new Date().toLocaleDateString()`.

### Categories

The valid category values are defined in two places that must stay in sync:
1. `<select id="expense-category">` options in the HTML
2. `categoryColors` object in the script
3. Filter buttons in `#filter-bar`

When adding a new category, update all three.

### XSS prevention

User-supplied `name` values are passed through `escapeHtml()` before being set as `innerHTML`. Amount and category values are never interpolated as HTML.
