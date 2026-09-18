# vLLM Semantic Router — Interactive Presentation

A ~30-minute interactive web presentation for **vLLM Semantic Router**, matching the format of the [live demo](https://vsr-demo-user-cnuland.apps.ocp.cloud.rhai-tmm.dev/).

Based on [cnuland/hello-chris-vsr-demo](https://github.com/cnuland/hello-chris-vsr-demo).

## Run Locally

```bash
python3 -m http.server 8080
open http://localhost:8080
```

Or open `index.html` directly in a browser (fullscreen recommended: press `F`).

## Presenter Controls

| Key | Action |
|-----|--------|
| `→` / `Space` | Next slide |
| `←` | Previous slide |
| `P` | Toggle speaker notes |
| `T` | Reset 30-min timer |
| `A` | Auto-play |
| `F` | Fullscreen |

See [PRESENTER_GUIDE.md](PRESENTER_GUIDE.md) for the full slide-by-slide script with timing.

## Structure

15 animated scenes covering:

1. Introduction
2. The Multi-Model Challenge
3. What vLLM Semantic Router Does
4. Architecture
5. Signals & Decisions
6. Cost-Optimized Routing
7. Fine-Tuning for Production
8. Fine-Tuning Pipeline (Red Hat AI)
9. Banking & Data Sovereignty
10. Enterprise Guardrails
11. Agentic AI
12. llm-d Integration
13. End-to-End Request Lifecycle
14. Key Takeaways
15. Resources & Q&A

## Customization

- **Speaker notes:** Edit the `speakerNotes` array in `index.html`
- **Slide timing:** Edit `sceneDurations` in `index.html`
- **Slide content:** Edit the `scene-*` divs in `index.html`
