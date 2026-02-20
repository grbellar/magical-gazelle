# UI Audit Rubric

Opinionated checklist for evaluating web UI against established design principles. Each category lists specific things to check and how to classify findings.

## Severity Levels

- **Critical**: Broken layout, unreadable text, inaccessible contrast, missing interactive affordances. These actively harm usability.
- **Warning**: Inconsistent spacing, misaligned elements, hierarchy confusion, styling one-offs. These degrade perceived quality.
- **Info**: Minor polish opportunities, subjective improvements, small deviations from ideal patterns. These are nice-to-fix.

## Spacing & Layout

Grounded in the 8-point grid system. Consistent spacing creates visual order and professionalism.

**Check for:**

- Spacing values follow a consistent system (multiples of 4px or 8px). Random values like 13px, 37px, or 22px indicate ad-hoc spacing.
- Vertical rhythm between sections is uniform. Sections at the same hierarchy level should have equal gaps between them.
- Padding within similar containers is consistent. All cards should have the same internal padding; all buttons the same padding.
- Adequate breathing room around content. Text or elements crammed against container edges.
- Container max-widths appropriate for content type. Paragraph text wider than ~75 characters per line hurts readability.
- No unnecessary nesting or wasted space that makes the layout feel sparse or unbalanced.

**Severity guide:**
- Critical: Elements overlapping, content touching edges with no padding, layout breaking
- Warning: Inconsistent spacing between similar elements, uneven section gaps
- Info: Spacing works but doesn't follow a strict system

## Typography

Grounded in modular type scales and readability research.

**Check for:**

- Heading hierarchy is visually clear. h1 should be noticeably larger than h2, h2 larger than h3. Each level should be distinguishable at a glance.
- Type scale follows a consistent ratio. Common scales: 1.25 (major third), 1.333 (perfect fourth), 1.5 (perfect fifth). Font sizes should not be arbitrary.
- Body text is at least 16px (or 14px minimum for secondary text). Anything smaller than 14px is hard to read.
- Line heights are appropriate: 1.4-1.6 for body text, 1.1-1.3 for headings. Too tight or too loose both hurt readability.
- Font families are consistent. Body text uses one family throughout. Headings may use a different family, but consistently.
- No more than 2-3 font families total. More creates visual noise.
- Letter spacing is appropriate. Uppercase text or small text benefits from slightly increased letter spacing. Large headings may benefit from tighter spacing.
- Text wrapping and line length are comfortable. Body text between 45-75 characters per line is ideal.

**Severity guide:**
- Critical: Text too small to read (<12px), line height so tight text overlaps, heading hierarchy inverted
- Warning: Inconsistent font sizes, random type scale, mixed font families without purpose
- Info: Line height slightly off, could use a tighter type scale

## Alignment

Grounded in Gestalt principle of continuity. Aligned elements create visual connections.

**Check for:**

- Left-aligned content shares a common left edge. Text, images, and elements in the same column should start at the same x-position.
- Centered content is actually centered. Use the accessibility snapshot or visual inspection to confirm. Off-center elements feel unpolished.
- Grid items align consistently. Cards in a row should have the same height or at least align their top edges.
- Form labels and inputs are aligned. Labels should either be above or beside their inputs, consistently.
- No "pixel drift" — similar elements should be at the same position/size, not off by 1-2px.
- Baseline alignment for text at the same hierarchy level placed side by side.
- Icons and text vertically centered relative to each other.

**Severity guide:**
- Critical: Major misalignment visible without squinting (elements clearly not where they should be)
- Warning: Subtle misalignment between similar elements, inconsistent centering
- Info: Minor baseline inconsistencies, trivial pixel differences

## Visual Hierarchy

Grounded in Nielsen's heuristics and Gestalt principles of figure-ground.

**Check for:**

- One clear primary action per screen or section. If everything is bold, nothing is. There should be an obvious "what should I do here?" answer.
- Secondary actions are visually subordinate. Outline or ghost buttons, smaller text, less contrast.
- Tertiary actions (like "cancel" or navigation links) are the least prominent.
- Logical reading flow. Content should guide the eye from most important to least. Typically top-to-bottom, left-to-right (for LTR languages).
- Important content has more visual weight (larger, bolder, higher contrast, more whitespace around it).
- Decorative elements do not compete with functional content. Backgrounds, borders, and icons should support, not distract.
- Related items are visually grouped (Gestalt proximity). Unrelated items have clear separation.
- Section headings clearly introduce their content. Orphaned headings (closer to the section above than below) confuse grouping.

**Severity guide:**
- Critical: No clear primary action, competing CTAs of equal weight, reading flow is confusing
- Warning: Hierarchy exists but isn't sharp enough, decorative elements slightly distracting
- Info: Could benefit from more contrast between hierarchy levels

## Buttons & Interactive Elements

Grounded in Fitts's Law and platform conventions.

**Check for:**

- Consistent button sizing within the same context. All primary buttons should be the same height; all secondary buttons should match each other.
- Touch targets are at least 44x44px (WCAG 2.5.5 / Apple HIG). This applies to buttons, links, form controls, and any clickable element.
- Clear visual distinction between primary, secondary, and tertiary actions. Primary: filled/solid. Secondary: outlined. Tertiary: text-only or minimal.
- Consistent placement conventions. In dialogs: primary action on the right (Western convention). In forms: submit button aligned with form fields.
- Interactive elements look interactive. Buttons should look clickable (not flat text). Links should be distinguishable from body text.
- Disabled states are visually distinct but still readable.
- Consistent border radius across buttons and interactive elements.
- Icons within buttons are vertically centered and consistently sized.

**Severity guide:**
- Critical: Touch targets too small (<32px), buttons indistinguishable from text, no visual affordance for clickable elements
- Warning: Inconsistent button sizes, mixed placement conventions, unclear primary action
- Info: Slight radius inconsistencies, minor icon alignment within buttons

## Color & Contrast

Grounded in WCAG 2.1 AA and color theory.

**Check for:**

- Normal text (under 18px or 14px bold) has at least 4.5:1 contrast ratio against its background.
- Large text (18px+ or 14px+ bold) has at least 3:1 contrast ratio.
- UI components and graphical objects have at least 3:1 contrast against adjacent colors (WCAG 1.4.11).
- Consistent color palette. Colors used across the page should come from a coherent set. One-off colors (a button that's a slightly different blue) indicate inconsistency.
- Color is not the only means of conveying information. Error states need more than just red — add icons, borders, or text labels.
- Interactive element borders or outlines have sufficient contrast against their background.
- Links are distinguishable from surrounding body text (by color, underline, or both).
- Focus indicators are visible (for keyboard navigation). At least 3:1 contrast, 2px minimum thickness.

**Severity guide:**
- Critical: Text contrast below 3:1, color as sole information carrier, invisible focus indicators
- Warning: Contrast between 3:1 and 4.5:1 for normal text, inconsistent palette, links not distinguishable
- Info: Palette is functional but could be more cohesive, minor contrast improvements possible

## Overflow & Clipping

**Check for:**

- No unintended horizontal scrollbar on the page. Test by resizing or checking at the target viewport width.
- Text does not overflow its container. Long words, URLs, or user-generated content should wrap or truncate gracefully (`overflow-wrap: break-word` or `text-overflow: ellipsis`).
- Images maintain their aspect ratio. Stretched or squished images indicate missing `object-fit` or incorrect sizing.
- Fixed-width containers handle variable content. Names, titles, and descriptions of varying lengths should not break the layout.
- Scrollable areas have visible scroll indicators. A container with hidden overflow and no scrollbar hides content.
- Absolutely positioned elements do not escape their intended containers.
- Responsive images and media scale within their containers.

**Severity guide:**
- Critical: Horizontal scrollbar on the page, text overflowing and overlapping other elements, broken images
- Warning: Text truncated unexpectedly, images slightly stretched, scroll areas without indicators
- Info: Minor overflow handling that works but could be more graceful

## Consistency

Grounded in Nielsen's heuristic of consistency and standards.

**Check for:**

- Similar elements are styled identically. All cards, all buttons, all form inputs of the same type should look the same.
- Border radius is consistent across component types. If cards have 8px radius, modals and dropdowns should use 8px or a clearly related value.
- Shadow treatment is consistent. If one card has a shadow, all cards at the same elevation should. Shadow values (blur, spread, color) should match.
- Icon sizing is uniform within the same context. Navigation icons should all be the same size. Inline icons should match their text size.
- Hover and focus state patterns are consistent. If one button darkens on hover, all similar buttons should darken on hover.
- No "one-off" styling variants. An element that looks similar to others but has inexplicable differences (different padding, slightly different color, different font weight) signals inconsistency.
- Consistent use of capitalization in UI text (all sentence case, or all title case — not mixed).
- Loading and empty states follow the same visual patterns across the app.

**Severity guide:**
- Critical: Same component type styled completely differently across pages, making the app feel broken
- Warning: Subtle inconsistencies between similar elements (different padding, slightly different colors)
- Info: Minor capitalization or icon size variations
