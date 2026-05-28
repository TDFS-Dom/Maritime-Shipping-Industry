# DESIGN.md — Digital Bunkering Platform

## Metadata

| Key | Value |
|---|---|
| Project | Digital Bunkering Platform |
| Slug | digital-bunkering-platform |
| Date | 2026-05-27 |
| Owner | Design Lead |
| Status | Active |
| Reference | Professional B2B SaaS (maritime operations) |

---

## 0. Phạm vi áp dụng

Design system này áp dụng cho toàn bộ 4 portals:
- **P-SUPP:** Supplier Portal (Web — desktop-first, responsive)
- **P-OPS:** Operations Portal (Mobile/Tablet — touch-first)
- **P-VESSEL:** Vessel Portal (Mobile — minimal, signing-focused)
- **P-BUYER:** Buyer Portal (Web — desktop-first, responsive)

---

## 1. Visual Theme & Atmosphere

**Mood:** Professional, trustworthy, clean. Hàng hải nhưng không cổ điển — hiện đại, data-dense, efficient.

**Design Philosophy:**
- Data-first: dashboards và tables phải readable tại 1440px desktop
- Operational clarity: status indicators rõ ràng, không ambiguous
- Compliance confidence: mọi action đều có audit trail visible
- Mobile efficiency: field users (barge/vessel) cần hoàn thành task trong <30 giây mỗi step

**Không dùng:**
- Decorative illustrations, animations phức tạp
- Gradients trên surfaces
- Rounded corners quá lớn (>12px)
- Dark theme (operations cần high-visibility)

---

## 2. Information Architecture (Portals & Navigation)

### 2.1 Portal Summary

| Portal ID | Portal/App | Target Actor | Owned Screen Families | Notes |
|---|---|---|---|---|
| P-SUPP | Supplier Portal | Supplier Admin, Compliance, Finance | Dashboard, Nominations, Scheduling, Deliveries, eBDN, Compliance, Finance, Analytics, Settings | Primary management hub |
| P-OPS | Operations Portal | Barge Operator | Active Deliveries, Checklists, MFM Monitor, eBDN Sign | Mobile-first, touch optimized |
| P-VESSEL | Vessel Portal | Chief Engineer | Upcoming, eBDN Review & Sign, History | Minimal, signing-focused |
| P-BUYER | Buyer Portal | Shipowner/Operator | Nominations, Tracking, eBDN Archive, Invoices | Self-service ordering |

### 2.2 Navigation Schema

| Portal ID | Nav Schema | Pattern | Menu Items | Default Landing |
|---|---|---|---|---|
| P-SUPP | Side nav (collapsible) | Vertical sidebar + top bar | Dashboard, Nominations, Scheduling, Deliveries, eBDN, Compliance, Finance, Analytics, Settings | Dashboard |
| P-OPS | Bottom tab bar | 4-tab mobile nav | Active, Checklists, MFM, eBDN | Active Deliveries |
| P-VESSEL | Bottom tab bar | 3-tab mobile nav | Upcoming, Sign, History | Upcoming |
| P-BUYER | Side nav (collapsible) | Vertical sidebar + top bar | Nominations, Tracking, eBDN, Invoices | Nominations |

---

## 3. Color Palette & Roles

### 3.1 Core Colors

| Role | Hex | CSS Variable | Usage |
|---|---|---|---|
| Primary | `#1E40AF` | `--color-primary` | CTAs, active nav, links, primary actions |
| Primary Hover | `#1D4ED8` | `--color-primary-hover` | Hover states |
| Primary Light | `#DBEAFE` | `--color-primary-light` | Selected rows, tags background |
| Secondary | `#0F766E` | `--color-secondary` | Secondary actions, success-adjacent |
| Accent | `#F59E0B` | `--color-accent` | Warnings, attention items |

### 3.2 Status Colors

| Status | Hex | Usage |
|---|---|---|
| Success / Active | `#16A34A` | Completed, confirmed, passed, CLEAR |
| Warning | `#D97706` | Approaching deadline, partial, POTENTIAL_MATCH |
| Error / Critical | `#DC2626` | Overdue, failed, rejected, CONFIRMED_MATCH |
| Info | `#2563EB` | Informational, pending, in-progress |
| Neutral | `#6B7280` | Cancelled, voided, inactive |

### 3.3 Surfaces

| Role | Hex | Usage |
|---|---|---|
| Background | `#F9FAFB` | Page background |
| Surface | `#FFFFFF` | Cards, panels, modals |
| Surface Elevated | `#FFFFFF` + shadow | Dropdowns, popovers |
| Border | `#E5E7EB` | Card borders, dividers |
| Border Strong | `#D1D5DB` | Input borders, table borders |

### 3.4 Text Colors

| Role | Hex | Usage |
|---|---|---|
| Primary Text | `#111827` | Headings, body, values |
| Secondary Text | `#6B7280` | Labels, descriptions, placeholders |
| Muted Text | `#9CA3AF` | Timestamps, minor info |
| Inverse Text | `#FFFFFF` | On dark/colored backgrounds |

---

## 4. Typography Rules

| Level | Font | Size | Weight | Line Height | Usage |
|---|---|---|---|---|---|
| H1 (Page Title) | Inter | 24px | 700 (Bold) | 32px | Page headings |
| H2 (Section) | Inter | 20px | 600 (SemiBold) | 28px | Section headings |
| H3 (Card Title) | Inter | 16px | 600 (SemiBold) | 24px | Card/panel titles |
| Body | Inter | 14px | 400 (Regular) | 20px | Default body text |
| Body Small | Inter | 12px | 400 (Regular) | 16px | Table cells, secondary info |
| Label | Inter | 12px | 500 (Medium) | 16px | Form labels, column headers |
| Caption | Inter | 11px | 400 (Regular) | 14px | Timestamps, annotations |
| Button | Inter | 14px | 500 (Medium) | 20px | Button text |
| Mobile Body | Inter | 16px | 400 (Regular) | 24px | Mobile body text (larger for touch) |
| Mobile H1 | Inter | 20px | 700 (Bold) | 28px | Mobile page titles |

**Font:** Inter (Google Fonts) — universal, excellent readability at small sizes.

---

## 5. Component Stylings

### 5.1 Buttons

| Variant | Background | Text | Border | Radius | Padding |
|---|---|---|---|---|---|
| Primary | `#1E40AF` | White | None | 8px | 10px 16px |
| Secondary | White | `#1E40AF` | 1px `#1E40AF` | 8px | 10px 16px |
| Ghost | Transparent | `#6B7280` | None | 8px | 10px 16px |
| Danger | `#DC2626` | White | None | 8px | 10px 16px |
| Disabled | `#E5E7EB` | `#9CA3AF` | None | 8px | 10px 16px |

### 5.2 Cards

| Property | Value |
|---|---|
| Background | `#FFFFFF` |
| Border | 1px `#E5E7EB` |
| Border Radius | 8px |
| Padding | 20px (desktop), 16px (mobile) |
| Shadow | None (flat design) |
| Hover (if clickable) | Border → `#D1D5DB` |

### 5.3 Tables (Data Tables)

| Property | Value |
|---|---|
| Header Background | `#F9FAFB` |
| Header Text | `#6B7280`, 12px, Medium (500) |
| Row Border | 1px `#F3F4F6` (bottom only) |
| Row Hover | `#F9FAFB` |
| Selected Row | `#DBEAFE` |
| Cell Padding | 12px 16px |

### 5.4 Status Badges

| Property | Value |
|---|---|
| Border Radius | 9999px (pill) |
| Padding | 2px 8px |
| Font | 12px, Medium (500) |
| Dot Indicator | 6px circle before text |
| Colors | Match §3.2 Status Colors (bg = light variant, text = dark variant) |

### 5.5 Forms

| Property | Value |
|---|---|
| Input Height | 40px (desktop), 48px (mobile) |
| Input Border | 1px `#D1D5DB` |
| Input Radius | 6px |
| Input Focus | Border `#1E40AF`, ring 2px `#DBEAFE` |
| Input Error | Border `#DC2626`, helper text red |
| Label | 12px Medium, `#374151`, margin-bottom 4px |
| Helper Text | 12px Regular, `#6B7280` |
| Error Text | 12px Regular, `#DC2626` |

### 5.6 Modals & Dialogs

| Property | Value |
|---|---|
| Overlay | `#000000` 50% opacity |
| Background | White |
| Border Radius | 12px |
| Padding | 24px |
| Max Width | 480px (small), 640px (medium), 960px (large) |
| Header | H3 + close button (top-right) |
| Footer | Actions right-aligned, gap 12px |

### 5.7 Notifications/Toasts

| Variant | Left Border Color | Icon | Duration |
|---|---|---|---|
| Success | `#16A34A` | ✓ (checkmark) | 5s auto-dismiss |
| Warning | `#D97706` | ⚠ (triangle) | 8s auto-dismiss |
| Error | `#DC2626` | ✕ (x-circle) | Persistent until dismissed |
| Info | `#2563EB` | ℹ (info) | 5s auto-dismiss |

---

## 6. Layout Principles

### 6.1 Desktop (P-SUPP, P-BUYER)

```
┌─────────────────────────────────────────────────────────────┐
│ Top Bar (56px): Logo | Workspace Selector | Notifications | User │
├──────────┬──────────────────────────────────────────────────┤
│ Side Nav │ Content Area                                      │
│ (240px)  │ ┌────────────────────────────────────────────┐   │
│          │ │ Page Header (title + actions)               │   │
│ Dashboard│ ├────────────────────────────────────────────┤   │
│ Nominat. │ │ Filters / Search Bar                       │   │
│ Schedule │ ├────────────────────────────────────────────┤   │
│ Deliver. │ │ Main Content (table / cards / form)        │   │
│ eBDN     │ │                                            │   │
│ Complian.│ │                                            │   │
│ Finance  │ │                                            │   │
│ Analytic │ ├────────────────────────────────────────────┤   │
│ Settings │ │ Pagination                                  │   │
│          │ └────────────────────────────────────────────┘   │
└──────────┴──────────────────────────────────────────────────┘
```

- Content max-width: 1280px (centered if viewport wider)
- Side nav collapsible to 64px (icon-only)
- Breakpoint: collapse at 1024px

### 6.2 Mobile (P-OPS, P-VESSEL)

```
┌──────────────────────────┐
│ Status Bar (device)      │
├──────────────────────────┤
│ App Bar (56px):          │
│ [Back] Title [Action]    │
├──────────────────────────┤
│                          │
│ Content Area             │
│ (scrollable)             │
│                          │
│                          │
│                          │
├──────────────────────────┤
│ Bottom Tab Bar (64px)    │
│ [Tab1] [Tab2] [Tab3]    │
└──────────────────────────┘
```

- Safe area: respect notch + home indicator
- Touch targets: minimum 44×44px
- Content padding: 16px horizontal
- Card spacing: 12px between cards

### 6.3 Spacing Scale

| Token | Value | Usage |
|---|---|---|
| xs | 4px | Icon-text gap |
| sm | 8px | Tight spacing within components |
| md | 12px | Card internal padding (mobile), item gaps |
| lg | 16px | Section spacing, card padding (mobile) |
| xl | 20px | Card padding (desktop) |
| 2xl | 24px | Section margins |
| 3xl | 32px | Page section gaps |
| 4xl | 48px | Major layout gaps |

---

## 7. Depth & Elevation

| Level | Box Shadow | Usage |
|---|---|---|
| 0 (Flat) | None | Cards, panels (default) |
| 1 (Low) | `0 1px 2px rgba(0,0,0,0.05)` | Dropdowns trigger, hover |
| 2 (Medium) | `0 4px 6px rgba(0,0,0,0.07)` | Dropdowns open, popovers |
| 3 (High) | `0 10px 15px rgba(0,0,0,0.1)` | Modals, sheets |

**Philosophy:** Flat design by default. Shadows only for layered/floating elements.

---

## 8. Do's and Don'ts

### Do's ✅
- Use status badges consistently across all tables
- Show loading skeletons (not spinners) for list/table data
- Use inline validation (show error immediately, not on submit)
- Use relative time for < 3 days ("2 giờ trước"), absolute date for older
- Show empty states with illustration + message + CTA
- Confirm destructive actions (cancel, reject, abort) with dialog
- Show progress % in delivery monitoring (not just raw numbers)
- Use toast notifications for async operation results

### Don'ts ❌
- Don't use color alone to convey status — always include text/icon
- Don't hide critical actions (sign, submit) in menus — keep visible
- Don't show more than 7 items in a dropdown without search
- Don't use truncation without tooltip on hover
- Don't auto-refresh data without user indication (show "Updated just now")
- Don't mix icon styles (use Lucide icons consistently)
- Don't use technical codes as labels (show "Rất thấp Sulphur" not "VLSFO380" alone)
- Don't disable buttons without explaining why (tooltip on disabled)

---

## 9. Responsive Behavior

| Breakpoint | Width | Layout |
|---|---|---|
| Mobile | < 768px | Single column, bottom tabs, stacked cards |
| Tablet | 768–1024px | Side nav collapsed (icons), content full-width |
| Desktop | 1024–1440px | Side nav expanded, content with margins |
| Large | > 1440px | Content max-width 1280px, centered |

### Mobile-specific adaptations:
- Tables → Card lists on mobile
- Filters → Bottom sheet on mobile
- Date pickers → Native device picker
- Forms → Full-width inputs, larger touch targets (48px height)
- Navigation → Bottom tab bar (max 5 items)

---

## 10. Design Handoff Guide

### Icons
- Library: **Lucide** (open source, consistent stroke width)
- Size: 20px (desktop), 24px (mobile)
- Stroke width: 1.5px
- Color: inherit from text color

### Component Library
- Baseline: **Shadcn UI** (Radix primitives + Tailwind CSS)
- Customization: Override tokens per §3-§5
- Charts: **Recharts** (consistent with Shadcn ecosystem)

### File Format for Handoff
- Screens: Figma frames (or equivalent mockup tool)
- Specs: This DESIGN.md + wireframe-input.md per module
- Assets: SVG for icons, PNG for screenshots/references

### Implementation Notes
- CSS: Tailwind CSS utility-first approach
- Dark mode: NOT in scope (operations need max visibility)
- RTL: NOT in scope (English-only UI)
- Accessibility: WCAG 2.1 AA target (contrast ratios verified per §3)
