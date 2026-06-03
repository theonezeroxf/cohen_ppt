---
name: ppt-batch-split-merge
description: Automatically plan PDF-to-PPT decks by analyzing specified PDF sections for content volume and importance, allocating slide counts, generating an outline, creating per-slide backgrounds with img-gen, then adding editable outline/PDF-derived content into the PPT; for decks over 10 pages, split generation into batches and merge consistently.
---

# PPT Batch Split, Image Background & Editable Content Skill

## Purpose

Use this skill for PDF-to-PPT tasks such as thesis defenses, reports, proposals, teaching slides, project summaries, and other decks derived from specified portions of a PDF.

Before execution, the workflow must first confirm three inputs with the user: which part of the PDF should be converted into PPT, what visual style the PPT should use, and whether speaker notes should be generated. If the user does not specify a style, use the default **academic minimalist style** for the deck and for every `img-gen` background prompt. After confirming these inputs, analyze the requested PDF sections, decide how many PPT slides each content area deserves based on **content volume and importance**, generate a complete outline, create a custom background image for every slide with `img-gen`, and finally add the outline or PDF-derived content into the PPT as editable PowerPoint objects where required.

When the requested or planned PPT has more than 10 pages, do not generate the whole deck in one pass. Generate slides in smaller batches, then merge them into one consistent final deck.

## Implementation Technology

Use **`img-gen + Presentation`** as the implementation technology for this skill:

- Use `img-gen` only to generate slide background images and any additional image assets requested by `Presentation` for a slide.
- Use `Presentation` for all remaining work: planning execution, PPT creation, layout, inserting generated images, editable text boxes/shapes/tables, optional speaker notes, page numbering, batch assembly, and merging.
- `Presentation` defines the image requirements for `img-gen`, including dimensions, style, layout intent, visual references, and editable-text safe areas.
- Do not use `img-gen` to create final readable titles, bullet points, conclusions, or other critical PDF-derived text. Those must be added through `Presentation` as editable PPT objects.
- When checking quality, verify both sides of the implementation: `img-gen` backgrounds/assets satisfy `Presentation` requirements and `Presentation` content remains editable.

## Required Pre-Execution Questions

Before analyzing the PDF or generating any outline, backgrounds, or PPT files, ask the user these questions unless the answer is already explicit in the request:

1. **Which part of the PDF should be used to generate the PPT?** Examples: page ranges, chapters, sections, figure/table ranges, or named topics.
2. **What PPT style should be used?** If the user does not specify a style, default to **academic minimalist style**. Use this style consistently in the deck theme and in every `img-gen` background prompt.
3. **Should speaker notes be generated for the PPT?** Ask whether to create notes for each slide. If the user says yes, add concise presenter notes derived from the outline/PDF content. If the user says no, do not add notes.

Do not proceed to slide allocation, outline generation, image generation, or PPT creation until the PDF scope is known. If style or notes preference is missing and the user cannot be reached, use **academic minimalist style** and **no speaker notes** as safe defaults, but still require a known PDF scope.

## Core Workflow

For every PDF-to-PPT task:

1. Confirm the PDF scope, PPT style, and speaker-notes preference using the required pre-execution questions.
2. Identify the user-specified PDF sections, page ranges, chapters, figures, tables, or topics to cover.
3. Analyze each specified part for:
   - Content amount / density
   - Conceptual importance
   - Narrative priority
   - Figure, table, chart, formula, or diagram importance
   - Whether the content needs one slide, multiple slides, or only a brief mention
4. Allocate PPT slide counts to each content area according to that analysis.
5. Generate a complete slide outline before creating the PPT.
6. For each planned slide, let `Presentation` define the background and image-asset requirements, then call `img-gen` to create the required background image or supporting image assets.
7. Use `Presentation` to build the PPT and insert each generated background/image asset.
8. Use `Presentation` to handle layout and add the slide outline or condensed PDF-derived content on top of the images as editable PowerPoint text objects.
9. Add speaker notes only if the user requested them.
10. Verify that important PDF-derived text remains editable and that the final deck follows the outline.

## Slide Count Allocation Rule

Before producing slides, decide the number of PPT pages for each requested PDF part. Do not assign pages only by equal PDF page count.

Consider these factors:

- **Content volume:** dense text, multiple claims, many subtopics, or long explanations need more slides.
- **Importance:** core methods, key findings, major arguments, main contributions, and essential conclusions need more slides even if the PDF section is short.
- **Visual complexity:** important figures, tables, experimental results, architecture diagrams, or formulas may need separate slides.
- **Presentation flow:** background and transitions should be concise; method, analysis, results, and conclusions usually need more space.
- **Audience needs:** difficult concepts may need explanatory slides; familiar or low-priority content can be summarized.

### Suggested Allocation Heuristics

Use these as defaults unless the user gives a fixed slide count:

- Cover / title: 1 slide
- Agenda / contents: 1 slide when useful
- Background / motivation: 1–3 slides
- Problem statement / objectives: 1–2 slides
- Related work / context: 1–3 slides
- Method / design / framework: 2–5 slides depending on complexity
- Data / experiment setup / implementation: 1–3 slides
- Results / analysis / discussion: 2–6 slides depending on importance and number of visuals
- Case study / application: 1–3 slides
- Conclusion / outlook: 1–2 slides
- Thanks / Q&A: 1 slide when appropriate

If the user specifies a target total slide count, distribute slides proportionally by importance and density while preserving a coherent presentation flow.

## Required Outline Before Generation

Before generating any backgrounds or slides, create a `slide_outline` with:

- Target total slide count
- Slide number
- Slide title
- Purpose / key message
- Source PDF page range or specified section
- Content priority level: high / medium / low
- Reason for assigning this slide
- Main editable text to include
- Visual idea for the background image
- Confirmed PPT style, using **academic minimalist style** when no custom style is provided
- PDF figures, tables, screenshots, charts, or diagrams to reuse or reference
- Notes about what may remain image-based
- Speaker notes draft when the user requested notes, or `no speaker notes` when not requested

For decks over 10 pages, also create a `batch_plan` with:

- Target total slide count
- Slide titles
- Source PDF page ranges or sections
- Batch number
- Visual style notes
- Which text must be editable
- Which elements may be image-based

## Per-Slide `img-gen` Background Rule

After the outline is approved or finalized, `Presentation` should define each slide’s image needs, then `img-gen` should create the background image and any supporting image assets required by `Presentation` before editable text is added through `Presentation`.

For every `img-gen` background or image-asset prompt, include the requirements supplied by `Presentation`:

- Slide number and title
- Slide purpose / key message
- Confirmed overall deck style; default to **academic minimalist style** if no custom style was specified
- Color palette
- `Presentation` layout direction, required image dimensions, and safe areas for editable text
- Whether the background should be abstract, academic, business, technical, minimal, or illustration-heavy
- Relevant PDF visual references when available
- Instruction to avoid embedding final readable body text in the image background

### Background Requirements

- Generated backgrounds and image assets should support the slide narrative without replacing editable content or `Presentation` layout responsibilities.
- Avoid putting critical titles, bullet points, conclusions, or PDF-derived main text inside any generated image.
- Leave clear readable areas where `Presentation` will place PowerPoint text boxes.
- Use visual metaphors, subtle diagrams, section motifs, decorative shapes, or lightly blurred PDF visual references where helpful.
- If `Presentation` requests a chart, figure, table, or other supporting image, `img-gen` may create or adapt that image asset, but its meaning must be explained with editable text added by `Presentation`.

## Speaker Notes Rule

Generate speaker notes only after asking the user whether notes are needed.

- If the user requests notes, create concise presenter notes for each slide based on the outline and corresponding PDF content. Notes should explain the slide narrative, transitions, and key talking points without duplicating every bullet verbatim.
- If the user does not request notes, do not add speaker notes.
- If the user has not answered and execution must continue, use **no speaker notes** as the default, but document that default in the outline.
- Speaker notes do not replace editable slide text; important titles, bullets, and conclusions still need editable slide objects.

## Text Editability Requirement

Only the **main text from the source PDF or generated outline** must be editable in the PPT.

Main editable text includes:

- Slide titles derived from the PDF or outline
- Section titles
- Key bullet points
- Core conclusions
- Main paragraph summaries
- Important labels that represent the PDF’s essential meaning
- Important table values when they are central to the slide argument
- Short explanatory notes needed to understand image-based figures or charts

These must be recreated as real PowerPoint text objects, such as:

- Editable text boxes
- Editable shape text
- Editable table cells when practical

## What Can Be Images

The following may be inserted as images when this improves visual fidelity or efficiency:

- `img-gen` background images
- PDF page backgrounds or cropped PDF regions
- Decorative layouts
- Charts and figures
- Screenshots
- Diagrams
- Tables whose values are not central to the slide argument
- Icons, logos, illustrations, photos
- Complex formulas or visual elements that are not intended for editing
- Original PDF visual regions used as reference images

It is acceptable for a slide to use an image as the visual base, as long as the important text from the PDF or outline is separately recreated as editable PPT text where needed.

## Forbidden Behavior

Do not start execution without first asking which part of the PDF should be used, what PPT style should be used, and whether speaker notes should be generated, unless those answers are already explicit in the user request.

Do not proceed if the PDF scope is unknown.

Do not start by generating slides before analyzing the specified PDF content and producing a slide allocation outline.

Do not assign the same number of slides to each PDF section without considering content amount and importance.

Do not skip the per-slide `img-gen` background step unless the user explicitly asks not to use generated backgrounds; additional generated image assets should only be created when requested by `Presentation`.

Do not satisfy the task by only inserting full-page PDF screenshots if the PDF’s main text is not editable.

Do not rasterize all meaningful text into images.

Do not convert titles, key points, or important conclusions into non-editable image text.

Do not use OCR-only image text as the final editable content when reliable extracted PDF text is available.

Do not embed final bullet lists or critical readable paragraphs into `img-gen` backgrounds.

## Recommended PDF-to-PPT Construction Approach

For each slide:

1. Read the corresponding PDF section/page range, confirmed PPT style, speaker-notes preference, and approved outline entry.
2. Use `Presentation` to define the needed background and supporting image assets, including dimensions, style, safe areas, and visual references.
3. Generate or reuse the planned `img-gen` background image and any `Presentation`-requested image assets using the confirmed style, defaulting to academic minimalist style when unspecified.
4. Use `Presentation` to insert the background as a full-slide image and place any generated supporting image assets.
5. Extract the main text from the corresponding PDF section/page.
6. Rewrite or condense the main text for presentation use.
7. Use `Presentation` to add the rewritten title, key points, and conclusions as editable PPT text boxes.
8. Use `Presentation` to add essential PDF figures, charts, diagrams, or tables as images when helpful.
9. Use `Presentation` to add editable captions, labels, explanations, or takeaways for important visuals.
10. Use `Presentation` to add speaker notes only when requested by the user.
11. Keep the layout clean and readable, using the background safe areas.

## Batch Planning for Decks Over 10 Slides

For PPTs over 10 pages:

1. Analyze the whole requested PDF scope first.
2. Create the complete slide count allocation and slide outline.
3. Split the outline into batches of 5–10 slides.
4. Use `Presentation` to drive batch slide layouts and call `img-gen` only for the backgrounds or image assets required by those layouts.
5. Use `Presentation` to assemble or merge the batches into one final `.pptx`.
6. Run consistency and editability checks.

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

All slides and batches must share:

- Same slide size
- Same theme colors
- Same title style
- Same body font style
- Same page numbering format
- Same spacing rules
- Same section divider style
- Same background image art direction based on the confirmed PPT style
- Consistent overlay treatment for editable text
- Consistent speaker notes behavior across all slides

## Merge Rule

Prefer generating a single final deck from a unified slide specification with `Presentation` rather than physically concatenating separate `.pptx` files.

If separate batch files are created, merge them only after checking:

- Slide order
- Page numbers
- Font consistency
- Theme consistency
- Master/layout consistency when possible
- Background image and `Presentation`-requested image asset consistency
- Editable main text presence
- Speaker notes presence or absence according to the user preference

## Quality Check

Before final output, verify:

- Total slide count matches the planned or requested count
- Slide count allocation matches the PDF content amount and importance analysis
- Slide order matches the outline
- Every slide has a generated or intentionally selected background image, and any extra `img-gen` assets were requested by `Presentation`
- Every slide has editable title or main heading when applicable
- Main PDF-derived or outline-derived text is editable
- Non-essential graphics may remain images
- No critical content is only present as flattened image text
- Page numbers are continuous
- No repeated placeholder text
- No obvious batch-to-batch style drift
- Background images and assets leave enough contrast and whitespace for `Presentation` editable content
- Speaker notes are present only if requested, and absent when not requested

## Recovery Strategy

If a generated slide or batch looks weak, regenerate only that slide or batch instead of rebuilding the whole deck.

Common fixes:

- Reallocate slides if a section is too compressed or too sparse
- Reduce text density
- Turn long paragraphs into editable bullets
- Regenerate a background with larger safe areas or lower visual complexity
- Use PDF visuals as reference images rather than full-slide screenshots
- Recreate only the main text as editable overlays
- Simplify complex tables into editable summaries

## Summary Principle

First ask which PDF content to use, what PPT style to apply, and whether to generate speaker notes. Use academic minimalist style by default when style is unspecified. Then analyze the specified PDF content and decide slide allocation by amount and importance. Then create the outline. Then use `Presentation` to define image needs, call `img-gen` only for backgrounds and `Presentation`-requested image assets, and use `Presentation` for all remaining PPT construction, editable outline/PDF-derived content, speaker notes, batching, and merging.

For decks over 10 slides: split, generate, check, and merge.

From PDF: main text editable; background and non-essential visual elements may be images.
