# CLAUDE.md

Guidance for working in this repository.

## What this repo is

The BLIS landing page: one self-contained `index.html` (markup, CSS and inline SVG in
a single file) plus a GitHub Pages workflow. No build step, no package manager, no
JavaScript, no tests. Editing `index.html` and reloading the browser is the entire
development loop.

Scope is narrow on purpose. This is a product page, not a README and not a docs site.
The simulator, the dashboards and the calibration data all live under the
[inference-sim](https://github.com/inference-sim) org.

## Never invent a technical claim

This page describes a research system whose authors will read it. Every claim has to
be traceable, and several mistakes in this file's history were claims that sounded
right and were wrong:

- A plate was labelled `cost / throughput`. The tool it links to plots no cost axis at
  all; its Pareto chart is `output tok/s/GPU` against `output tok/s/user`.
- The same chart's frontier was drawn sloping up. It slopes **down**: pushing per-accelerator
  throughput higher costs per-user speed, which is the whole trade-off.
- A paragraph claimed you could hand BLIS an llm-d-router config and simulate its
  policies. That embedding is a proposal under discussion, not a shipped feature.

Before writing a capability, a metric name or a number, read the source: the
dashboards' own JS bundles name their axes and metrics, repo descriptions say what
each component does, and the papers and blog posts carry the measurements. If a claim
cannot be sourced, leave it out. Prefer "substantially" with a link over a precise
figure taken from the wrong place; note that the paper abstract and the blog posts
report *different* numbers for the same policies, so do not mix them.

## Verify layout by measuring, not by eyeballing screenshots

Headless Chrome's old `--headless` mode enforces a minimum window width, so a phone
screenshot looks clipped when the layout is fine. Chasing that illusion wasted real
time here. Use `--headless=new`, and for anything about wrapping, alignment or
overflow, measure the DOM instead:

```sh
# Serve the page, then load it in an iframe at a fixed width from a same-origin
# harness page and read getBoundingClientRect / getClientRects.
python3 -m http.server 8000
```

`file://` iframes are cross-origin, so the harness has to be served over HTTP too.
Useful things to assert, all of which have regressed at least once:

- **Page overflow.** No element's right edge past the viewport, excluding anything
  inside `.figscroll`.
- **Footer rules aligned.** Every `.cardfoot` in a row shares a `top`. The
  `Apache-2.0` pill is 1px taller than plain link text and used to push its card's
  rule up; the foot has a `min-height` so no child sets the height.
- **Caption line counts equal per row**, or the rules above the action links go
  ragged.
- **No orphan lines.** No line of a caption under ~20% of its measure.
- **SVG labels legible.** A 980-unit-wide `viewBox` scaled to a phone renders 10px
  text at under 3px. The diagram has a `min-width` and scrolls sideways below 940px
  rather than shrinking.
- **SVG text inside its box.** Compare each `<text>` `getBBox()` against the `<rect>`
  it sits in; labels have overrun by a few px unnoticed.

## Typography and copy

- **No em dashes.** Use colons, periods or commas. Do not substitute en dashes or
  minus signs either.
- **Say "accelerator", not "GPU"**, in prose. The exceptions are literal metric
  identifiers (`tok/s/GPU`) and published titles, which keep their original spelling
  because renaming them would misreport the source.
- `text-wrap: pretty` does not reliably prevent a short last line in Chrome;
  `text-wrap: balance` does. Rewording to fix an orphan usually just moves it to a
  different viewport width, so prefer the CSS.

## Design system

The tokens at the top of `index.html` are copied from
[blis-config-search](https://inference-sim.github.io/blis-config-search/) and the
[accuracy dashboard](https://inference-sim.github.io/accuracy-dashboard/), which share
one design system byte for byte. The three sites should read as one property, so do
not introduce a colour, typeface or radius that those sites do not use. Light mode
only, matching them.

Diagrams are hand-authored inline SVG using `var(--…)` tokens so they theme with the
page: no libraries, no image files, no `<script>` inside the SVG. Each figure carries
`role="img"` and an `aria-label` stating the same thing its caption does.

## Deploying

Pushing to `main` with a change to `index.html` deploys through
`actions/deploy-pages`. Settings → Pages → Source must be **GitHub Actions**, which
needs repo admin and cannot be set through the API.

The page serves from `llm-d-incubation.github.io`, a different origin from
`inference-sim.github.io` where the dashboards live.

## Review happens elsewhere

Design review for this page runs in
[inference-sim/inference-sim#2040](https://github.com/inference-sim/inference-sim/discussions/2040).
Screenshots for it are hosted on that repo's `assets/landing-mockup` branch under
`docs/assets/landing-mockup/`. When posting an updated mockup, add a new file rather
than overwriting one an existing comment references, so older comments keep showing
what their author reviewed.
