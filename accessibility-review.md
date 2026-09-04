# Accessibility review: Quote card module

## Scope and method

The quote card shows a testimonial: a logo or headline, a quote, the name and role of the person, an optional photo, and an optional button. It's used on landing pages and site pages, either alone or repeated across a row.

I checked the module itself: its markup, its styles, and the default values in the editor. I didn't check content an editor might add later. That's listed at the end as a remaining risk.

I picked five rules that this module can actually affect. Rules that depend on the whole page, like page titles, weren't included.

Contrast ratios were worked out with the formula WCAG defines, using the hex values set as defaults in the module. Any online contrast checker will give the same numbers. Every rule below links to its page on the W3C site if you want to read the full wording.

## Findings

### 1.4.3 Contrast (Level AA)

[Read the rule](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum)

Text needs a contrast ratio of at least 4.5:1 against its background.

**Issue.** The Role text defaulted to `#777777` on a white card. That's 4.48:1, just under the limit. The gap is too small to see, which is why this kind of problem usually goes unnoticed. Everything else was fine: the name and headline at 17.4:1, the button at 14.26:1, the button hover at 7.11:1.

**Resolution.** Changed the default to `#6F6F6F`, which is 5.02:1. I changed the CSS fallback to match, so older cards get the new colour too. The field now has a note explaining the 4.5:1 requirement.

### 2.4.7 Focus Visible (Level AA)

[Read the rule](https://www.w3.org/WAI/WCAG22/Understanding/focus-visible)

Anything you can reach with the keyboard needs a visible outline when focused.

**Issue.** The module had no focus style. The browser's own outline still appeared, but it looks different in every browser and is hard to see against the lime button, which is usually the only thing on the card you can tab to.

**Resolution.** Added a proper focus ring:

```css
.pqc-outer.pqc-outer :is(a, button):focus-visible {
  outline: 3px solid var(--pqc-focus-color);
  outline-offset: 3px;
}
```

The colour is editable and defaults to `#1D624D`, which is 7.23:1 against white. The 3px gap keeps the ring clear of the button's shadow.

### 1.3.1 Info and Relationships (Level A)

[Read the rule](https://www.w3.org/WAI/WCAG22/Understanding/info-and-relationships)

If something is clear from the layout, it should be clear in the code too.

**Issue.** The quote and the attribution were plain `<div>` elements. Sighted users can tell the name belongs to the quote because it sits underneath. A screen reader had no way to know that. It just read three separate blocks of text.

**Resolution.** Used the HTML that's meant for this:

```html
<figure class="pqc-main">
  <blockquote class="pqc-quote">…</blockquote>
  <figcaption class="pqc-person">…</figcaption>
</figure>
```

Now the name is officially the caption of the quote. Nothing looks different, as all the styling targets class names. I added `figure`, `figcaption` and `blockquote` to the module's reset, since browsers give them margins by default.

### 1.1.1 Non-text Content (Level A)

[Read the rule](https://www.w3.org/WAI/WCAG22/Understanding/non-text-content)

Images need alt text, or need to be marked as decorative.

**Issue.** The photo used whatever alt text an editor typed. Most people would type the person's name, so a screen reader read the name twice, once for the photo and again for the caption right next to it.

**Resolution.** The photo now always uses `alt=""` and `aria-hidden="true"`. A photo sitting next to its own caption is decorative. The logo keeps its editable alt text, because a logo is often the only place the company name appears.

### 2.4.10 Section Headings (Level AAA)

[Read the rule](https://www.w3.org/WAI/WCAG22/Understanding/section-headings)

Headings should follow the structure of the page.

**Issue.** The headline was always an `<h2>`. If you put the card in a section that already has an `<h2>`, you get two at the same level. If you put it under an `<h1>` on its own, there's a gap in the order.

**Resolution.** Added a Headline level setting with Heading 2, 3, 4, or "Not a heading". It defaults to Heading 2, so nothing changes unless you pick something else. It only shows when you've chosen the headline option.

## Summary

| Criteria | Level | Before | Action | After |
|---|---|---|---|---|
| 1.4.3 Contrast | AA | Failed, role text at 4.48:1 | New default `#6F6F6F`, matching fallback, note on the field | Passes, 5.02:1 |
| 2.4.7 Focus Visible | AA | Failed, no focus style | Added a focus ring with an editable colour | Passes, 7.23:1 |
| 1.3.1 Info and Relationships | A | Weak, quote and name unconnected | Semantic elements addition | Passes |
| 1.1.1 Non-text Content | A | Risk, name read twice | Photo marked decorative | Passes |
| 2.4.10 Section Headings | AAA | Weak, heading level fixed | Heading level is now a setting | Passes |

## Elements without changes

- **[1.4.11 Non-text Contrast](https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast):** the lime button stands out clearly against the white card, so you can see where it is without reading it.
- **[2.5.8 Target Size](https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum):** the button is about 40px tall; the minimum is 24px.
- **[1.4.10 Reflow](https://www.w3.org/WAI/WCAG22/Understanding/reflow):** the layout uses container queries, so the card adapts to a narrow column just as well as a narrow screen.
- **[2.3.3 Animation from Interactions](https://www.w3.org/WAI/WCAG22/Understanding/animation-from-interactions):** the hover effect turns off for anyone who has reduced motion switched on.

## Limitations

1. **Colours editors pick.** Every colour field accepts any value. The defaults pass, but someone could choose pale grey on white and break the contrast rule again. The note on the field helps, but doesn't stop it.
2. **Quote formatting.** The quote's colour and size come from the rich text editor on purpose, so its contrast is whatever an editor sets there.
3. **Heading level.** You can now pick the right one, but picking it correctly is still a decision for each page.

A short note in the editor guide covering points 1 and 3 would cover most of what's left.
