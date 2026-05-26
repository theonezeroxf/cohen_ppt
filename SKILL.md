---
name: ppt-batch-split-merge
description: Automatically split PowerPoint generation tasks over 10 pages into smaller batches, then merge them into one deck while keeping the main text extracted from the source PDF editable in PowerPoint.
---

# PPT Batch Split & Merge Skill

## Purpose

When a requested PPT has more than 10 pages, do not generate the whole deck in one pass. First create a full outline and style system, then generate slides in batches of 5–10 pages, and finally merge them into one consistent PPT.

This skill is especially useful for PDF-to-PPT tasks such as thesis defenses, reports, proposals, teaching slides, and project summaries.

## Core Rule

For PPTs over 10 pages:

1. Analyze the whole source first.
2. Create a complete slide outline.
3. Split the outline into batches of 5–10 slides.
4. Generate each batch using the same visual system.
5. Merge the batches into one final `.pptx`.
6. Run consistency and editability checks.

## Text Editability Requirement

Only the **main text from the source PDF** must be editable in the PPT.

Main text includes:

- Slide titles derived from the PDF
- Section titles
- Key bullet points
- Core conclusions
- Main paragraph summaries
- Important labels that represent the PDF’s essential meaning
- Important table values when they are central to the slide argument

These must be recreated as real PowerPoint text objects, such as:

- Editable text boxes
- Editable shape text
- Editable table cells when practical

## What Can Be Images

The following may be inserted as images when this improves visual fidelity or efficiency:

- PDF page backgrounds
- Decorative layouts
- Charts and figures
- Screenshots
- Diagrams
- Tables whose values are not central to the slide argument
- Icons, logos, illustrations, photos
- Complex formulas or visual elements that are not intended for editing
- Original PDF visual regions used as reference images

It is acceptable for a slide to use an image as the visual base, as long as the important text from the PDF is separately recreated as editable PPT text where needed.

## Forbidden Behavior

Do not satisfy the task by only inserting full-page PDF screenshots if the PDF’s main text is not editable.

Do not rasterize all meaningful text into images.

Do not convert titles, key points, or important conclusions into non-editable image text.

Do not use OCR-only image text as the final editable content when reliable extracted PDF text is available.

## Recommended PDF-to-PPT Approach

For each slide:

1. Use source PDF visuals as images where helpful.
2. Extract the main text from the corresponding PDF section/page.
3. Rewrite or condense the main text for presentation use.
4. Add the rewritten text as editable PPT text boxes.
5. Use images for non-essential visual detail.
6. Keep the layout clean and readable.

## Batch Planning

Before generating any slides, create a `batch_plan` with:

- Target total slide count
- Slide titles
- Source PDF page ranges
- Batch number
- Visual style notes
- Which text must be editable
- Which elements may be image-based

Default batch size:

- 5 slides for design-heavy decks
- 6–8 slides for normal academic/report decks
- Up to 10 slides only when layouts are simple

## Batch Boundaries

Split by logical sections rather than arbitrary page count when possible.

Good examples:

- Batch 1: Cover, contents, background
- Batch 2: Related work and research problem
- Batch 3: Method and system design
- Batch 4: Experiments and results
- Batch 5: Conclusion, outlook, thanks

## Style Consistency

All batches must share:

- Same slide size
- Same theme colors
- Same title style
- Same body font style
- Same page numbering format
- Same spacing rules
- Same section divider style

## Merge Rule

Prefer generating a single final deck from a unified slide specification rather than physically concatenating separate `.pptx` files.

If separate batch files are created, merge them only after checking:

- Slide order
- Page numbers
- Font consistency
- Theme consistency
- Master/layout consistency when possible
- Editable main text presence

## Quality Check

Before final output, verify:

- Total slide count matches the requested count
- Slide order matches the outline
- Every slide has editable title or main heading when applicable
- Main PDF-derived text is editable
- Non-essential graphics may remain images
- No critical content is only present as flattened image text
- Page numbers are continuous
- No repeated placeholder text
- No obvious batch-to-batch style drift

## Recovery Strategy

If a generated batch looks weak, regenerate only that batch instead of rebuilding the whole deck.

Common fixes:

- Reduce text density
- Turn long paragraphs into editable bullets
- Use PDF visuals as background/reference images
- Recreate only the main text as editable overlays
- Simplify complex tables into editable summaries

## Summary Principle

Over 10 slides: split, generate, check, merge.

From PDF: main text editable; other visual elements may be images.
