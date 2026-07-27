# Blog summaries in the Writing section

## Problem

The "Writing" zone lists posts as links to `#` with "In progress" / "Planned"
status tags — dead links pointing at articles that don't exist yet. The site
owner wants an actual place to write short pieces directly on the page,
without building out separate article pages.

## Goals

- Replace the dead-link post list with self-contained short write-ups
  ("resúmenes") that live entirely inside `index.html`.
- Each post collapsed by default, showing only its title; clicking expands
  it in place to show the full text.
- Multiple posts can be expanded at once — opening one does not close
  another.
- No new JavaScript, no build step, no separate pages — stays inside the
  site's existing "single static HTML file" constraint (see README.md).

## Non-goals

- No pagination, tagging, RSS feed, or post archive.
- No dedicated per-post URLs/pages.
- No CMS or markdown pipeline — posts are hand-written HTML in `index.html`,
  same as the rest of the content (career timeline, skill cards).

## Design

### Markup

Each entry in `.posts` changes from an `<a class="post" href="#">` to a
native `<details class="post">`:

```html
<details class="post rise">
  <summary>
    <span class="meta">...</span>
    <h3>Post title</h3>
  </summary>
  <p>Full summary text goes here.</p>
</details>
```

`<details>`/`<summary>` is the mechanism because:
- It needs zero JavaScript — the browser handles open/closed state natively.
- Content stays in the DOM (and visible to crawlers/link-preview bots) even
  while collapsed, matching the site's existing static-markup-over-JS stance.
- Keyboard and screen-reader accessible by default.
- Each `<details>` is independent, so "multiple open at once" is the native
  behavior with no extra code.

### Style

- The native disclosure triangle is hidden (`summary::-webkit-details-marker{display:none}`,
  `summary{list-style:none}`) and replaced with a `+`/`×` glyph in
  `var(--pixel)`, right-aligned, matching the existing hover affordance on
  `.scene` (same `steps()` transition timing already used across the site).
- Expanding reveals the paragraph with a short CSS-only fade/slide — no JS.
  If smooth height animation isn't practical in pure CSS for the target
  browsers, a simple opacity fade with no height animation is an acceptable
  fallback (content simply appears/disappears without a slide).
- Layout, spacing and hover behavior otherwise match the current `.post`
  styling (border, meta label, heading).

### Content

Two posts carry over from the current placeholder list, rewritten as
finished short pieces (not "in progress" / "planned" teasers for a future
article, since there is no future article — the summary *is* the content):

1. **Passkeys in C#: the whole WebAuthn ceremony, end to end**
2. **Choosing a database you can still live with in three years**

Draft copy is written by Claude in the site's existing tone (direct,
concrete, no jargon) and handed to the site owner to edit or replace.
Adding a new post later means copying the `<details>` block and writing a
new title/paragraph — no CSS or JS changes needed.

## Testing

Manual only, consistent with the rest of this vanilla-HTML site:
- Open in a browser, confirm each post expands/collapses independently.
- Confirm keyboard (Tab + Enter/Space on the summary) toggles state.
- Confirm collapsed posts still contain their full text in the DOM (view
  source), so crawlers see real content.
- Confirm `prefers-reduced-motion` still disables the existing `.rise`
  transition (unaffected by this change) and that the new expand
  animation doesn't fight it.
