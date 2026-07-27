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
- **Pixel art** drawn on a real pixel grid: the runner is a hand-drawn 32×32
  sprite sheet (`runner.png`, 6-frame walk cycle in a horizontal strip), the
  skill vignettes are hand-drawn 32×32 PNGs in `src/images/`.

Arrow-up and arrow-down are deliberately *not* captured, so keyboard-only
visitors keep native page scrolling.

## Editing the content

The career timeline, the skill cards and the article list are plain HTML in
`index.html` - inside `.road`, `.scenes` and `.posts` respectively. They used
to be generated from JavaScript arrays, which was tidier to edit but meant
crawlers and link-preview bots saw an empty page. Static markup wins.

Colours are set per zone in the `PALETTE` object in the script, and the full
46-colour source palette is declared as CSS custom properties (`--a-b1`
through `--a-k10`) at the top of the stylesheet.

## Updating the runner sprite

`runner.png` is a 192×32 sheet, six 32×32 frames in a horizontal strip,
PNG with alpha - never JPEG, which destroys hard edges and has no
transparency. It's wired up with a percentage-based `background-size` so it
stays sharp at any of the runner's responsive `clamp()` sizes:

```css
#runner .sprite{
  background-image:url(runner.png);
  background-size:600% 100%;  /* 6 frames */
  image-rendering:pixelated;
}
#runner.go .sprite{ animation:walk .6s steps(6) infinite; }
@keyframes walk{ from{ background-position-x:0%; } to{ background-position-x:120%; } }
```

The `to` value is **not** 100% - with N frames, `background-position-x:100%`
only ever reaches frame N-1's position, and with a percentage-based
`background-size` the intermediate `steps()` stops land *between* frames
instead of on them (a visible smear/double-exposure look). The formula that
lands every step exactly on a frame is `to: 100 * N / (N-1)%`. For 6 frames
that's `100*6/5 = 120%`. If you change the frame count, recompute this
value, the `background-size` percentage (`100% * N`), and the `steps()`
count together - all three must agree.
If you replace it with a sheet with a different frame count, update the
`background-size` percentage (`100% * frame count`) and the `steps()` count
to match - both must stay in sync with the number of frames in the strip.

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
