# Firedancer Frontend → Next.js Component Library + Storybook

## Comprehensive Implementation Plan

---

## 1. Project Overview

**Source:** React 18 + Vite + TanStack Router real-time Solana validator dashboard
**Target:** Next.js 14 (App Router) component library with Storybook 8, Bloomberg Terminal-style design system

### Current Stack → New Stack

| Current | New |
|---------|-----|
| Vite 5.4 | Next.js 14 (App Router) |
| TanStack Router | Next.js App Router |
| Radix UI Themes | Radix UI Primitives + Custom Bloomberg Theme |
| CSS Modules (camelCase) | CSS Modules + CSS Variables (Bloomberg tokens) |
| Jotai atoms (WebSocket-coupled) | Jotai atoms (decoupled with mock providers) |
| No Storybook | Storybook 8 with full addon suite |
| 147 ad-hoc colors | Systematic Bloomberg design tokens |

---

## 2. Bloomberg Terminal Design System

### 2.1 Design Principles

The Bloomberg Terminal aesthetic is defined by:

- **Dark-first UI**: Pure black/near-black backgrounds (`#000000`, `#0A0A0A`, `#111111`)
- **High-density data display**: Every pixel carries information
- **Monospace typography**: `Roboto Mono` for data, `Inter Tight` for UI labels
- **Signature amber/orange accents**: `#FF8C00` (Bloomberg orange) for highlights
- **Flat design, minimal borders**: Subtle `1px` borders in dark grays
- **Status-driven color coding**: Green (up/success), Red (down/failure), Amber (warning/pending)
- **Dense grid layouts**: Tight spacing, maximum data per viewport
- **No rounded corners**: Sharp, terminal-like edges (0px border-radius)

### 2.2 Design Token System

```
tokens/
├── colors.ts          # Color palette (backgrounds, text, accents, status)
├── typography.ts      # Font families, sizes, weights, line-heights
├── spacing.ts         # 4px base grid system
├── borders.ts         # Border widths, styles, colors
├── shadows.ts         # Minimal/no shadows (flat Bloomberg look)
└── index.ts           # Combined token export
```

**Color Palette:**

| Token | Value | Usage |
|-------|-------|-------|
| `bg-primary` | `#000000` | Main background |
| `bg-secondary` | `#0A0E17` | Card backgrounds |
| `bg-tertiary` | `#111927` | Elevated surfaces |
| `bg-input` | `#0D1117` | Input fields |
| `border-default` | `#1E293B` | Default borders |
| `border-subtle` | `#162032` | Subtle separators |
| `text-primary` | `#E2E8F0` | Primary text |
| `text-secondary` | `#8892A5` | Secondary text |
| `text-muted` | `#4A5568` | Muted/disabled text |
| `accent-orange` | `#FF8C00` | Bloomberg orange (primary accent) |
| `accent-blue` | `#3B82F6` | Links, selections |
| `status-success` | `#22C55E` | Positive values, connected |
| `status-danger` | `#EF4444` | Negative values, errors |
| `status-warning` | `#F59E0B` | Warnings, pending |
| `status-info` | `#06B6D4` | Informational |
| `chart-green` | `#00D68F` | Positive chart lines |
| `chart-red` | `#FF3D71` | Negative chart lines |
| `chart-blue` | `#0095FF` | Neutral chart lines |
| `chart-purple` | `#A855F7` | Compute units |
| `chart-cyan` | `#22D3EE` | Fees |

---

## 3. Component Architecture

### 3.1 Atomic Design Structure

```
src/
├── components/
│   ├── atoms/           # Smallest building blocks
│   │   ├── Badge/
│   │   ├── Button/
│   │   ├── Icon/
│   │   ├── Indicator/
│   │   ├── Label/
│   │   ├── Separator/
│   │   ├── Shimmer/      # Loading skeleton
│   │   ├── StatusDot/
│   │   ├── Text/
│   │   └── Toggle/
│   │
│   ├── molecules/       # Composed from atoms
│   │   ├── Card/
│   │   ├── CardHeader/
│   │   ├── CardStat/
│   │   ├── Dropdown/
│   │   ├── IdentityKey/
│   │   ├── NavLink/
│   │   ├── PeerIcon/
│   │   ├── ProgressBar/
│   │   ├── SearchInput/
│   │   ├── SlotBadge/
│   │   ├── StatValue/
│   │   ├── ToastMessage/
│   │   └── ToggleGroup/
│   │
│   ├── organisms/       # Complex, self-contained components
│   │   ├── ChartCard/         # Generic chart wrapper
│   │   ├── ClusterSelector/
│   │   ├── ComputeUnitsChart/
│   │   ├── DataGrid/          # AG Grid wrapper
│   │   ├── EpochBar/
│   │   ├── Header/
│   │   ├── LeaderScheduleGrid/
│   │   ├── SankeyDiagram/
│   │   ├── SlotCard/
│   │   ├── SlotPerformanceTiles/
│   │   ├── SlotSelector/
│   │   ├── SparkLine/
│   │   ├── StatusCard/
│   │   ├── TransactionBarsChart/
│   │   ├── TransactionsCard/
│   │   └── ValidatorsCard/
│   │
│   └── compositions/   # Full page sections / layouts
│       ├── DashboardOverview/
│       ├── GossipView/
│       ├── LeaderScheduleView/
│       ├── SlotDetailsView/
│       ├── StartupProgressView/
│       └── AppShell/         # Header + EpochBar + Content layout
│
├── design-system/
│   ├── tokens/
│   ├── theme/
│   │   ├── BloombergThemeProvider.tsx
│   │   └── globalStyles.css
│   └── fonts/
│
├── hooks/              # Shared hooks
│   ├── useEventListener.ts
│   ├── useIdentityPeer.ts
│   ├── useNavigateLeaderSlot.ts
│   ├── useNextSlot.ts
│   ├── usePeer.ts
│   ├── usePing.ts
│   ├── usePubKey.ts
│   └── useSlotQuery.ts
│
├── lib/                # Utilities & business logic
│   ├── atoms/          # Jotai state (decoupled from WebSocket)
│   ├── api/            # API types and mocks
│   ├── utils/
│   ├── charts/         # uPlot plugins & config
│   ├── d3Sankey/
│   └── sankey/
│
├── mocks/              # Mock data for Storybook
│   ├── epoch.ts
│   ├── peers.ts
│   ├── slots.ts
│   ├── transactions.ts
│   ├── validators.ts
│   ├── startup.ts
│   └── websocket.ts
│
├── app/                # Next.js App Router pages
│   ├── layout.tsx
│   ├── page.tsx           # → Overview
│   ├── gossip/
│   ├── leader-schedule/
│   ├── slot-details/
│   └── about/
│
└── stories/            # Co-located or centralized stories
```

### 3.2 Component Extraction Map

**From `src/components/` (9 base components):**

| Original | → Atom/Molecule | Notes |
|----------|----------------|-------|
| `Card.tsx` | `molecules/Card` | Add Bloomberg border treatment |
| `CardHeader.tsx` | `molecules/CardHeader` | Amber accent underline |
| `CardStat.tsx` | `molecules/CardStat` | Monospace value display |
| `Dropdown.tsx` | `molecules/Dropdown` | Dark flat styling |
| `PopoverDropdown.tsx` | `molecules/Dropdown` (variant) | Merge with Dropdown |
| `ArrowDropdown.tsx` | `molecules/Dropdown` (variant) | Merge with Dropdown |
| `PeerIcon.tsx` | `molecules/PeerIcon` | Keep identicon style |
| `RowSeparator.tsx` | `atoms/Separator` | Subtle border line |
| `ToggleControl.tsx` | `atoms/Toggle` | Bloomberg toggle style |

**From `src/features/Header/` (7 components):**

| Original | → Target | Notes |
|----------|----------|-------|
| `index.tsx` | `organisms/Header` | Bloomberg top-bar |
| `Logo.tsx` | `atoms/Logo` | Minimal, clean |
| `NavLinks.tsx` | `organisms/Header/NavLinks` | Tab-style navigation |
| `Cluster.tsx` | `organisms/ClusterSelector` | Status-colored indicator |
| `ClusterIndicator.tsx` | `atoms/StatusDot` | Colored dot component |
| `IdentityKey.tsx` | `molecules/IdentityKey` | Truncated key display |
| `MenuNavLinks.tsx` | `organisms/Header/MobileMenu` | Responsive menu |

**From `src/features/Overview/` (major dashboard):**

| Original | → Target | Notes |
|----------|----------|-------|
| `StatusCard/` | `organisms/StatusCard` | Connection status with indicator |
| `TransactionsCard/` | `organisms/TransactionsCard` | Chart + stats combo |
| `ValidatorsCard/` | `organisms/ValidatorsCard` | Pie chart + stats |
| `SlotPerformance/` | `organisms/SlotPerformanceTiles` | Tile grid with sparklines |
| `SlotPerformance/ComputeUnitsCard/` | `organisms/ComputeUnitsChart` | uPlot line chart |
| `SlotPerformance/TransactionBarsCard/` | `organisms/TransactionBarsChart` | Custom bar chart |
| `SlotPerformance/SlotSankey/` | `organisms/SankeyDiagram` | D3 Sankey flow |

**From `src/features/LeaderSchedule/` (36 files):**

| Original | → Target | Notes |
|----------|----------|-------|
| `index.tsx` | `compositions/LeaderScheduleView` | Full page composition |
| `Slots/SlotCardList.tsx` | `organisms/LeaderScheduleGrid` | Card list view |
| `Slots/CurrentSlotCard.tsx` | `organisms/SlotCard` (variant: current) | Active slot highlight |
| `Slots/PastSlotCard.tsx` | `organisms/SlotCard` (variant: past) | Dimmed past state |
| `Slots/UpcomingSlotCard.tsx` | `organisms/SlotCard` (variant: upcoming) | Future slot |

**From `src/features/EpochBar/` (6 components):**

| Original | → Target | Notes |
|----------|----------|-------|
| `index.tsx` | `organisms/EpochBar` | Full epoch bar |
| `EpochBarLive.tsx` | Part of `EpochBar` | Live progress indicator |
| `EpochSlider.tsx` | Subcomponent | Custom slider |
| `NavigateNext/Prev.tsx` | `atoms/Button` (icon variant) | Arrow navigation |

**From `src/features/StartupProgress/` (13 files):**

| Original | → Target | Notes |
|----------|----------|-------|
| `index.tsx` | `compositions/StartupProgressView` | Full startup overlay |
| `CompleteStep.tsx` | `molecules/ProgressStep` (variant) | Check + label |
| `InprogressStep.tsx` | `molecules/ProgressStep` (variant) | Animated progress |
| `IncompleteStep.tsx` | `molecules/ProgressStep` (variant) | Dimmed step |
| `SnapshotProgress.tsx` | `molecules/ProgressBar` | Linear progress |
| `ValueDisplay.tsx` | `atoms/StatValue` | Formatted value |

**From `src/features/Toast/`:**

| Original | → Target | Notes |
|----------|----------|-------|
| `index.tsx` | `molecules/ToastMessage` | Bloomberg-style alert bar |

**From `src/features/Gossip/`:**

| Original | → Target | Notes |
|----------|----------|-------|
| `index.tsx` + `Grid.tsx` | `compositions/GossipView` + `organisms/DataGrid` | AG Grid wrapper |

---

## 4. Storybook Setup

### 4.1 Storybook 8 Configuration

```
.storybook/
├── main.ts            # Storybook config
├── preview.ts         # Global decorators, parameters
├── manager.ts         # UI theme (Bloomberg dark)
├── preview-head.html  # Font imports
└── bloomberg-theme.ts # Custom Storybook theme
```

### 4.2 Addons

| Addon | Purpose |
|-------|---------|
| `@storybook/addon-essentials` | Controls, Actions, Viewport, Backgrounds, Docs |
| `@storybook/addon-interactions` | Play functions for interaction testing |
| `@storybook/addon-a11y` | Accessibility auditing |
| `@storybook/addon-links` | Story-to-story navigation |
| `@storybook/addon-viewport` | Responsive viewport testing |
| `@storybook/addon-measure` | Inspect layout measurements |
| `@storybook/addon-outline` | Visual component boundaries |
| `@storybook/addon-themes` | Theme switching (Bloomberg dark) |
| `@storybook/test` | Built-in testing (vitest compatible) |
| `storybook-addon-mock` | Mock API/WebSocket responses |
| `msw-storybook-addon` | MSW integration for network mocking |

### 4.3 Story Structure (per component)

Each component gets:

```
ComponentName/
├── ComponentName.tsx          # Component implementation
├── ComponentName.module.css   # Bloomberg-styled CSS module
├── ComponentName.stories.tsx  # Storybook stories
├── ComponentName.test.tsx     # Unit tests (optional)
└── index.ts                   # Barrel export
```

### 4.4 Mock Data Strategy

Since the original app is WebSocket-driven, all Storybook stories need mock data:

- **Jotai Provider Decorator**: Wraps stories with pre-populated atom values
- **Mock WebSocket**: Simulated connection for real-time components
- **Static Fixtures**: JSON fixtures for slots, epochs, peers, transactions
- **MSW Handlers**: Mock service worker for any HTTP requests
- **Story Args**: Storybook Controls for interactive prop manipulation

---

## 5. Implementation Phases

### Phase 1: Project Scaffolding
1. Initialize Next.js 14 project with TypeScript
2. Configure `next.config.js` with WASM support
3. Set up path aliases (`@/components`, `@/lib`, `@/hooks`, etc.)
4. Port `tsconfig.json` settings
5. Install core dependencies (Radix UI primitives, Jotai, lodash, luxon, etc.)
6. Set up ESLint + Prettier (match existing config)
7. Set up CSS Modules with `camelCaseOnly` convention

### Phase 2: Design System Foundation
1. Create Bloomberg design token system (colors, typography, spacing, borders)
2. Create `BloombergThemeProvider` component
3. Set up global CSS with CSS custom properties
4. Create base reset styles
5. Port and remap the 147 existing colors to Bloomberg tokens
6. Configure fonts (`Roboto Mono`, `Inter Tight`)

### Phase 3: Storybook Setup
1. Install Storybook 8 for Next.js
2. Configure all addons
3. Create Bloomberg dark theme for Storybook manager UI
4. Set up global decorators (theme provider, Jotai provider)
5. Create mock data fixtures
6. Set up MSW for network mocking
7. Configure viewport presets (1080p, 1440p, 4K - dense layouts)

### Phase 4: Atom Components (10 components)
Extract and restyle the smallest building blocks:
- Badge, Button, Icon, Indicator, Label
- Separator, Shimmer, StatusDot, Text, Toggle

Each with: Bloomberg styling + Storybook story + Controls

### Phase 5: Molecule Components (13 components)
Compose atoms into functional molecules:
- Card, CardHeader, CardStat, Dropdown
- IdentityKey, NavLink, PeerIcon, ProgressBar
- SearchInput, SlotBadge, StatValue, ToastMessage, ToggleGroup

Each with: Bloomberg styling + Storybook story + Controls + mock data

### Phase 6: Organism Components (15 components)
Build complex, self-contained components:
- ChartCard, ClusterSelector, ComputeUnitsChart
- DataGrid, EpochBar, Header
- LeaderScheduleGrid, SankeyDiagram, SlotCard
- SlotPerformanceTiles, SlotSelector, SparkLine
- StatusCard, TransactionsCard, ValidatorsCard

Each with: Bloomberg styling + Storybook story + mock data + interactions

### Phase 7: Composition Components (6 compositions)
Full page-level compositions:
- DashboardOverview, GossipView, LeaderScheduleView
- SlotDetailsView, StartupProgressView, AppShell

Each with: Storybook story showing complete layouts

### Phase 8: Next.js Pages & Routing
1. Create `app/layout.tsx` with BloombergThemeProvider
2. Create page routes using App Router
3. Wire compositions into pages
4. Implement client-side data fetching with Jotai
5. Configure WebSocket connection provider

### Phase 9: Utilities & Business Logic
1. Port all utility functions (`utils.ts`, `mathUtils.ts`, `numUtils.ts`, etc.)
2. Port custom hooks
3. Port Jotai atom definitions (decoupled from direct WS dependency)
4. Port uPlot plugins and chart configuration
5. Port D3 Sankey implementation

### Phase 10: Integration & Polish
1. Verify all Storybook stories render correctly
2. Run accessibility audits via `addon-a11y`
3. Verify responsive behavior at all viewport presets
4. Ensure all mock data scenarios are covered
5. Final Bloomberg design QA pass

---

## 6. Execution Strategy (Sub-Agent Workflow)

### Parallel Agent Deployment

The implementation will use multiple specialized sub-agents running in parallel where possible:

```
Phase 1-2 (Sequential):
  └─ Agent: "scaffolding"
       → Next.js init + Design system tokens + Theme provider

Phase 3 (Sequential, depends on 1-2):
  └─ Agent: "storybook-setup"
       → Storybook install + config + addons + decorators + mock infrastructure

Phase 4-5 (Parallel after Phase 3):
  ├─ Agent: "atoms"        → All atom components + stories
  └─ Agent: "molecules"    → All molecule components + stories

Phase 6 (Parallel, batched after Phase 4-5):
  ├─ Agent: "organisms-charts"    → Chart components (uPlot, Sankey, SparkLine)
  ├─ Agent: "organisms-cards"     → Card-based organisms (Status, Txn, Validators)
  └─ Agent: "organisms-layout"    → Layout organisms (Header, EpochBar, Grid, SlotCard)

Phase 7 (Sequential after Phase 6):
  └─ Agent: "compositions"
       → All composition components + full-page stories

Phase 8-9 (Parallel after Phase 7):
  ├─ Agent: "nextjs-pages"   → App Router pages + routing
  └─ Agent: "utilities"      → Utils, hooks, atoms, chart plugins

Phase 10 (Sequential, final):
  └─ Agent: "integration"
       → QA, accessibility, design review, final polish
```

---

## 7. Key Technical Decisions

### 7.1 Why Next.js 14 App Router
- Server Components for static parts (About, layout chrome)
- Client Components for real-time data (dashboards, charts)
- Built-in image optimization, font optimization
- Future: SSR for initial page load, ISR for cached data

### 7.2 Jotai State Strategy
- Keep Jotai as state manager (minimal migration)
- Decouple atoms from WebSocket (inject via providers)
- Create `MockAtomProvider` for Storybook
- All WebSocket-dependent atoms get mock counterparts

### 7.3 Chart Library Strategy
- Keep uPlot (lightweight, performant for real-time)
- Wrap in `"use client"` components
- Create Storybook-compatible wrappers with static data
- D3 Sankey: port custom implementation as-is

### 7.4 Bloomberg Styling Approach
- CSS Modules (keep existing convention)
- CSS Custom Properties for all design tokens
- Global theme via `BloombergThemeProvider`
- Replace Radix Themes with Radix Primitives (more control)
- Sharp edges, flat design, high-density layouts

---

## 8. File Count Estimates

| Category | Estimated Files |
|----------|----------------|
| Design tokens & theme | ~10 |
| Atom components (+ stories) | ~30 |
| Molecule components (+ stories) | ~40 |
| Organism components (+ stories) | ~60 |
| Composition components (+ stories) | ~20 |
| Mock data | ~10 |
| Storybook config | ~6 |
| Next.js pages & layout | ~8 |
| Utilities & hooks | ~20 |
| Chart plugins | ~15 |
| **Total new/modified files** | **~220** |

---

## 9. Dependencies to Install

### Production
```
next@14
react@18 react-dom@18
@radix-ui/react-dropdown-menu
@radix-ui/react-popover
@radix-ui/react-toggle
@radix-ui/react-toggle-group
@radix-ui/react-tooltip
@radix-ui/react-dialog
@radix-ui/react-icons
jotai jotai-immer jotai-optics
immer
uplot
@nivo/core @nivo/pie
@ag-grid-community/react @ag-grid-community/core
@ag-grid-community/client-side-row-model
@ag-grid-community/csv-export @ag-grid-community/styles
react-circular-progressbar
lodash luxon zod clsx
hammerjs micro-memoize use-debounce react-use
react-intersection-observer react-virtualized-auto-sizer
cmdk
@fontsource/inter-tight @fontsource/roboto-mono
@oneidentity/zstd-js
```

### Development
```
typescript @types/react @types/react-dom @types/lodash @types/luxon
@storybook/react @storybook/nextjs
@storybook/addon-essentials @storybook/addon-interactions
@storybook/addon-a11y @storybook/addon-links
@storybook/addon-viewport @storybook/addon-measure
@storybook/addon-outline @storybook/addon-themes
@storybook/test @storybook/blocks
storybook-addon-mock msw msw-storybook-addon
eslint prettier eslint-config-next
vitest @testing-library/react
```

---

## 10. Success Criteria

- [ ] All 44+ components extracted and Bloomberg-styled
- [ ] All components have Storybook stories with controls
- [ ] Mock data covers all major data scenarios
- [ ] Storybook builds without errors
- [ ] Next.js app builds without errors
- [ ] Accessibility audit passes (no critical issues)
- [ ] Responsive at 1080p, 1440p, 4K viewports
- [ ] Bloomberg aesthetic consistently applied across all components
