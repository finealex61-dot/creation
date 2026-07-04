# FROSTYDBOT

A self-hosted, visual trading-bot builder built on the Deriv WebSocket API. Drag-and-drop strategy building with Blockly, interactive SmartCharts chart, automated strategy execution, and a suite of analysis tools.

## Stack

- **Framework**: React 18 + TypeScript
- **Bundler**: Rsbuild (not Vite/Next.js — important distinction)
- **Styling**: SCSS modules + CSS custom properties for theming
- **State**: MobX + Redux
- **Charts**: `@deriv-com/smartcharts-champion`
- **Bot builder**: Blockly
- **Routing**: React Router v6

## How to run

```bash
npm install
npm run dev
```

The app runs at `http://localhost:5000` (configured via Rsbuild).

## Environment variables

These are baked in at build time via `rsbuild.config.ts` → `source.define`. Set them before building:

| Variable | Required | Purpose |
|---|---|---|
| `NEXT_PUBLIC_DERIV_APP_ID` | Yes (for login) | Deriv OAuth App ID — get from [developers.deriv.com](https://developers.deriv.com/dashboard/) |
| `NEXT_PUBLIC_DERIV_ENV` | No | `production` or `staging` |
| `NEXT_PUBLIC_DERIV_REFERRAL_LINK` | No | Affiliate referral link |
| `NEXT_PUBLIC_DERIV_APP_NAME` | No | Override app display name |
| `GD_CLIENT_ID` / `GD_APP_ID` / `GD_API_KEY` | No | Google Drive strategy save/load |

Copy `.env.production` and fill in `NEXT_PUBLIC_DERIV_APP_ID` to enable login. Without it the Log in / Sign up buttons stay disabled.

## App structure

```
src/
  app/           — App root, router, content shell
  components/    — Shared UI components (layout, header, run panel, journal, etc.)
  constants/     — Tab indices (DBOT_TABS), tab IDs (TAB_IDS)
  external/      — Vendored bot-skeleton (Blockly workspace, API layer)
  hooks/         — Custom React hooks (API, store, auth)
  pages/
    dashboard/   — Dashboard tab
    bot-builder/ — Blockly bot builder tab
    free-bots/   — Pre-built strategy bots tab
    chart/       — SmartCharts tab
    tutorials/   — Tutorial content tab
    analysis/    — Analysis Tools tab (Digit Frequency, Win/Loss Tracker, Stake Calculator)
  stores/        — MobX stores
  utils/         — Helpers (GTM, blockly params, trade type modal, etc.)
  xml/           — Pre-built bot XML files (martingale, dalembert, etc.)
```

## Navigation tabs

Tabs are defined in `src/constants/bot-contents.ts` → `DBOT_TABS` and rendered in `src/pages/main/main.tsx`.

| Index | Tab | ID |
|---|---|---|
| 0 | Dashboard | id-dbot-dashboard |
| 1 | Bot Builder | id-bot-builder |
| 2 | Free Bots | id-free-bots |
| 3 | D-Circles | id-d-circles |
| 4 | Analysis Tool | id-analysis-tool |
| 5 | Market Analyzer | id-market-analyzer |
| 6 | Charts | id-charts |
| 7 | Trading View | id-trading-view |
| 8 | Copy Trading | id-copy-trading |
| 9 | Calculator | id-analysis |
| 10 | Tutorials | id-tutorials |

## Copy Trading

The Copy Trading tab (`src/pages/copy-trading/`) lets any account mirror trades from a leader account to multiple follower accounts in real time.

- **How it works**: Separate WebSocket connections per account; the leader's `transaction` stream is subscribed, and on each `buy` event the trade is replicated on all followers via `proposal` + `buy` calls.
- **Demo → real**: Set the leader token to a demo account API token, add real accounts as followers.
- **Key files**:
  - `src/services/copy-trading.service.ts` — WebSocket service
  - `src/stores/copy-trading-store.ts` — MobX store
  - `src/pages/copy-trading/index.tsx` — UI
- **API tokens**: Need Read + Trade scopes. Generated at app.deriv.com → API Token. Tokens are stored only in memory (never persisted to localStorage/sessionStorage).

## Branding

Branding (colors, fonts, app name) is driven by `brand.config.json`. After editing it, run:

```bash
npm run generate:brand-css
```

This runs automatically on `npm install`, `npm run dev`, and `npm run build`.

## User preferences

- App name: **FROSTYDBOT**
- User will add their own XML bot files later
