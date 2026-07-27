# Blog Summaries Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the dead-link post teasers in the Writing section of `index.html` with self-contained, independently expandable/collapsible short write-ups, using native `<details>`/`<summary>` and no new JavaScript.

**Architecture:** Single-file change to `index.html` — markup swap from `<a class="post" href="#">` to `<details class="post">`, plus new CSS rules scoped to `.post`/`.post summary`. No JS, no build step, no new files.

**Tech Stack:** Plain HTML + CSS, matching the rest of the site (vanilla, no framework, no build).

## Global Constraints

- No new JavaScript, no build step, no separate pages — single `index.html` file only (per spec: [docs/superpowers/specs/2026-07-28-blog-summaries-design.md](../specs/2026-07-28-blog-summaries-design.md)).
- Collapsed post content must remain in the DOM (not injected by JS) so crawlers/link-preview bots see it.
- Multiple posts can be expanded simultaneously — no accordion/mutual-exclusion behavior.
- Visual style (fonts, colors, spacing, `steps()` transition timing) must match the site's existing pixel-art aesthetic already defined in `index.html`'s `<style>` block.
- `prefers-reduced-motion` handling already in `index.html` (around the `.rise` rules) must continue to work; the new expand transition must not fight it.

---

### Task 1: Convert post teasers to expandable `<details>` summaries

**Files:**
- Modify: `index.html` (CSS block around lines 291–306, the `.post`/`.posts` rules)
- Modify: `index.html` (markup block around lines 572–587, the `.posts` div contents)

**Interfaces:**
- No JS functions or cross-file interfaces — this is a self-contained markup + CSS change.
- Reuses existing CSS custom properties already defined in `index.html`'s `:root` (e.g. `--pixel`, `--line`, `--soft`, `--pop`) — do not invent new custom properties.

- [ ] **Step 1: Replace the two post entries' markup**

Find the current `.posts` block in `index.html`:

```html
    <div class="posts">
      <a class="post rise" href="#">
        <span class="meta">In progress</span>
        <h3>Passkeys in C#: the whole WebAuthn ceremony, end to end</h3>
        <p>What the challenge actually protects, what you store, and the three places the tutorials and the spec disagree.</p>
      </a>
      <a class="post rise" href="#">
        <span class="meta">Planned</span>
        <h3>Choosing a database you can still live with in three years</h3>
        <p>Access patterns first. Picking between key-value and relational without the religion.</p>
      </a>
    </div>
```

Replace it with:

```html
    <div class="posts">
      <details class="post rise">
        <summary>
          <span class="meta">Notes</span>
          <h3>Passkeys in C#: the whole WebAuthn ceremony, end to end</h3>
        </summary>
        <p>Every passkey tutorial shows you the happy path and skips the part where the challenge, the origin and the credential ID all have to line up or the browser silently refuses. The short version: the challenge is a nonce you generate server-side and never trust the client to echo back unchecked, the origin check is what actually stops phishing (not the biometric prompt, which is just UX), and the three places I've seen tutorials and the spec disagree are attestation handling, resident-key requirements, and what "user verification" is supposed to mean when the authenticator itself decides. Fido2NetLib gets you most of the way in C# - the rest is reading the WebAuthn spec once instead of the fifth blog post copying the first one.</p>
      </details>
      <details class="post rise">
        <summary>
          <span class="meta">Notes</span>
          <h3>Choosing a database you can still live with in three years</h3>
        </summary>
        <p>Start from the access pattern, not the schema. If you're reading by a single key almost every time, a key-value store will outlive three schema migrations that would've hurt in a relational one. If you need to ask questions you haven't thought of yet - ad hoc joins, reporting, "show me everyone who" - relational buys you that flexibility back. Most real systems are honestly both: a fast key-value path for the hot read, and a relational store for the stuff that needs to stay queryable. The mistake isn't picking the "wrong" one, it's picking either one on brand loyalty instead of on what you actually do with the data.</p>
      </details>
    </div>
```

- [ ] **Step 2: Update the `.post` CSS rules**

Find the current CSS block:

```css
.posts{margin-top:24px;max-width:58ch;}
.post{
  display:block;text-decoration:none;padding:18px 0;
  border-bottom:3px solid var(--line);
  transition:padding-left .12s steps(2);
}
.post:first-child{border-top:3px solid var(--line);}
.post:hover{padding-left:14px;}
.post .meta{
  font-family:var(--pixel);font-size:9px;letter-spacing:.1em;text-transform:uppercase;
  color:var(--pop);display:block;margin-bottom:6px;
}
.post h3{font-family:var(--display);font-weight:700;font-size:1.3em;line-height:1.18;margin:0 0 5px;}
.post p{margin:0;color:var(--soft);font-size:.94em;}
```

Replace it with:

```css
.posts{margin-top:24px;max-width:58ch;}
.post{
  display:block;padding:18px 0;
  border-bottom:3px solid var(--line);
  transition:padding-left .12s steps(2);
}
.post:first-of-type{border-top:3px solid var(--line);}
.post:hover{padding-left:14px;}
.post summary{
  cursor:pointer;list-style:none;
}
.post summary::-webkit-details-marker{display:none;}
.post summary::after{
  content:"+";
  float:right;
  font-family:var(--pixel);font-size:14px;
  color:var(--pop);
}
.post[open] summary::after{content:"\00d7";}
.post .meta{
  font-family:var(--pixel);font-size:9px;letter-spacing:.1em;text-transform:uppercase;
  color:var(--pop);display:block;margin-bottom:6px;
}
.post h3{font-family:var(--display);font-weight:700;font-size:1.3em;line-height:1.18;margin:0 0 5px;}
.post p{
  margin:10px 0 0;color:var(--soft);font-size:.94em;
  animation:post-fade .18s steps(3);
}
@keyframes post-fade{from{opacity:0;}to{opacity:1;}}
```

Note: `.post:hover` currently applies to the whole `<details>` element (matches the previous behavior where it applied to the whole `<a>`). This is intentional — hovering anywhere on the post, open or closed, still gives the padding-shift affordance.

- [ ] **Step 3: Verify `prefers-reduced-motion` still disables the fade**

Find the existing reduced-motion block in `index.html`:

```css
@media(prefers-reduced-motion:reduce){
  html{scroll-behavior:auto;}
  *{animation:none !important;transition-duration:.01ms !important;}
  .rise{opacity:1;transform:none;}
}
```

Confirm `*{animation:none !important;}` already covers the new `post-fade` keyframe — no edit needed here, this step is a check, not a change.

- [ ] **Step 4: Manual verification in a browser**

Open `index.html` directly in a browser (double-click the file or drag it into a browser tab — no server needed, per the site's "no build step" setup).

Check all of the following:
1. Scroll to the "Writing" zone. Both posts show only their title (collapsed) with a `+` on the right.
2. Click the first post's title — it expands to show the full paragraph, the marker changes to `×`.
3. Click the second post's title — it also expands. Confirm the first one is still open (multiple-open, not accordion).
4. Click the first post's title again — it collapses back to just the title, marker returns to `+`.
5. Tab to a post's summary with the keyboard and press Enter or Space — confirm it toggles open/closed the same way as a click.
6. Right-click → "View Page Source" (not the rendered DOM) and confirm the full paragraph text for both posts is present in the raw HTML even though the posts render collapsed. This is what keeps the content visible to crawlers.
7. In OS/browser accessibility settings, enable "reduce motion", reload, and confirm expand/collapse still works (no animation, but no broken layout either).

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "$(cat <<'EOF'
Replace dead-link post teasers with expandable blog summaries

Posts in the Writing section now use native <details>/<summary> instead
of linking to "#" — each one expands in place to show a real, finished
short write-up, and multiple posts can be open at once.
EOF
)"
```
