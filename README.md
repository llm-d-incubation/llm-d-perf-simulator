# llm-d-perf-simulator

The landing page for **BLIS**, the llm-d performance simulator: a single
self-contained `index.html` that states what BLIS answers, links the dashboards a
visitor can open, shows how a prediction is made, and lists the papers and
write-ups.

BLIS is an incubating component of [llm-d](https://llm-d.ai), the Kubernetes-native
stack for distributed LLM inference. llm-d deploys and scales serving engines across
a cluster; BLIS answers what to deploy.

This repository holds **only the landing page**. The simulator itself and the
dashboards it links to live elsewhere, under the
[inference-sim](https://github.com/inference-sim) org.

## What is here

```
index.html                     the entire page: markup, CSS and inline SVG
.github/workflows/pages.yml    deploys to GitHub Pages on push to main
```

No build step, no package manager, no JavaScript. Open `index.html` in a browser
and you are looking at production.

## Where it links

| Destination | What it is |
| --- | --- |
| [blis-config-search](https://inference-sim.github.io/blis-config-search/) | Pareto-optimal serving configurations, one report per scenario |
| [accuracy-dashboard](https://inference-sim.github.io/accuracy-dashboard/) | Predicted vs measured latency, broken out per model |
| [inference-sim](https://github.com/inference-sim/inference-sim) | The simulator source, Apache-2.0 |
| [blis-catalog](https://github.com/inference-sim/blis-catalog), [blis-registry](https://github.com/inference-sim/blis-registry), [blis-latency-kernel](https://github.com/inference-sim/blis-latency-kernel), [blis-schemas](https://github.com/inference-sim/blis-schemas) | The versioned pieces behind a prediction |

## Working on it

```sh
# Serve locally. Opening the file directly works, but a server matches production
# and avoids file:// quirks.
python3 -m http.server 8000
# then open http://localhost:8000
```

Edit `index.html` and reload. That is the whole loop.

### Before you push

The page has no tests, so the checks are manual and short:

1. **Resize the window** from a phone width to a wide desktop. Nothing should
   overflow horizontally; the pipeline diagram is the one exception, and it scrolls
   inside its own box by design.
2. **Click all the links.** Every destination is external, so a typo is invisible
   until someone lands on a 404.
3. **Check the page in both a narrow and a wide window** at the point where the
   cards reflow from three columns to two to one.

### Design

The page deliberately matches the two dashboards it links to, which share one design
system. The tokens are declared at the top of `index.html` and are copied from those
sites, not invented here:

| | |
| --- | --- |
| Paper / panel | `#f7f9f6` / `#fff` |
| Ink / muted | `#171c1a` / `#5b6660` |
| Accent | `#1f6e5c`, ink `#0f4438`, soft `#d9e9e1` |
| Type | IBM Plex Sans and IBM Plex Mono, 15px base |

Light mode only (`color-scheme: light`), matching the dashboards. Keep it that way
unless all three sites change together.

## Deploying

Pushing to `main` with a change to `index.html` deploys the page through
`actions/deploy-pages`. `workflow_dispatch` runs it by hand.

**One-time setup:** in Settings → Pages, Source must be set to **GitHub Actions**.
Until that is done the first run fails.

It serves at `https://llm-d-incubation.github.io/llm-d-perf-simulator/`. Note this is
a different origin from `inference-sim.github.io`, where both dashboards live, so
links between them leave the org.

## Accuracy of what the page claims

Every number and claim on the page should be traceable to something published. The
page says *"simulator predictions, not measured results"* in its footer and points at
the accuracy dashboard for how far predictions sat from measured runs. If you add a
claim, link its source, and prefer no number to a number you cannot attribute.
