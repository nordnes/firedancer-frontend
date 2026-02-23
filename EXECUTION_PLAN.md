# Execution Plan: Single-Session Build

## Orchestration Model

```
ORCHESTRATOR (me)
├── Judges: Orchestrator reviews every phase output before proceeding
│
├── Phase 1: SCAFFOLDING [sequential]
│   └── Agent: scaffold-nextjs
│       - Initialize Next.js 14 inside existing repo (co-exist with Vite)
│       - Install all production + dev dependencies
│       - Configure tsconfig, next.config.js, path aliases
│       - Set up CSS Modules with Bloomberg conventions
│
├── Phase 2: DESIGN SYSTEM [sequential, depends on Phase 1]
│   └── Agent: design-system
│       - Create tokens/ directory (colors, typography, spacing, borders)
│       - Map all 147 existing colors → Bloomberg tokens
│       - Create BloombergThemeProvider
│       - Create global CSS with CSS custom properties
│       - Configure fonts (Roboto Mono, Inter Tight)
│
├── Phase 3: STORYBOOK [sequential, depends on Phase 2]
│   └── Agent: storybook-setup
│       - Install Storybook 8 for Next.js
│       - Configure main.ts, preview.ts, manager.ts
│       - Create Bloomberg dark theme for Storybook UI
│       - Set up global decorators (theme, Jotai providers)
│       - Create mock data fixtures (epoch, peers, slots, transactions)
│       - Configure viewport presets
│
├── Phase 4-5: ATOMS + MOLECULES [parallel, depends on Phase 3]
│   ├── Agent: atoms-batch-1 (Badge, Button, Icon, Indicator, Label)
│   ├── Agent: atoms-batch-2 (Separator, Shimmer, StatusDot, Text, Toggle)
│   ├── Agent: molecules-batch-1 (Card, CardHeader, CardStat, Dropdown, IdentityKey, NavLink, PeerIcon)
│   └── Agent: molecules-batch-2 (ProgressBar, SearchInput, SlotBadge, StatValue, ToastMessage, ToggleGroup)
│
│   JUDGE: Verify all 23 components build, stories render, consistent styling
│
├── Phase 6: ORGANISMS [parallel batches, depends on Phase 4-5]
│   ├── Agent: organisms-charts (ChartCard, ComputeUnitsChart, SparkLine, SankeyDiagram, TransactionBarsChart)
│   ├── Agent: organisms-cards (StatusCard, TransactionsCard, ValidatorsCard, SlotCard, SlotPerformanceTiles)
│   └── Agent: organisms-layout (Header, EpochBar, LeaderScheduleGrid, DataGrid, ClusterSelector, SlotSelector)
│
│   JUDGE: Verify all 16 organisms build, stories render, compositions work
│
├── Phase 7: COMPOSITIONS + PAGES + UTILS [parallel, depends on Phase 6]
│   ├── Agent: compositions (DashboardOverview, GossipView, LeaderScheduleView, SlotDetailsView, StartupProgressView, AppShell)
│   ├── Agent: nextjs-pages (layout.tsx, all page routes, WebSocket provider)
│   └── Agent: utilities (port all utils, hooks, atoms, chart plugins, D3 Sankey)
│
│   JUDGE: Verify full app builds, all stories render
│
└── Phase 8: INTEGRATION [sequential, final]
    └── Agent: integration-qa
        - Run `next build` and fix errors
        - Run `storybook build` and fix errors
        - Verify component count matches plan
        - Final commit and push
```

## Detailed Phase Specifications

### Phase 1: Scaffolding

**Goal**: Next.js 14 project initialized alongside existing Vite setup.

**Actions**:
1. Create `next.config.js` with:
   - WASM support (for zstd-js)
   - CSS Modules with camelCase
   - Transpile packages (uplot, ag-grid, etc.)
2. Create `app/` directory for Next.js App Router
3. Update `tsconfig.json` with Next.js paths + existing aliases
4. Install missing deps: `next`, `@storybook/*`, Radix primitives
5. Add scripts: `dev:next`, `build:next`, `storybook`, `build-storybook`

**Files created**: ~5 config files
**Estimated scope**: Small

### Phase 2: Design System

**Goal**: Complete Bloomberg-style design token system.

**Actions**:
1. `src/design-system/tokens/colors.ts` - Full color palette mapping existing 147 → Bloomberg
2. `src/design-system/tokens/typography.ts` - Font system
3. `src/design-system/tokens/spacing.ts` - 4px grid
4. `src/design-system/tokens/borders.ts` - Border tokens
5. `src/design-system/tokens/index.ts` - Re-exports
6. `src/design-system/theme/BloombergThemeProvider.tsx` - Context provider
7. `src/design-system/theme/globalStyles.css` - CSS custom properties + reset
8. `src/design-system/theme/bloomberg.css` - Component-level tokens

**Files created**: ~10
**Estimated scope**: Medium

### Phase 3: Storybook

**Goal**: Storybook 8 running with Bloomberg theme, mock data, and all decorators.

**Actions**:
1. `.storybook/main.ts` - Framework config
2. `.storybook/preview.ts` - Global decorators (theme, Jotai, viewport)
3. `.storybook/manager.ts` - Bloomberg dark UI theme
4. `.storybook/bloomberg-theme.ts` - Custom theme object
5. `.storybook/preview-head.html` - Font imports
6. `src/mocks/` - All mock data fixtures
7. `src/mocks/MockProviders.tsx` - Wrapper for Jotai + Theme in stories

**Files created**: ~15
**Estimated scope**: Medium

### Phase 4: Atoms (10 components)

Each component = `.tsx` + `.module.css` + `.stories.tsx` + `index.ts`

| Component | Source Mapping | Key Props |
|-----------|---------------|-----------|
| Badge | New (from slot status patterns) | variant, label, size |
| Button | New (NavigateNext/Prev patterns) | variant, size, icon, disabled |
| Icon | Radix icons wrapper | name, size, color |
| Indicator | ClusterIndicator.tsx | color, pulse, size |
| Label | From header/card patterns | text, variant, weight |
| Separator | RowSeparator.tsx | orientation, color |
| Shimmer | New (loading skeleton) | width, height, variant |
| StatusDot | From ClusterIndicator | status, size |
| Text | New (typography wrapper) | variant, color, mono, weight |
| Toggle | ToggleControl.tsx | checked, label, onChange |

**Files created**: ~40
**Estimated scope**: Medium

### Phase 5: Molecules (13 components)

| Component | Source Mapping | Key Props |
|-----------|---------------|-----------|
| Card | Card.tsx | title, children, padding |
| CardHeader | CardHeader.tsx | title, action, icon |
| CardStat | CardStat.tsx | label, value, trend, color |
| Dropdown | Dropdown.tsx + PopoverDropdown + ArrowDropdown | items, selected, onChange |
| IdentityKey | features/Header/IdentityKey.tsx | pubkey, truncate, copyable |
| NavLink | From NavLinks patterns | href, label, active, icon |
| PeerIcon | PeerIcon.tsx | pubkey, size |
| ProgressBar | From StartupProgress patterns | value, max, label, color |
| SearchInput | From LeaderSchedule search | placeholder, value, onChange |
| SlotBadge | From slot card patterns | slot, status, variant |
| StatValue | From ValueDisplay patterns | value, label, unit, trend |
| ToastMessage | features/Toast/ | type, message, onDismiss |
| ToggleGroup | From toggle group patterns | items, selected, onChange |

**Files created**: ~52
**Estimated scope**: Large

### Phase 6: Organisms (16 components)

| Component | Source | Complexity |
|-----------|--------|------------|
| ChartCard | New wrapper | Low |
| ClusterSelector | features/Header/Cluster.tsx | Medium |
| ComputeUnitsChart | features/Overview/SlotPerformance/ComputeUnitsCard | High |
| DataGrid | AG Grid wrapper from Gossip | High |
| EpochBar | features/EpochBar/ | High |
| Header | features/Header/ | Medium |
| LeaderScheduleGrid | features/LeaderSchedule/ | High |
| SankeyDiagram | src/sankey/ + src/d3Sankey/ | High |
| SlotCard | features/LeaderSchedule/Slots/ | Medium |
| SlotPerformanceTiles | features/Overview/SlotPerformance/ | High |
| SlotSelector | From slot navigation patterns | Medium |
| SparkLine | From tile sparklines | Medium |
| StatusCard | features/Overview/StatusCard/ | Medium |
| TransactionBarsChart | features/Overview/SlotPerformance/TransactionBarsCard | High |
| TransactionsCard | features/Overview/TransactionsCard/ | Medium |
| ValidatorsCard | features/Overview/ValidatorsCard/ | Medium |

**Files created**: ~64
**Estimated scope**: Very Large (most work)

### Phase 7: Compositions + Pages + Utils

**Compositions** (6):
- DashboardOverview, GossipView, LeaderScheduleView
- SlotDetailsView, StartupProgressView, AppShell

**Next.js Pages** (6):
- layout.tsx, page.tsx (overview), gossip/page.tsx
- leader-schedule/page.tsx, slot-details/page.tsx, about/page.tsx

**Utilities** (~20 files):
- Port utils.ts, mathUtils.ts, numUtils.ts, transactionUtils.ts
- Port all 8 hooks
- Port Jotai atoms (decoupled)
- Port uPlot plugins
- Port D3 Sankey

**Files created**: ~50
**Estimated scope**: Large

### Phase 8: Integration & QA

1. `next build` passes
2. `storybook build` passes
3. All component barrel exports work
4. Commit and push

## Total Estimated Files: ~230

## Parallelization Summary

| Phase | Agents | Parallel? |
|-------|--------|-----------|
| 1 | 1 | No (foundation) |
| 2 | 1 | No (depends on 1) |
| 3 | 1 | No (depends on 2) |
| 4-5 | 4 | YES (all parallel) |
| 6 | 3 | YES (all parallel) |
| 7 | 3 | YES (all parallel) |
| 8 | 1 | No (final check) |
| **Total** | **14 agents** | |
