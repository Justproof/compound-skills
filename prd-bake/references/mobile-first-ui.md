# Mobile-First, Touch-Friendly UI Rules

Read this whenever the feature involves website coding: pages or web apps built with HTML and CSS and opened in a browser, including a browser extension's own pages (popup, options, full-tab pages). It does not apply to other interfaces (terminal tools, native apps, email, generated images or video). It drives the PRD's Design Considerations section and the acceptance criteria of every UI story.

**These rules are mandatory.** They apply to every website feature in every project. The project's design system supplies the tokens (colours, fonts, spacing scale, component names) and may set stricter values, but it cannot relax a rule. If a project convention conflicts with a rule here, the rule wins and the PRD records the conflict under Risks. The only way out of a rule is an explicit decision by the user during questioning, recorded in the decisions table with its reason.

Why so firm: these layouts get used with a thumb, often on a small touchscreen. A missed rule shows up as a button nobody can hit or a page that scrolls sideways, and it is far cheaper to demand it in the PRD than to retrofit it after the build.

The layout recipes are distilled from Shruti Balasa's *Complete Guide to CSS Flex & Grid (Vanilla CSS)*. They are written as plain CSS; in a Tailwind project, specify the same technique with the equivalent utilities.

## Contents

1. The mandatory rules
2. Breakpoints
3. Baseline CSS
4. Choosing Flexbox or Grid
5. Flexbox recipes
6. Grid recipes
7. Mobile bottom navigation bar
8. Banned patterns
9. Mandatory acceptance criteria
10. What the PRD must contain

## 1. The mandatory rules

- **R1. Mobile first.** Base styles target the smallest screen. Larger layouts are added with `min-width` media queries only. No `max-width` queries that undo desktop styles.
- **R2. One column first.** Every layout starts as a single column and earns more columns at a breakpoint, or responds on its own with `auto-fit` / `flex-wrap`.
- **R3. Flexbox for one direction, Grid for two.** Rows or columns of items use Flexbox. Anything with both rows and columns, or a page-level layout, uses Grid. No floats, tables or absolute positioning for layout.
- **R4. Touch targets.** Every interactive element is at least 48×48px, with at least 8px between neighbours.
- **R5. Nothing depends on hover.** Every `:hover` is paired with `:active` and `:focus-visible`, and hover-only styling sits inside `@media (hover: hover)`. Any action reachable by hover is also reachable by tap.
- **R6. No horizontal overflow.** No fixed pixel widths on containers. Text-heavy flex children get `min-width: 0`; grid tracks holding long content use `minmax(0, 1fr)`.
- **R7. Dynamic viewport height.** Full-height layouts use `min-height: 100dvh`, never `100vh`, because mobile browser chrome makes `100vh` overflow.
- **R8. `gap` for spacing between items.** Not margins on children.
- **R9. Fluid type and spacing.** Use `clamp()` (or the project's fluid scale) so text stays readable from phone to desktop without a breakpoint per size.
- **R10. Contained scrolling.** Scrollable regions set `overscroll-behavior: contain`. No nested scroll areas unless the PRD specifies how touch is handled.
- **R11. Safe areas.** Anything fixed to a screen edge pads with `env(safe-area-inset-*)`.
- **R12. Bottom navigation on mobile.** Primary navigation on small screens is a fixed bottom tab bar (section 7), never a hamburger menu.
- **R13. Reuse before building.** If the project already has a component for the job, the PRD names it and the story reuses it.

## 2. Breakpoints

Use the project's breakpoints if it defines them. Otherwise: base (phones), 600px (large phone, portrait tablet), 1024px (landscape tablet, small touchscreen), 1280px (desktop).

If the project targets a specific device (a kiosk, a 7-inch 1024×600 touchscreen), the PRD names the device and resolution and treats it as the primary target for verification.

## 3. Baseline CSS

Required if the project does not already ship an equivalent reset:

```css
*, *::before, *::after { box-sizing: border-box; }
html { -webkit-text-size-adjust: 100%; touch-action: manipulation; }
```

## 4. Choosing Flexbox or Grid

| The layout is... | Use |
| --- | --- |
| Items in a single row or column: toolbar, tabs, icon plus text, button group | Flexbox |
| Content decides the sizes, items wrap when they run out of room | Flexbox with `flex-wrap` |
| Rows and columns that must line up: card grid, gallery, dashboard | Grid |
| Page skeleton: header, sidebar, main, footer | Grid with `grid-template-areas` |
| A form with labels and fields in aligned columns | Grid |
| Items that change position between breakpoints | Grid (`grid-template-areas`), or `order` for a single item |

They nest freely: a Grid page layout whose cells are Flexbox components is the normal case.

## 5. Flexbox recipes

| Need | Technique |
| --- | --- |
| Icon and text on one line, vertically centred | `display: flex; align-items: center; gap: ...` |
| Items pushed to opposite ends (tabs, prev/next links) | `justify-content: space-between` |
| Cards that wrap to the next line on small screens | `flex-wrap: wrap` with `flex: 1 1 280px` on each card |
| Equal-width columns regardless of content | `flex: 1` (that is `1 1 0%`) on each child |
| Fixed ratio split, for example image and text 2:1 | `flex: 2` and `flex: 1` |
| Input that fills the row beside a button | `flex-grow: 1` on the input |
| Icon, avatar or button that must not squash | `flex-shrink: 0` |
| Long text beside a fixed item that must truncate, not overflow | `min-width: 0` on the text child |
| One item pushed to the far end (badge, count, last footer column) | `margin-left: auto` on that item |
| Sticky footer | Column container with `min-height: 100dvh`, `flex-grow: 1` on main |
| Card with header, stretching body, footer pinned to the bottom | `flex-direction: column`, `flex-grow: 1` on the body |
| Centre one thing in its container | Flex container plus `margin: auto` on the child |
| Nav bar with a centred menu | `flex: 1` on the left and right wrappers, menu between them |
| Nav bar whose menu drops to its own row on mobile | `flex-wrap: wrap`; menu gets `flex-basis: 100%` at base, then `flex: auto` and reordering from the breakpoint up |
| Alternating image-left, image-right rows | `flex-direction: row-reverse` on even rows, from a breakpoint up |
| One item aligned differently from its siblings | `align-self` |
| Inline group that sits in the text flow (social icons) | `display: inline-flex` |
| Wrapped rows centred as a block in a tall container | `align-content: center` |

Use `order` sparingly. It changes the visual order only, so keyboard and screen-reader order still follows the HTML. Put the HTML in the mobile reading order and reorder for larger screens.

## 6. Grid recipes

| Need | Technique |
| --- | --- |
| Responsive card grid with no media queries | `grid-template-columns: repeat(auto-fit, minmax(250px, 1fr))` |
| Same, but keep empty tracks so a lone card does not stretch | `auto-fill` in place of `auto-fit` |
| Equal columns from a breakpoint up | `1fr` at base, `repeat(n, 1fr)` in the `min-width` query |
| Sidebar plus main | `1fr` at base, `22rem 1fr` (fixed plus flexible) from the breakpoint up |
| Main column holding wide content (code, tables) beside a sidebar | `minmax(0, 1fr)` for the main track so it cannot overflow |
| Cards with a maximum width, centred | `repeat(n, minmax(auto, 18rem))` with `justify-content: center` |
| Sticky footer | `min-height: 100dvh; grid-template-rows: auto 1fr auto` |
| Label and field columns in a form | `grid-template-columns: auto 1fr`, single column at base |
| Element spanning the full width (form button, section header) | `grid-column: 1 / -1` |
| Element spanning rows or columns | `grid-column: span 2`, `grid-row: span 2` |
| Layout whose regions rearrange between breakpoints | Name regions with `grid-area`, redraw `grid-template-areas` in each `min-width` query |
| Highlighted item first on mobile (featured pricing plan) | Put it first in the HTML, then move it with `order` from the breakpoint up |
| Columns sized to their content (summary rows, detail tables) | `max-content`, `auto`, `fit-content()` tracks |
| Items of mixed size centred in their cells (logos) | `justify-items: center; align-items: center` (or `place-items: center`) |
| Centre one thing on the page | `display: grid; place-items: center` |
| Uniform rows for content added later (gallery) | `grid-auto-rows` |
| Items flowing down columns, or back-filling gaps | `grid-auto-flow: column` or `dense` |
| Label or caption pinned to a corner of its cell | `justify-self` and `align-self` on the item |

`grid-template-areas` must describe rectangles: every named area is one solid block, and a `.` marks an empty cell. This is the preferred way to specify page layouts in a PRD because the area map reads like a picture of the screen. Write the map for each breakpoint into the Design Considerations section, for example:

```
base:     "header" "main" "aside" "footer"
1024px+:  "header header" "aside main" "footer footer"
```

## 7. Mobile bottom navigation bar

Required by R12 for primary navigation on small screens. Thumbs reach the bottom of the screen; they do not reach a hamburger in the top corner.

- Fixed bottom tab bar on mobile viewports, hidden at the desktop breakpoint.
- 4 to 5 tabs at most.
- Each tab: icon above a short label, clear active versus inactive state, and a minimum height of 48px so the touch target is guaranteed, not left to padding.
- Auto-hide on scroll: the bar slides away while the user scrolls down to read and returns as soon as they scroll back up. Track an anchor scroll position; hide after about 60px of downward travel, show after about 60px upward, and re-anchor each time so small jitters do nothing.
- Main content is padded so the bar never covers the last item, and the bar itself pads with `env(safe-area-inset-bottom)` so tabs clear the home indicator.
- The `<nav>` carries an `aria-label` (for example "Mobile navigation").
- Optional raised centre tab for the one primary action: a floating circle lifted out of the tab row with its label underneath, wired to an event or modal instead of a route. Use it for one action at most.
- Built once as a reusable component driven by a tabs array (`route`, `label`, `icon`, `match`, where `match` accepts wildcards such as `games.*` for section-wide active state). If the project already has one, the PRD says "reuse X" and does not respecify it.

Reference implementation for a Laravel, Blade, Tailwind and Alpine project. The structure and behaviour are fixed; swap the colours for the project's own tokens:

```
Container:   fixed inset-x-0 bottom-0 z-50 flex items-center justify-around border-t border-zinc-700 bg-zinc-900 py-2 lg:hidden transition-transform duration-300 will-change-transform
Auto-hide:   Alpine toggles translate-y-full (hidden) / translate-y-0 (shown) on @scroll.window.passive, 60px delta, re-anchoring anchorY = window.scrollY
Safe area:   style="padding-bottom: env(safe-area-inset-bottom, 0.5rem)"
Tab:         flex flex-col items-center gap-1 flex-1 py-1 min-h-[48px] justify-center
Active:      text-emerald-400, resolved with request()->routeIs($tab['match'])
Inactive:    text-zinc-400
Icon:        size-5, rendered dynamically: <x-dynamic-component :component="'heroicon-m-' . $tab['icon']" class="size-5" />
Label:       text-xs font-medium
Centre CTA:  flex items-center justify-center size-10 bg-emerald-500 rounded-full -mt-4 shadow-lg, wrapping a white size-5 icon
Component:   resources/views/components/mobile-bottom-nav.blade.php
Layout:      <x-mobile-bottom-nav :tabs="$tabs ?? []" /> with main content wrapped in <div class="pb-16 lg:pb-0">
```

## 8. Banned patterns

A PRD must not specify any of these, and should call them out if research finds them in the code the feature touches:

- Fixed widths or heights that overflow (`width: 800px`, `height: 100vh`).
- Desktop-first CSS: base styles for wide screens, undone with `max-width` queries.
- Flex children with long text and no `min-width: 0`.
- Touch targets under 48px, or packed closer than 8px.
- Functionality reachable only by hover.
- Nested scroll regions without `touch-action` and `overscroll-behavior` handling.
- A hamburger menu as the primary mobile navigation.
- Floats, tables or absolute positioning used for layout.
- Margins on children doing the job of `gap`.

## 9. Mandatory acceptance criteria

Every website UI story carries all of these, worded for the story. Drop one only when it cannot apply (a story with no interactive elements has no touch targets to check), never because it is inconvenient.

- `- [ ] Built mobile first: base styles for the smallest screen, min-width queries only`
- `- [ ] No horizontal scroll at 360px wide`
- `- [ ] Every interactive element is at least 48×48px with 8px spacing`
- `- [ ] All actions work by touch alone (no hover-only behaviour)`
- `- [ ] Layout verified at base, 600px, 1024px and 1280px (or the project's breakpoints)`
- `- [ ] Verified in a real browser` (see the browser verification note in SKILL.md)

Add where relevant:

- `- [ ] Reuses [existing component] rather than introducing a new one`
- `- [ ] Fixed elements respect safe-area insets`
- `- [ ] Primary navigation uses the mobile bottom nav below [breakpoint]`

## 10. What the PRD must contain

For a website feature, the Design Considerations section includes:

1. The target devices and breakpoints.
2. The layout for each screen or component: Flexbox or Grid, the recipe used, and for page layouts the `grid-template-areas` map per breakpoint.
3. The rules from section 1 that this feature exercises, by number, with anything feature-specific spelled out (which elements are touch targets, what the hover fallback is, which regions scroll).
4. The components being reused.
5. Any rule waived by the user, with the decision ID.

Do not paste this whole file into the PRD. Cite the rules by number and spell out only what is specific to the feature.
