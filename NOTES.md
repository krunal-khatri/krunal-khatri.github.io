# Personal profile site — build notes

A one-page portfolio for Krunal Khatri. One hand-written HTML file, no build step, no
dependencies to install. Open `index.html` in a browser and it works.

```
personal-profile/
  index.html    the entire site — markup, Tailwind config, and ~150 lines of vanilla JS
  NOTES.md      this file
  assets/       hero photo, app icons for the side-project cards, and the BHIM/Jiraaf logos
```

---

## How it's built

| Concern | Choice | Why |
| --- | --- | --- |
| CSS | Tailwind via CDN (`cdn.tailwindcss.com`) | No build, no `node_modules`; the file stays portable and openable from `file://` |
| Theme | `tailwind.config` inline in a `<script>` | Custom `ink` scale + `accent` live in one place near the top |
| Fonts | Google Fonts — Fraunces (display), Inter (body), JetBrains Mono (labels) | Loaded with `preconnect`; the serif display face is what gives the page its character |
| Dark mode | `darkMode: 'class'` + a toggle that writes to `localStorage` | Respects `prefers-color-scheme` on first visit, remembers the choice after |
| Motion | `.reveal` class + `IntersectionObserver` | Sections fade up as they scroll in; fully disabled under `prefers-reduced-motion` |
| JS | One IIFE at the bottom, `'use strict'`, no framework | Theme toggle, sticky nav, mobile menu, scroll reveal, footer year |

The CDN build of Tailwind compiles classes in the browser. That is fine for a personal
site of this size, and it is the trade that buys the no-build-step property. If this ever
grows past one page, that is the first thing to revisit.

### Colour system

`ink` is a warm neutral scale (`#faf9f7` → `#111010`) rather than a grey one — it keeps the
page from reading cold. `accent` is `#8a6a4b` with a lighter `accent.soft` `#b08d69` for
dark backgrounds, where the base accent is too dim against `ink-950`.

---

## Section map

| # | id | What's in it |
| --- | --- | --- |
| — | `#top` | Hero: name, one-line positioning, CTAs, social icons, portrait slot |
| 01 | `#about` | Background and philosophy prose, stat row, education |
| 02 | `#experience` | Jiraaf → NPCI/BHIM → Daynil, as a timeline with per-role tech tags |
| 03 | `#skills` | Four cards — Mobile, Languages, Web & Backend, Tools & Practice |
| 04 | `#work` | Professional work: BHIM and Jiraaf headliners, then three earlier client apps |
| 05 | `#personal` | Six personal side projects |
| 06 | `#contact` | Closing line and mail CTA |

Nav links, the mobile menu, and these section ids have to stay in sync — they're plain
anchor links, so a renamed id silently breaks a nav item.

---

## Content sources

Experience, education, and the metrics in the BHIM card come from the résumé
(`E:\UserData\Downloads\Krunal_Khatri_Resume.pdf`).

The six side-project descriptions were written from each repo's own README and source, not
from memory:

| Card | Repo | Framing used |
| --- | --- | --- |
| MistriBook | `D:\ws\web\mistribook` | Real user (a Dombivli furniture contractor), single self-contained HTML file, Firestore added later |
| ReGrade | `D:\ws\rn\regrade` | GPU fragment shaders on Impeller, 33-point `.cube` LUT, on-device |
| Triage | `D:\ws\rn\triage` | RN New Architecture, React 19 + React Compiler, pure reducer with injected side effects |
| Certifi | `D:\ws\rn\certifi` | Parametric SVG backgrounds, real canvas editor, batch export per recipient |
| FetchFlix | `D:\ws\python\fetch-flix` | Every `yt-dlp` option exposed, sleep-survival queue, bundled ffmpeg |
| FileTagr | `D:\ws\python\FileTagr` | SQLite tagging, originals untouched, one binary is both CLI and GUI |

Cards are ordered strongest-first: MistriBook leads because it is the only one with a real
user, and that beats any technical detail on a portfolio.

Each description leads with the interesting engineering decision rather than a feature
list. That is deliberate — a feature list reads like a changelog; a decision reads like an
engineer.

---

## Deliberate omissions

These are choices, not gaps. Re-adding any of them should be a decision, not a cleanup.

- **No source links on the side-project cards.** All six repos are private. The section
  intro says so out loud, which turns a missing link into a stance and an invitation.
- **No "More on GitHub" link in the section header.** With private repos it would land a
  visitor on an empty profile. The hero and footer GitHub icons stay — the profile page
  still shows a contribution graph.
- **No UI screenshots anywhere.** App icons and logos are used instead.

### Employer logos

The BHIM and Jiraaf cards carry the real logos (`assets/icon-bhim.png`,
`assets/icon-jiraaf.svg`), supplied by Krunal.

These are NPCI's and Jiraaf's trademarks, used here nominatively — to identify products
worked on, in a résumé context, with no claim of endorsement. That is the ordinary use
portfolios make of employer marks. Keep it that way: the marks belong on their own project
cards only, never in the nav, hero, or footer, where they would read as affiliation
branding rather than work history. Alt text names each mark plainly ("BHIM logo").

Both marks are dark-on-transparent, so they sit on a permanently white chip
(`bg-white/95` + shadow) rather than directly on the card panel — otherwise they vanish in
dark mode. Verified in both themes.

`icon-bhim.png` is 200×50, displayed at 40px tall (~160px wide). Fine at 1×, slightly soft
on a 2× display; a ~400px-wide export would fix it if it ever bothers you.

---

## Hero photo

`assets/photo.jpg` (413×531, ~220KB), supplied by Krunal. Sits in the `aspect-[4/5]` frame
with `object-cover object-top` — the photo's own ratio (0.778) is close enough to the
frame's (0.8) that the crop is minimal, and `object-top` keeps the head from being cut if
it ever isn't. Replaces the "KK" monogram placeholder that was there before.

## Icon pipeline

Each side-project card shows its app's real icon at 64px on the card's gradient panel.
Nothing here was drawn freehand — every icon traces back to something in its own repo.

| File in `assets/` | Origin | Processing |
| --- | --- | --- |
| `icon-triage.png` | `triage/android/.../mipmap-xxxhdpi/ic_launcher_foreground.png` | Cropped to content bounds — the legacy `ic_launcher.png` has a white ring baked in |
| `icon-certifi.png` | same repo's foreground + `ic_launcher_background` `#4E5B92` | Composited, then centre-cropped at the adaptive-icon 72/108 safe ratio |
| `icon-regrade.png` | `regrade/android/.../ic_launcher.png` | As-is — already a full-bleed dark square |
| `icon-fetchflix.png` | `fetch-flix/assets/icon.png` | As-is, circular |
| `icon-filetagr.png` | `FileTagr/filetagr/assets/icon.png` | As-is; transparent artwork, so the card gives it a light tile and padding |
| `icon-mistribook.svg` | drawn here — MistriBook had no icon | See below |

Per-card CSS follows the artwork: `rounded-2xl` for square icons, `rounded-full` for the
circular ones (Triage, FetchFlix), and a `bg-ink-50` tile only for FileTagr's transparent PNG.

### Regenerating the Android-derived icons

```bash
python -c "
from PIL import Image
src = Image.open(r'D:/ws/rn/triage/android/app/src/main/res/mipmap-xxxhdpi/ic_launcher_foreground.png').convert('RGBA')
src.crop(src.getbbox()).save('assets/icon-triage.png')
"
```

```bash
python -c "
from PIL import Image
fg = Image.open(r'D:/ws/rn/certifi/android/app/src/main/res/mipmap-xxxhdpi/ic_launcher_foreground.png').convert('RGBA')
comp = Image.alpha_composite(Image.new('RGBA', fg.size, '#4E5B92'), fg)
s = fg.size[0]; keep = int(s * 72 / 108); off = (s - keep) // 2
comp.crop((off, off, off + keep, off + keep)).resize((256, 256), Image.LANCZOS).save('assets/icon-certifi.png')
"
```

The background colour comes from `certifi/android/app/src/main/res/values/colors.xml`
(`ic_launcher_background`), and the 72/108 crop is how Android itself renders an adaptive
icon — the foreground is drawn at 108dp with only the central 72dp guaranteed visible.

### The MistriBook icon

MistriBook had no icon, so one was drawn: a ledger sheet on a walnut squircle with a rupee
mark, in the app's own palette (walnut `#5c4530`, paper `#fbf8f4`, amber `#b5651d`, read out
of MistriBook's CSS variables rather than invented).

**The rupee glyph is a real font outline, not hand-drawn.** A first attempt drawn from
memory was wrong. The fix pulls the actual U+20B9 outline out of Segoe UI Bold:

```bash
python -c "
from fontTools.ttLib import TTFont
from fontTools.pens.svgPathPen import SVGPathPen
f = TTFont(r'C:/Windows/Fonts/segoeuib.ttf')
gs = f.getGlyphSet()
pen = SVGPathPen(gs)
gs[f.getBestCmap()[0x20B9]].draw(pen)
print(pen.getCommands())
"
```

That path is placed inside the SVG under `translate(196.10 396) scale(0.09763 -0.09763)`
— the negative Y flips font coordinates (Y-up) into SVG coordinates (Y-down), and the
scale sizes the glyph's 1434-unit height to 140px inside the 512px icon.

The same icon exists as PNG inside MistriBook itself (`icon-512.png`,
`apple-touch-icon.png`), rendered with PIL drawing the same font at 200px on the same
baseline, so the SVG and the PNGs agree.

---

## Still open

Nothing. Résumé download button lives beside "View work" in the hero
(`assets/Krunal_Khatri_Resume.pdf`), and the site is deployed at
`https://krunal-khatri.github.io/`.

---

## Editing recipes

**Add a side-project card** — copy any `<article>` in `#personal` and change the title,
paragraph, badge, tag `<li>`s, and `<img src>`. The grid is
`md:grid-cols-2 lg:grid-cols-3` and reflows on its own; nothing else needs touching.

**Add a skill tag** — append an `<li>` to the relevant card's `<ul>` in `#skills`. Tag
markup is identical everywhere, so copy a neighbour.

**Change the accent colour** — one value in `tailwind.config` near the top
(`colors.accent`). `accent.soft` is its dark-mode partner and should move with it.

**Check dark mode after any visual change.** Every surface has an explicit `dark:` variant;
a new element without one will look wrong in exactly one of the two themes.
