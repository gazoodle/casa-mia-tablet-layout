# Tablet Layout

<p align="center">
<img src="https://img.shields.io/github/stars/gazoodle/casa-mia-tablet-layout?style=for-the-badge&label=Stars&color=d6a102" alt="Stars">
<a href="https://github.com/gazoodle/casa-mia-tablet-layout/releases"><img src="https://img.shields.io/github/downloads/gazoodle/casa-mia-tablet-layout/total?style=for-the-badge&label=Downloads&color=e8604c" alt="Downloads"></a>
<a href="https://github.com/gazoodle/casa-mia-tablet-layout/releases/latest"><img src="https://shields.io/github/v/release/gazoodle/casa-mia-tablet-layout?style=for-the-badge&color=5da3a6" alt="version"></a>
<a href="https://github.com/gazoodle/casa-mia-tablet-layout/actions/workflows/validate.yml"><img src="https://img.shields.io/github/actions/workflow/status/gazoodle/casa-mia-tablet-layout/validate.yml?style=for-the-badge&label=Validated&color=3fbf5f" alt="Validated"></a>
<a href="https://github.com/gazoodle/casa-mia-tablet-layout/blob/main/LICENSE"><img src="https://img.shields.io/badge/licence-MIT-5da3a6?style=for-the-badge" alt="Licence: MIT"></a>
</p>

A Home Assistant view that fits the screen exactly. No scrolling, nothing hanging off the
edge, on any tablet or desktop, in portrait or landscape. On a phone it becomes Home
Assistant's own sections view instead (see [On a phone](#on-a-phone)).

> **Maturity: Beta.** Complete and used every day; its features have settled. Please report what breaks in your setup. ([The levels](https://github.com/gazoodle/casa-mia/blob/2026.11.10/docs/README.md#maturity))

<img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-default.webp" alt="A Tablet Layout view: a main panel with panels round it" width="800">

It *is* Home Assistant's own Sections view, so the header, footer, badges, sections, cards,
visibility conditions and card editors all work as you know them. What it adds is the
arrangement: its sections become **panels** round a **main** panel, sized to the screen,
and the whole view is locked to the screen's size. Make the screen bigger or smaller and the
panels grow and shrink with it; show or hide a card and the panels make room or close up.

If you've ever spent an evening nudging `card-mod` CSS so a wall tablet's dashboard *just*
fits, and then a card appeared under a condition and the page started scrolling again,
this is what it's for. And don't even get me started on using stack cards in section
views ...

**The same view on three screens.** Nothing was changed between these: the view simply fits.

<table><tr>
<td width="40%" valign="top"><img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-finished.webp" alt="The view at a tablet's size, landscape"><br>A tablet, landscape.</td>
<td width="25%" valign="top"><img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-portrait.webp" alt="The same view narrow and tall, as an iPad held upright"><br>Portrait.</td>
<td width="35%" valign="top"><img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-wide.webp" alt="The same view wide and short, as a desktop window"><br>A wide desktop window.</td>
</tr></table>

**Contents:** [Getting started](#getting-started) ·
[Panels, edges and layers](#panels-edges-and-layers) ·
[Sizing an edge](#sizing-an-edge) · [Panels along an edge](#panels-along-an-edge) ·
[Spacing and lines](#spacing-and-lines) · [Showing and hiding](#showing-and-hiding) ·
[One card filling a panel](#one-card-filling-a-panel) · [The main panel](#the-main-panel) ·
[Header and footer](#header-and-footer) · [Edit mode](#edit-mode) ·
[Options reference](#options-reference) · [Debugging](#debugging) · [Examples](#examples)

## Getting started

The view has to be loaded into Home Assistant. The Casa Mia integration does that itself,
on every page. A house without Casa Mia can have the view as a dashboard plugin instead:
see [By itself, without Casa Mia](#by-itself-without-casa-mia). Then:

1. Open a dashboard, enter edit mode, and add a view (or edit one).
2. Set **View type** to **Tablet (Casa Mia)**.
3. Save, and enter edit mode on the view. You get a main panel with a top, left, right and
   bottom edge round it, each with an empty section, named on its chip.
4. Add cards to the panels as you would in any Sections view.

<img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-default.webp" alt="A new Tablet Layout view in edit mode: four empty edges round main, with the dimension lines" width="800">

That's a working layout. Every edge starts at a share of the screen (left and right 15% of its
width, top 18% and bottom 20% of its height) and the main panel takes what's left. An
edge with nothing in it takes no room, so a layout with cards only in main and the bottom
edge is simply a main area over a strip.

In YAML, the view is:

```yaml
type: custom:casa-mia-tablet-layout
title: Kitchen
layout:            # the view's layout options; left out, the defaults
  gap: 8
sections:
  - type: grid
    view_layout: { panel: main }
    cards: [...]
  - type: grid
    view_layout: { panel: bottom }
    cards: [...]
```

Everything in `layout:` and each section's `view_layout:` is set on the edit surface; you
never need to write it by hand, but it's all plain YAML if you want to.

## Installing

The Downloads badge at the top is GitHub's count of downloads of the releases' packages. Every
update and every repeat download adds to it, so it isn't the number of houses using
Tablet Layout.

Tablet Layout needs Home Assistant 2026.3.0 or later.

It is a dashboard plugin: one file, which Home Assistant's dashboards load. It needs
nothing else of Casa Mia.

1. In HACS, or in Home Assistant's Marketplace (2026.11 and later), add
   `https://github.com/gazoodle/casa-mia-tablet-layout` as a custom repository. It is a dashboard resource:
   HACS calls that type Dashboard.
2. Download Tablet Layout there, and reload the page when asked.
3. The rest of this page says how to use it.

## Panels, edges and layers

**Main** is the panel in the middle. Round it are four **edges**: `top`, `left`, `right` and
`bottom`. Each section names its panel in `view_layout`:

```yaml
view_layout: { panel: top }
```

**Several sections can share an edge.** They sit in it as a stack of panels: side by side in
the top and bottom edges, one under the other in the left and right. A top edge with a
clock, the weather and the alarm, each its own section, is three panels in one edge.

**Layers nest like an onion.** Layer 1's edges surround a middle; layer 2's edges sit
*inside* that middle, round a smaller one, with main innermost.

```yaml
view_layout: { panel: right, layer: 2 }
```

```
┌──────────────── top (layer 1) ────────────────┐
│ left │ ┌──────── top (layer 2) ────────┐ │ right │
│      │ │ left │      main      │ right │ │       │
│      │ └──────── bottom (layer 2) ─────┘ │       │
└─────────────── bottom (layer 1) ──────────────┘
```

<img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-layers.webp" alt="Edit mode with a second layer: L2 LEFT and L2 TOP inside the outer edges, round MAIN · 2 LAYERS; the view grown outwards to make room" width="800">

In edit mode a layer's panels are named on their chips (*L2 TOP*), main says how many layers there are, and the view grows outwards to make room, so it scrolls.

Use a second layer when you want something to sit *between* the outer edges and main: a
status strip that spans only the width of the camera picture, say, while the outer left edge
runs the full height of the screen.

**Two layers at most.** It used to allow four, but past two it became unusable: each layer
grows edit mode outwards by another ring of frames, chips and gaps, until main is a small
box in the middle of them. Anything a third layer was wanted for has another way:

- **Several things along one edge:** a stack of panels in that edge (the + on its chip).
- **A top or bottom that stops short of the sides, or runs over them:** the corner locks.
- **Something kept to the far end of an edge:** hold it to the end.
- **An edge exactly as deep as its contents:** `size: auto`.

A view made earlier with three or four layers still shows them all, and the innermost can
still be removed.

The view's own `layout:` holds layer 1's edges; each further layer's are in `inner:`, one
inside the last:

```yaml
layout:
  bottom: { size: 12 }        # layer 1's bottom edge
  inner:
    top: { size: auto }       # layer 2's top edge
```

> A view made before panel stacks existed, with no `view_layout: {panel}` on its sections,
> still works: its first five sections are main, left, top, right and bottom, in that order.

## Sizing an edge

An edge's depth is how tall it is (top, bottom) or how wide (left, right). Set it on the
edge, for every panel in it:

| Setting | What it means |
|---|---|
| `size: 18` (with `unit: "%"`, the default) | A share of the view's height (top, bottom) or width (left, right). Grows and shrinks with the screen. |
| `size: 120` with `unit: px` | Fixed, the same on any screen, but never more than 45% of the view: a small screen gets less. |
| `size: auto` | **As its cards.** The edge is exactly as tall (or wide) as what its panels hold, and follows them as cards show and hide. The usual choice for a strip of tiles or buttons. |

<img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-mid-build.webp" alt="Mid-build in edit mode: a picture filling main, a strip of tiles along the bottom, and the Bottom edge box set to as tall as its cards" width="800">

Changing between % and px on the edit surface keeps the edge the same size on the screen
you're on: it converts the number for you.

<img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-padlocks.webp" alt="Edit mode: the bottom edge's left padlock open, so it stops at the left edge; its right padlock closed, so it runs to the view's right side" width="800">

**Where the top and bottom edges end.** By default the bottom edge runs the full width of
the view, under the left and right edges, and the top edge stops short of them, between
them. Each end of a top or bottom edge can be switched (`anchor_left`, `anchor_right`): on, it
runs to the view's side, over the side edge; off, it stops at the side edge. In edit mode,
each end has a padlock to click.

## Panels along an edge

When an edge holds several panels, each one's **length** along it is one of:

| Length | Set by | Meaning |
|---|---|---|
| As its cards | `view_layout: { length: cards }` | As wide (top, bottom) or tall (left, right) as its cards. The default for every panel of an edge but the last. |
| Fill | `view_layout: { length: fill }` | A share of whatever the others leave, alike between all that fill. The default for the last panel of an edge (so a panel alone fills it), unless another is set to fill or the edge is arranged `centre` or `end`. |
| Fixed | the section's `column_span` (top, bottom) or `row_span` (left, right) | Top and bottom: a number of the view's columns (its `max_columns`, HA's Width in the section's Layout tab). Left and right: a number of HA's card rows, 56 px each plus the gap. |

If they don't all fit, every panel but those filling shrinks alike. The last panel fills by
default so the edge reaches its end, and the corner locks have something to move. To pack
them all as their cards instead, set the last one to **As its cards**.

<img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-edge-stack.webp" alt="A top edge of three panels in edit mode, with the third panel's options open: as wide as its cards, held to the right" width="800">

**Room over** (when none fills) is arranged by the edge's `arrange`: `start` (from the left
or the top, the default), `centre`, or `end`. Choosing `centre` or `end` also turns off the
last panel's default fill, since the room over is what they arrange. A panel can also be **held to the end** of its
edge (`view_layout: { hold: end }`): it sits against the right or bottom whatever the others
do. A row of buttons from the left with the clock held right is two panels, the second held.

**Order** is the sections' order in the view. In edit mode, a panel's arrows move it along
its edge.

## Spacing and lines

Spacing works like CSS's box model.

- **Margin:** room left clear all round the whole view. One value, or each side its own
  (`margin: { top: 0, right: 8, bottom: 8, left: 8 }`). Top is below the header, bottom
  above the footer.
- **Gap:** the view's `gap` (4 px by default) is used everywhere, unless one has its own:
  - between an edge and its middle: the edge's `gap`;
  - between two panels of an edge: the first one's `view_layout: { gap_after }`.
- **Padding:** room inside a panel round its cards, to line them up with something else:
  `view_layout: { padding: 12 }`, or each side its own.

<img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-gap-line.webp" alt="A 12 px gap between main and the bottom edge with a dashed line in it, and the Line box open" width="800">

A gap is only there while what makes it shows: hide an edge and its gap goes with it.

**Lines.** Any gap can hold a line, drawn over the gap without taking room: an edge's
`line`, or a panel's `view_layout: { line_after }`.

```yaml
layout:
  left:
    gap: 12
    line: { width: 2, color: [128, 128, 128], style: dashed }
```

| Line option | Meaning |
|---|---|
| `width` | px |
| `color` | `[r, g, b]` |
| `style` | `solid`, `dashed`, `dotted` or `double` |
| `knock` | px across from the middle of the gap, to sit it nearer one side |
| `extend_start`, `extend_end` | px beyond its top or left end, and its bottom or right end; negative stops short |

## Showing and hiding

The layout follows what's showing, so conditional cards just work:

- **A card's or section's own visibility** (HA's Visibility tab) hides it as usual.
- **A panel with no card showing takes no room**, and an edge with no panel showing takes
  none either, with its gap. So a warnings panel appears only while there's a warning.
  Turn this off for an edge with `hide_empty: false` to keep its room, empty.
- **Garnish.** A heading over some conditional cards would keep its panel showing on its
  own. Make it garnish: adornment that never holds its panel open, so the panel hides when
  only garnish is left. In edit mode every card in a panel has a small sprig on its
  bottom-right corner; tap it to make the card garnish (the sprig fills, and the card is
  dimmed with a dashed outline), and tap again to make it content. A panel holding only
  garnish is hatched in edit mode, as it will never show. In YAML it's
  `view_layout: { garnish: true }`.

  **On every Sections dashboard, too:** switch on **Enable garnish on all section
  dashboards** on the Casa Mia panel's [Settings](https://github.com/gazoodle/casa-mia/blob/2026.11.10/docs/applets/settings.md) page (the cog by
  the house photo), and garnish works in every section of every Sections dashboard, not
  only a Tablet Layout's panels. Home Assistant already hides a section whose cards are all
  hidden; with garnish, a section left with only garnish showing hides too, and the others
  take its room.

  <img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-garnish.webp" alt="A right panel in edit mode: a Warnings heading as garnish, dimmed with a dashed outline and a filled sprig, over a Door tile as content with a plain sprig" width="274">

- **An edge can be hidden** outright (`hidden: true`): no room, no cards, but its sections
  kept for when it's shown again.

**A warnings panel**, then, is just a section of its own in an edge: a garnish heading, and the cards it heads, each visible only when it has something to say.

```yaml
- type: grid
  view_layout: { panel: right }
  cards:
    - type: heading
      heading: Warnings
      view_layout: { garnish: true }
    - type: tile
      entity: binary_sensor.barn_door
      visibility:
        - condition: state
          entity: binary_sensor.barn_door
          state: "on"
```

<table><tr>
<td width="50%"><img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-warning-off.webp" alt="The door shut: no right edge, main takes the room"><br>The door shut: the warnings panel has no content showing, so the right edge takes no room.</td>
<td width="50%"><img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-warning-on.webp" alt="The door open: the right edge appears with its Warnings heading and the door tile"><br>The door open: the right edge appears, and main makes room.</td>
</tr></table>

## One card filling a panel

When a panel has exactly one content card showing, and it's a kind made to fill (a Camera
Commander, a picture, picture-entity or picture-glance card, a map, an iframe, or
advanced-camera-card or WebRTC Camera), that card **fills** the panel, whatever its size;
garnish above it keeps its height. That's how a camera picture fills main, or a map fills a
side edge. A Camera Commander filling a panel switches to tile mode and draws to exactly
that size.

Every other card (a tile, a button, an entities card) keeps its own size, alone or not, as in
any Sections view. Overrule either way with `view_layout: { fill: true }` or
`{ fill: false }`.

The exceptions: a top or bottom edge of `size: auto` (it is as tall as its cards, so there is
nothing to fill), and the panels of a left or right edge that are as their cards (by default,
all but the last).

Otherwise HA's own grid places the cards, by their Width and rows, as in any Sections view.

## The main panel

Main takes whatever the edges leave, unless you give it a **fixed shape**:

```yaml
layout:
  main_fit: fixed
  main_ratio: "16:9"     # width:height
  main_width: 70         # % of the view's width
  panel_min: 8           # % each edge keeps at least
```

Main is then `main_width` wide at that shape, and the edges share the room round it. If the
screen is too short for that, main shrinks (keeping its shape) rather than squeeze an edge with
cards below `panel_min`. Good for a camera picture that should never be letterboxed.

<!-- screenshot wanted: tablet-layout-fixed-main.webp: a Picture Glance card filling main at 16:9 with panels round it -->

Inner layers always fit their middle; the shape applies to main itself.

## Header and footer

The view's **header** (its title, heading card and badges) is HA's own. Its one Tablet
Layout option is the space above it, `header_space` (24 px, HA's own default).

The **footer** (HA's view footer) takes space under the panels by default. Set
`footer: float` in `layout:` to float it over the bottom of the panels instead, as HA's
Sections view does.

## On a phone

A layout of panels round a main panel doesn't fit a phone's narrow screen. When the view
is narrower than `narrow_below` (600 px by default), it becomes Home Assistant's own
sections view instead: the sections in one column that scrolls, as on any other
dashboard.

- The top edge's panels come first, then the main panel, then the left, right and bottom
  edges' panels. Panels of one edge keep the view's order.
- What wouldn't show in the Tablet Layout doesn't show here either: a section with no
  panel, a hidden edge, and a section with no card that counts showing.
- Garnish cards are left out, as they only make sense inside a panel.
- A card that filled its panel (a Camera Commander, say) takes its own shape instead.
- Home Assistant's own visibility conditions still apply. To hide a section on phones
  only, give it the **Screen** condition.

The switch happens as the width changes, so a tablet turned on its side, or a browser
window made narrower, switches too. Edit mode on a phone is Home Assistant's own, for
cards only: sections can't be moved or added there, so the panels are arranged on a wider
screen. Set `narrow_below: 0` to keep the Tablet Layout on every screen. Set it in the
main panel's options as **Phone layout below**.

## Edit mode

Edit mode shows the layout as a drawing you can click.

**Everything on it is clickable:** every chip, label, padlock and line opens what sets it. If you can see a number or a name, click it.

- **Each panel has a toolbar.** Its **chip** (its name, such as *Top 2*) opens its options:
  this panel's own (length, hold to the end, padding, and a link to HA's own section
  editor for visibility and background) and its whole edge's (size, hidden, hide when
  empty, arrange). **+** adds a panel after it in its edge; on main, + adds a layer.
  The **arrows** move it along its edge. Too narrow for them all, it shows the chip alone;
  the options box has the same buttons.
- **Dimension lines**, as on a technical drawing: each edge's depth, each gap, the margin,
  each panel's padding, the space above the header, and each panel's size in its corner.
  The sizes are as they'll be *out* of edit mode. Click any label to change it. The bar
  above the panels shows or hides them (remembered in that browser).
- **Gaps:** click a gap's label to size it, or every gap at once, or to add a line in it.
  Click a line's chip to style or remove it.
- **Padlocks** at the ends of the top and bottom edges switch whether that end runs to the
  view's side.
- **Header and footer** each have a chip: the header's sets the space above it, the
  footer's whether it floats; both link to HA's own editors.
- **Hidden edges** show, hatched, so you can find them to show them again.
- With more than one layer, edit mode grows outwards to make room for each layer's
  toolbars, and the view scrolls both ways. Out of edit mode it fits the screen again.

<img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-edit.webp" alt="Edit mode with the dimension lines on: each panel's chip and size, the gaps, the margins, and the Main options box open" width="800">

Every box shows its change on the view as you make it. **Save** writes it, **Cancel**
puts it back.

Sections aren't dragged or duplicated here; cards move between them as in any Sections
view. A section can be deleted (from its menu) only from an edge with more than one, so every
panel keeps a section.

## Options reference

### The view (`layout:`)

| Option | Default | Meaning |
|---|---|---|
| `gap` | `4` | px between panels, and between each edge and its middle, unless one has its own. |
| `margin` | `0` | px clear round the whole view; one number, or `{top, right, bottom, left}`. |
| `main_fit` | `fit` | `fixed` gives main a fixed shape (see [The main panel](#the-main-panel)); otherwise main takes what the edges leave. |
| `main_ratio` | `"16:9"` | With `fixed`: main's shape, width:height. |
| `main_width` | `70` | With `fixed`: main's width, % of the view's. |
| `panel_min` | `8` | With `fixed`: % an edge with cards keeps at least. |
| `header_space` | `24` | px above the view's header, when it has one. |
| `footer` | (takes space) | `float`: over the bottom of the panels. |
| `narrow_below` | `600` | px: narrower than this, the view is HA's own sections view (see [On a phone](#on-a-phone)); `0`: never. |
| `top`, `left`, `right`, `bottom` | | Layer 1's edges (below). |
| `inner` | | The next layer in: its own `top`, `left`, `right`, `bottom` and `inner`. |

Also on the view itself (beside `layout:`): `max_columns` (HA's, 4 by default: the columns a
top or bottom panel's fixed Width counts in) and `debug: true` (see [Debugging](#debugging)).

### Each edge (`layout: { <edge>: {...} }`)

| Option | Default | Meaning |
|---|---|---|
| `size` | left, right 15; top 18; bottom 20 | Its depth: a number, or `auto` (as its cards). |
| `unit` | `"%"` | `%` of the view's, or `px` (at most 45% of the view). |
| `gap` | the view's | px between it and its middle. |
| `line` | none | A line in that gap (see [Spacing and lines](#spacing-and-lines)). |
| `arrange` | `start` | Where its panels sit when none fills it: `start`, `centre`, `end`. |
| `hidden` | `false` | Off the view entirely; its sections kept. |
| `hide_empty` | `true` | With no card showing, it takes no room. `false`: its room kept, empty. |
| `anchor_left`, `anchor_right` | top: off; bottom: on | Top and bottom only: that end runs to the view's side, over the side edge. |

### Each section (`view_layout:`)

| Option | Default | Meaning |
|---|---|---|
| `panel` | | `main`, `top`, `left`, `right` or `bottom`. |
| `layer` | `1` | 1 to 4. |
| `length` | fill for the last of its edge, else `cards` | Its length along its edge: `cards` or `fill`. A fixed one is HA's `column_span` (top, bottom) or `row_span` (left, right) on the section instead. |
| `hold` | | `end`: held against the end of its edge. |
| `padding` | `0` | px inside it round its cards; one number, or `{top, right, bottom, left}`. |
| `gap_after` | the view's | px to the next panel along its edge. |
| `line_after` | none | A line in that gap. |

### Each card (`view_layout:`)

| Option | Default | Meaning |
|---|---|---|
| `fill` | by kind | `true`: alone in its panel, this card fills it; `false`: it never does. Unset: cameras, pictures, maps and iframes fill, other cards don't. |
| `garnish` | `false` | `true`: adornment that never keeps its panel showing (the sprig in edit mode). |

## By itself, without Casa Mia

Tablet Layout is also published by itself, in the repository
[gazoodle/casa-mia-tablet-layout](https://github.com/gazoodle/casa-mia-tablet-layout),
for a house without Casa Mia. Add that repository in HACS,
or in Home Assistant's Marketplace (2026.11 and later), as a custom repository, and
download it. It is a dashboard resource, which HACS calls the type Dashboard.

It is one file, `casa-mia-tablet-layout.js`. It has the same view as Casa Mia's, with
garnish, the [Over layer](#over-layer) and the Orientation choice in a dashboard's
Screen condition ([Augmenting Home Assistant](https://github.com/gazoodle/casa-mia/blob/2026.11.10/docs/augmenting-home-assistant.md)). Casa Mia's
other cards are not in it.

It has nothing to set. Two things that Casa Mia has switches for on its Settings page
are always on: garnish on every Sections dashboard, and the Orientation choice.

The Settings page's other two switches, which outline the panels and show the view's
size, are not needed: `debug: true` in the view's own YAML does both
(see [Debugging](#debugging)).

A house with Casa Mia has all of this already and doesn't need the file. If both are
loaded, the first one is used, and the browser's console says which. Take
`casa-mia-tablet-layout.js` out of the dashboard's resources.

## Home Assistant updates

The Tablet Layout is built on Home Assistant's own Sections view, and to do what it does
(place the panels, edit them in place, garnish, the sprigs and chips) it reaches into the
frontend's workings, which Home Assistant doesn't promise to keep the same. That's the
long tradition of custom layouts: Thomas Lovén's layout-card and card-mod do the same, and
an HA update has broken each of them now and then until it caught up.

So now and then a new Home Assistant release may upset it: a panel out of place, an edit
control missing. Your dashboards' configuration is safe whatever happens: the sections and
cards are kept as Home Assistant's own, with the layout's options beside them, and nothing is
saved except by your own edits. If an HA update
breaks something, update Casa Mia (a fix usually follows the HA release), and if it's still
wrong, [open an issue](https://github.com/gazoodle/casa-mia/issues) with your HA version.

## Debugging

When something doesn't sit where you expect:

- **Casa Mia panel → Settings → Tablet Layout debugging** outlines every panel and the view's
  header and footer, and labels the view's size, the room below its top, and how far the
  page still scrolls (it should say `0 x 0`). It's switched for every Tablet Layout view, and
  the views follow the switch while they're open.
- **`debug: true`** in one view's config does both, for that view only.

<img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/tablet-layout-debug.webp" alt="Tablet Layout debugging on: the size label at top right reads view 1882 x 1343, room 1343, page scrolls 0 x 0" width="800">

A page that still scrolls by a pixel or two on a wall tablet is often the tablet's WebView
being slightly larger than its screen; a small margin fixes it.

## Examples

> **To write:** a picture and the YAML for each, all with invented names, each checked on the
> test rig.
>
> - The default: main with a strip of tiles along the bottom (`size: auto`).
> - A camera wall: a Camera Commander filling main at a fixed 16:9, lights and locks down the
>   right.
> - Conditional panels: a warnings edge that appears only when something needs attention.
> - Two layers: a full-height left edge, with a status strip only as wide as the camera.
> - A top edge of three panels: buttons from the left, the clock held right.

## Over layer

One card shown over the whole dashboard while its conditions hold. What's beneath can
still be seen, but not touched: only the card on the layer works.

> **Maturity: Alpha.** Does its job every day in the author's house, but hasn't been tried in many others. Expect rough edges and changes. ([The levels](https://github.com/gazoodle/casa-mia/blob/2026.11.10/docs/README.md#maturity))

<img src="https://raw.githubusercontent.com/gazoodle/casa-mia/2026.11.10/docs/screenshots/over-layer.webp" alt="An alarm panel card over a dashboard of lights, locks and temperatures, which shows through dimmed" width="800">

- **The alarm is set:** the wall tablets show the alarm's disarm panel over everything,
  and nothing else can be pressed until it's disarmed.
- **A visiting engineer:** sees how everything is doing, and can't change any of it.
- **A cover:** against little fingers, or a lock at night.
- **A notice:** a warning that wants dealing with now, floating over the page.

### Adding one

It's a card like any other: **Casa Mia Over layer** in the card picker, on any Sections
dashboard, a Tablet Layout included. Put it in any section.

1. **Its card:** pick the one card it holds with Home Assistant's own card picker (an alarm
   panel, a tile, a markdown note, a stack of several), and edit it as usual. **Change the
   card** starts again.
2. **How it shows:**
   - **Float:** the card where you place it (the middle, unless you choose), up to the
     width you set.
   - **Full screen:** the card fills what's covered.
   - **Scroll with the view:** the card at the top, moving with the page.
3. **What it covers:** **the view** (the sidebar and header stay usable), or **the whole
   window** (sidebar and header too).
4. **Block taps on what's beneath:** on, what's beneath can be seen and not touched; off,
   taps reach the dashboard round the card.
5. **Where it sits** (float, scroll): tap one of nine places in the 3 × 3 grid, from top left
   to bottom right, and set how far in it sits from the edges it's anchored to: across for
   left and right, down for top and bottom, both in a corner. Scroll with the view sits
   along the top only. Full screen fills what's covered.
6. **The backdrop:** a colour, how see-through it is, and a blur.
7. **When it shows:** the card's own **Visibility** tab, Home Assistant's conditions: the
   alarm armed, a user, the time of day, a sensor on. No conditions: always.

**Its whole panel.** Turn on **Show its whole panel** and, in place of a card of its own,
the layer shows the panel (section) the Over layer is in: every card in it, its headings,
garnish and each card's own Visibility included, anchored and sized like a card (raise
**Card width** for a wide panel). A warnings panel, say, can float over the dashboard in one
piece, its headings and all, rather than as one card. That panel never shows in its own
place out of edit mode; in edit mode it's there as usual, so you edit its cards where they
are (in a Tablet Layout it's hatched, as it never shows there otherwise), and the Over
layer itself is just its chip. The layer shows the panel as it is each time it goes up.

**A pop-over that goes when it's dealt with.** Float it in a corner (bottom right, say), turn
**Block taps** off and the backdrop's opacity to 0: a panel in the corner, the dashboard
round it still in use. Give it a Visibility condition its own card changes (a door left
open, a reminder, an alert to acknowledge), and acting on the card is what dismisses it:
close the door, tick the reminder, and its condition is gone, and so is it.

While the dashboard is being edited, the Over layer is an ordinary card in its place, with a
badge saying what it does (*Over layer · float · the view*), so you can always edit it and
it's never live while you do. Out of edit mode it takes no room in its section.

It also goes when its section is hidden by the section's own Visibility. In a Tablet Layout
it never holds a panel open, so a panel holding only an Over layer stays out of the way.

### The way out

The layer is meant to keep people out, so nothing in a page's address turns it off. The way
out is for admins only:

- **The door.** Over the whole window and blocking taps, the layer leaves an admin one way
  through, to Home Assistant's own edit button (its pencil, or its ⋮ menu when the pencil
  is in it). A small lid covers it, looking like the backdrop, with a soft ring pulsing
  round it. With a mouse, move over it and it opens; on a touch screen, tap it once to
  open it for a few seconds, then tap the button. In edit mode the layer steps aside.
  Everyone who isn't an admin gets the wall.
- **`?edit=1`.** Add it to the end of the dashboard's address and Home Assistant opens it in
  edit mode, for admins only. That's the way out when the header is hidden (kiosk-mode,
  say) and there's no button for a door.
- **Lift its condition** from somewhere else: your phone, an automation, a voice command.
  Every layer on that condition goes.

To leave admins out of a layer altogether, give it a **User** condition on its Visibility tab.

**How far it goes.** The layer keeps hands off a screen; it isn't a lock on Home Assistant.
Whoever is signed in is still signed in: another tab, the companion app or a keyboard can
reach whatever their Home Assistant user may. For a wall tablet that's the point; for a
guest, pair it with a user that can do little (see Guest login's
[How safe is it?](https://github.com/gazoodle/casa-mia/blob/2026.11.10/docs/applets/guest-login.md#how-safe-is-it)).

### Troubleshooting

- **It doesn't show:** check its Visibility conditions, and that you're not in edit mode
  (there it's an ordinary card). The browser's console says `CASA-MIA CARDS: over layer
  shown (…)` when it goes up.
- **It shows when its section is hidden:** the console says `… a section round it is hidden
  by its own Visibility: down` when it sees the section go. No line: say so in an issue,
  with your Home Assistant version.
- **A Home Assistant update upset it:** like the Tablet Layout, it leans on Home Assistant's
  frontend (see [Home Assistant updates](#home-assistant-updates)).

## Part of Casa Mia

Tablet Layout is part of [Casa Mia](https://github.com/gazoodle/casa-mia), where it is written and
released. This repository is generated from Casa Mia 2026.11.10 (commit `61050faf35`) and
isn't edited by hand, so a change to Tablet Layout is made in Casa Mia. A problem with it is
reported here, in this repository's [issues](https://github.com/gazoodle/casa-mia-tablet-layout/issues).

A house with the Casa Mia app has Tablet Layout already, in the file Casa Mia's own integration
loads, and doesn't need this one. If both are loaded, the first one is used and the
browser's console says which: take this one out of the dashboard's resources.
