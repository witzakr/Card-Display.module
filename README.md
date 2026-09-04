# Quote card

A HubSpot custom module for testimonials. Shows a logo or headline, a quote, the
name and role of the person, an optional photo, and an optional button.

Works as a single wide card or as one of several in a column row.

## Files

| File | Purpose |
|---|---|
| `module.html` | HubL template. Also writes field values into a scoped `<style>` block |
| `module.css` | All styling. Class based, no JavaScript |
| `fields.json` | Editor fields |
| `meta.json` | Module metadata |
| `accessibility-review.md` | WCAG 2.2 AA review of this module |

## Installing

Copy the folder into your theme and upload:

```
hs upload "Quote card.module" "your-theme/Quote card.module"
```

## Settings

**Content tab**

- Logo or headline at the top, with the unused option hidden
- Quote, entered as rich text so the editor controls its own formatting
- Name, role, and an optional photo
- Button with a show toggle, text, and link

**Styles tab**

- Layout: stacked or side by side, left or centred, capped width or fill the column
- Card: background, width, padding, radius, shadow colour and depth, hover growth
- Text colours and sizes for the headline, name, and role
- Button colours, radius, shadow, and focus ring colour

## How the CSS is put together

A few conventions worth knowing before editing:

- **Everything is prefixed `pqc-`.** No selector can match markup outside this
  module.
- **The root class is doubled** (`.pqc-outer.pqc-outer`) on every rule. That
  raises specificity to 0,3,0 so theme styles don't override the module. Any new
  rule needs the same prefix or the theme will win.
- **Custom properties live on the root element, not `:root`.** They can't leak
  to the page or collide with another module's variables.
- **A scoped `:where()` reset** neutralises bare tag styles arriving from
  elsewhere, while staying weak enough that the rules below always win.
- **Breakpoints are container queries**, so the card responds to the width of the
  column it sits in rather than the browser window. They target `.pqc-card`, not
  `.pqc-outer`, because an element can't answer its own container query.

Field values reach the CSS as custom properties written into a `<style>` block
scoped to `#pqc-{{ name }}`, so several instances on one page keep their own
settings.

## Accessibility

Reviewed against WCAG 2.2 Level AA. See `accessibility-review.md` for the full
write up. In short:

- Quote and attribution use `figure` / `blockquote` / `figcaption`
- Default text colours meet 4.5:1 on the default background
- Explicit focus ring, since the browser default is faint on the lime button
- The photo renders as decorative, as the name sits beside it
- Headline level is selectable so the card fits the page outline

Editors can still choose colours that fail contrast. The defaults pass.

## Browser support

Uses container queries, which need Chrome 105, Safari 16, or Firefox 110 and
later. Older browsers get the desktop layout at every width rather than a broken
one.
