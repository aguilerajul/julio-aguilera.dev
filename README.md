# julio-aguilera.dev

My personal site. A single-page, scroll-driven CV built as four illustrated
zones you run through, in the spirit of the old playable resumes - except the
world here is a server room and a road, not a platformer.

**Live:** https://julio-aguilera.dev

---

## What it is

One `index.html`. No framework, no build step, no dependencies. It is served
as a static file and it will still work in ten years, which is the point.

- **Four zones** - Start, Career, Skills, Writing - each with its own sky,
  ground and palette. Scrolling into a zone repaints the whole page.
- **A runner** pinned to the right of the viewport. It runs while you scroll,
  and you can drive it yourself with `←` `→` / `A` `D`, and jump with `W`.
- **Pixel art** drawn on a real pixel grid: the runner is a 20×27 sprite, the
  skill vignettes are 32×26. Rendered as inline SVG rectangles for now,
  to be replaced with hand-drawn PNGs.

Arrow-up and arrow-down are deliberately *not* captured, so keyboard-only
visitors keep native page scrolling.

## Editing the content

All the content lives in three arrays at the top of the `<script>` block at
the bottom of `index.html`. No HTML edits needed.

| Array    | Drives                                        |
|----------|-----------------------------------------------|
| `CAREER` | The timeline stops in the Career zone         |
| `SKILLS` | The four cards in the Skills zone             |
| `POSTS`  | The article list in the Writing zone          |

Colours are set per zone in the `PALETTE` object just below those, and the
full 46-colour source palette is declared as CSS custom properties (`--a-b1`
through `--a-k10`) at the top of the stylesheet.

## Replacing the SVG sprites with PNGs

Export at 1x as indexed PNG-8 with alpha - never JPEG, which destroys hard
edges and has no transparency. Then swap each `<svg>` for an element with a
background sprite sheet:

```css
#runner .sprite{
  width:116px; height:156px;
  background:url(runner.png) 0 0 / 464px 156px;  /* 4 frames */
  image-rendering:pixelated;
}
#runner.go .sprite{ animation:run .32s steps(4) infinite; }
@keyframes run{ to{ background-position:-464px 0; } }
```

Keep `background-size` an integer multiple of the native size, or you get
half-pixels. The `steps()` count must match the number of frames.

## Running it locally

Open `index.html` in a browser. That's it.

## Credits

- Colour palette: [Apollo](https://lospec.com/palette-list/apollo) by AdamCYounis
- Typefaces: Pixelify Sans, Silkscreen and IBM Plex Sans, via Google Fonts (OFL)

## License

The **source code** in this repository is MIT licensed - see [LICENSE](LICENSE).

The **written content, images and pixel art** are © 2026 Julio Aguilera and
are not covered by the MIT license. Take the code, learn from it, reuse it.
Please don't republish the writing or the artwork as your own.

## Elsewhere

[GitHub](https://github.com/aguilerajul) ·
[itch.io](https://okmaya.itch.io) ·
[ArtStation](https://www.artstation.com/okmaya) ·
[Sketchfab](https://sketchfab.com/okmaya) ·
[LinkedIn](https://www.linkedin.com/in/julio-aguilera-43940919/)
