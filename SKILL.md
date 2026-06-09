---
name: cohen_ppt
description: Automatically plan PDF-to-PPT decks by confirming scope, notes, and one visual-source choice (style description, logo-derived template, or template-library PPT); saving generated templates as reusable PPTX files; then allocating slide counts, creating img-gen backgrounds, adding content with Presentation, and batching decks over 10 pages.
---

# PPT Batch Split, Template Strategy, Image Background & Content Skill

## Purpose

Use this skill for PDF-to-PPT tasks such as thesis defenses, reports, proposals, teaching slides, project summaries, and other decks derived from specified portions of a PDF.

Before execution, the workflow must first confirm four inputs with the user: which part of the PDF should be converted into PPT, whether to build the PPT narrative from the PDF content or from a user-provided custom production idea, which **one** visual source to use, and whether speaker notes should be generated. The visual-source choice is mutually exclusive: **style description**, **logo-derived template**, or **template-library PPT reuse**. Do not ask for a separate style when the user has already chosen a logo-derived template or reusable PPT template; their logo/prompt or selected template is the style source. If the user chooses style description and does not specify a style, use the default **academic minimalist style** for the deck and for every `img-gen` background prompt. If the user chooses a logo-derived template, collect the logo asset and template prompt before creating the deck theme and save the generated reusable template as a `.pptx` file before or alongside the final deck. After confirming these inputs, analyze the requested PDF sections, decide how many PPT slides each content area deserves based on **content volume and importance**, generate a complete outline, create a custom background image for every slide with `img-gen`, and finally add the outline or PDF-derived content into the PPT with `Presentation`.

When the requested or planned PPT has more than 10 pages, do not generate the whole deck in one pass. Generate slides in smaller batches, then merge them into one consistent final deck.

## Implementation Technology

Use **`img-gen + Presentation`** as the implementation technology for this skill:

- Use `img-gen` only to generate slide background images, style-description/logo-template decorative backgrounds or motifs, and any additional image assets requested by `Presentation` for a slide.
- Use `Presentation` for all remaining work: planning execution, visual-source resolution, template-library PPT reuse, style-description template creation, logo/prompt-based template system creation, saving generated templates as reusable `.pptx` files, PPT creation, layout, inserting generated images, text boxes/shapes/tables, optional speaker notes, page numbering, batch assembly, and merging.
- `Presentation` defines the image requirements for `img-gen`, including dimensions, style, layout intent, visual references, and content safe areas.
- Let `Presentation` handle final readable titles, bullet points, conclusions, and other critical PDF-derived text unless the user explicitly requests image-based output.
- When checking quality, verify both sides of the implementation: the resolved template system is applied consistently, `img-gen` backgrounds/assets satisfy `Presentation` requirements, and `Presentation` content is present.

## Required Pre-Execution Questions

Before analyzing the PDF or generating any outline, backgrounds, templates, or PPT files, ask the user these questions unless the answer is already explicit in the request:

1. **Which part of the PDF should be used to generate the PPT?** Examples: page ranges, chapters, sections, figure/table ranges, or named topics.
2. **What PPT production idea should be used?** Ask whether the deck should follow an idea inferred from the PDF content, or a custom production idea provided by the user. If the user chooses a custom idea, collect the custom narrative, audience, emphasis, structure, or slide-making requirements before outlining.
3. **Which single visual source should be used?** The user must choose exactly one of these mutually exclusive options:
   - **Style description:** collect the deck style / mood / color / audience / constraints. If unspecified, default to **academic minimalist style**. Use `Presentation` to turn this style into a reusable template system, and save that generated template as a `.pptx`.
   - **Logo-derived template:** collect the logo file/path/image reference and a template prompt describing brand, audience, mood, colors, and constraints. Use the logo/prompt as the visual style source, not a separate style question. Save the generated template as a reusable `.pptx`.
   - **Template-library PPT reuse:** ask the user to choose or describe the preferred library `.pptx` template. Use that template as the visual style source, not a separate style question.
4. **Should speaker notes be generated for the PPT?** Ask whether to create notes for each slide. If the user says yes, add concise presenter notes derived from the outline/PDF content. If the user says no, do not add notes.

Do not proceed to slide allocation, outline generation, template creation/selection, image generation, or PPT creation until the PDF scope, PPT production idea, and visual-source choice are known. If speaker-notes preference is missing and the user cannot be reached, use **no speaker notes** as a safe default. If the visual-source choice is style description but no style details are available, use **academic minimalist style** as the default style description.


## Visual Source and Template Strategy

Before generating any PPT file, the user must choose exactly one of three visual-source paths. These paths are mutually exclusive; do not combine a separate style-description request with logo-derived styling or template-library reuse unless the user explicitly asks to override the chosen source.

1. **Style description**
   - Ask the user for style, mood, audience, palette, density, and constraints; if missing, default to **academic minimalist style**.
   - Use `Presentation` to convert the style description into a reusable template system: cover slide, agenda slide, section divider, content slide, visual/chart slide, summary slide, and closing/Q&A slide where appropriate.
   - Save the generated template system as a reusable `.pptx` file, record its path in the plan, and use it as the source for the final deck and future exports.
   - Use `img-gen` only for template backgrounds, decorative motifs, or supporting image assets requested by `Presentation`; do not place placeholder labels, slide titles, bullet text, or core content into template background images unless requested.

2. **Reuse a template-library PPT template**
   - Ask the user to select a template by name, path, category, thumbnail, or description.
   - Use `Presentation` to inspect the selected `.pptx` and preserve its reusable theme elements: master layouts, colors, fonts, title/body styles, section dividers, placeholder geometry, page-number style, and visual motifs.
   - Adapt the generated deck outline to the selected template rather than converting the whole template into screenshots.
   - If the selected template conflicts with readability requirements, keep the template's brand direction but adjust layouts through `Presentation`.

3. **Generate a new template from logo and prompt**
   - Require a logo asset or clear logo reference plus a template prompt before creating the template. The prompt should describe brand personality, audience, mood, preferred or forbidden colors, industry context, and any layout constraints.
   - Analyze the logo for palette, contrast, geometry, visual rhythm, and typography cues. Combine that analysis with the prompt; do not ask for or apply a separate style description unless the user explicitly asks for an override.
   - Use `Presentation` to create a reusable template system: cover slide, agenda slide, section divider, content slide, visual/chart slide, summary slide, and closing/Q&A slide where appropriate.
   - Use `img-gen` only for template backgrounds, decorative motifs, or supporting image assets requested by `Presentation`; do not place placeholder labels, slide titles, bullet text, or core content into template background images unless requested.
   - Save the generated template system as a reusable `.pptx` file and record its path so future deck generations can reuse it or export from it.

In all paths, the resolved template must be applied before per-slide background generation so every `img-gen` prompt and `Presentation` layout follows the same visual system. For generated templates (style-description or logo-derived), the reusable template `.pptx` should be produced and saved before or alongside the final content deck.

## Core Workflow

For every PDF-to-PPT task:

1. Confirm the PDF scope, PPT production idea, one visual-source/template strategy, and speaker-notes preference using the required pre-execution questions.
2. Resolve the PPT production idea before slide allocation:
   - If using the PDF-content idea, infer the storyline, emphasis, and slide structure from the confirmed PDF scope, content density, importance, and narrative priority.
   - If using a custom idea, align the slide allocation, outline, emphasis, section order, and visual hierarchy to the user-provided custom narrative while still grounding content in the confirmed PDF scope.
   - Record the chosen idea mode and custom instructions in the plan so `Presentation` can follow them consistently.
3. Resolve the visual-source/template strategy before slide generation:
   - If using a style description, derive the theme colors, typography direction, master layouts, cover/section/content/closing slide patterns, safe areas, and background art direction; then save the reusable generated template as a `.pptx`.
   - If reusing a library template, inspect or load the selected `.pptx` template, then map its theme colors, fonts, master layouts, section dividers, placeholders, and reusable visual components into the deck plan.
   - If generating a new logo-based template, analyze the provided logo and prompt, derive a brand palette, typography direction, master layouts, cover/section/content/closing slide patterns, safe areas, and background art direction before creating per-slide backgrounds; then save the reusable generated template as a `.pptx`.
   - Record the selected or generated template source path in the plan, and ensure `Presentation` applies it consistently across all slides and batches.
4. Identify the user-specified PDF sections, page ranges, chapters, figures, tables, or topics to cover.
5. Analyze each specified part for:
   - Content amount / density
   - Conceptual importance
   - Narrative priority
   - Figure, table, chart, formula, or diagram importance
   - Whether the content needs one slide, multiple slides, or only a brief mention
6. Allocate PPT slide counts to each content area according to that analysis.
7. Generate a complete slide outline before creating the PPT.
8. For each planned slide, let `Presentation` define the background and image-asset requirements, then call `img-gen` to create the required background image or supporting image assets.
9. Use `Presentation` to build the PPT and insert each generated background/image asset.
10. Use `Presentation` to handle layout and add the slide outline or condensed PDF-derived content on top of the images.
11. Add speaker notes only if the user requested them.
12. Verify that important PDF-derived content is represented and that the final deck follows the outline.

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
- Main text to include
- Visual idea for the background image
- PPT production idea mode and any custom narrative or structure requirements
- Template strategy and per-slide template layout type to apply
- Resolved visual source and style notes; use **academic minimalist style** only when the chosen source is style description and no custom style is provided
- PDF figures, tables, screenshots, charts, or diagrams to reuse or reference
- Notes about what may remain image-based
- Speaker notes draft when the user requested notes, or `no speaker notes` when not requested

For decks over 10 pages, also create a `batch_plan` with:

- Target total slide count
- Slide titles
- Source PDF page ranges or sections
- Batch number
- Visual style notes
- Template source or generated style-description/logo-derived template system to keep consistent
- Which text/content to include
- Which elements may be image-based

## Per-Slide `img-gen` Background Rule

After the outline is approved or finalized, `Presentation` should define each slide’s image needs, then `img-gen` should create the background image and any supporting image assets required by `Presentation` before final content is added through `Presentation`.

For every `img-gen` background or image-asset prompt, include the requirements supplied by `Presentation`:

- Slide number and title
- Slide purpose / key message
- Resolved visual source style notes; default to **academic minimalist style** only for the style-description path when no custom style was specified
- Color palette
- `Presentation` layout direction, required image dimensions, and safe areas for text
- Whether the background should be abstract, academic, business, technical, minimal, or illustration-heavy
- Relevant PDF visual references when available
- Instruction to keep the background readable and unobtrusive

### Background Requirements

- Generated backgrounds and image assets should support the slide narrative without replacing `Presentation` layout responsibilities.
- Keep generated images focused on visual support rather than overcrowding them with dense text.
- Leave clear readable areas where `Presentation` will place PowerPoint content.
- Use visual metaphors, subtle diagrams, section motifs, decorative shapes, or lightly blurred PDF visual references where helpful.
- If `Presentation` requests a chart, figure, table, or other supporting image, `img-gen` may create or adapt that image asset, but its meaning should be explained by content added with `Presentation`.

## Speaker Notes Rule

Generate speaker notes only after asking the user whether notes are needed.

- If the user requests notes, create concise presenter notes for each slide based on the outline and corresponding PDF content. Notes should explain the slide narrative, transitions, and key talking points without duplicating every bullet verbatim.
- If the user does not request notes, do not add speaker notes.
- If the user has not answered and execution must continue, use **no speaker notes** as the default, but document that default in the outline.

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
- Complex formulas or visual elements
- Original PDF visual regions used as reference images

It is acceptable for a slide to use images for backgrounds, figures, diagrams, tables, formulas, icons, logos, illustrations, photos, or original PDF visual regions when this improves visual fidelity or efficiency.

## Forbidden Behavior

Do not start execution without first asking which part of the PDF should be used, whether to follow the PDF-content production idea or a user-provided custom production idea, which single visual source to use (style description, logo-derived template, or template-library PPT reuse), and whether speaker notes should be generated, unless those answers are already explicit in the user request.

Do not proceed if the PDF scope, PPT production idea, or visual-source/template strategy is unknown.

Do not start by generating slides before resolving the PPT production idea and visual-source/template strategy, analyzing the specified PDF content, and producing a slide allocation outline.

Do not assign the same number of slides to each PDF section without considering content amount and importance.

Do not generate a logo-based template without a logo asset/reference and prompt. Do not ask for a separate style description when the logo/prompt is the chosen visual source. Do not ignore the selected template-library PPT when the user chose template reuse.

Do not skip the per-slide `img-gen` background step unless the user explicitly asks not to use generated backgrounds; additional generated image assets should only be created when requested by `Presentation`.

Do not overcrowd `img-gen` backgrounds with dense readable paragraphs.

## Recommended PDF-to-PPT Construction Approach

For each slide:

1. Read the corresponding PDF section/page range, resolved PPT production idea, resolved visual-source/template strategy, speaker-notes preference, and approved outline entry.
2. Apply the selected template-library PPT template or generated logo-based template system through `Presentation`, preserving layout structure and theme consistency.
3. Use `Presentation` to define the needed background and supporting image assets, including dimensions, style, safe areas, and visual references.
4. Generate or reuse the planned `img-gen` background image and any `Presentation`-requested image assets using the resolved visual source, defaulting to academic minimalist style only for the style-description path when unspecified.
5. Use `Presentation` to insert the background as a full-slide image and place any generated supporting image assets.
6. Extract the main text from the corresponding PDF section/page.
7. Rewrite or condense the main text for presentation use.
8. Use `Presentation` to add the rewritten title, key points, and conclusions.
9. Use `Presentation` to add essential PDF figures, charts, diagrams, or tables as images when helpful.
10. Use `Presentation` to add captions, labels, explanations, or takeaways for important visuals.
11. Use `Presentation` to add speaker notes only when requested by the user.
12. Keep the layout clean and readable, using the background safe areas.

## Batch Planning for Decks Over 10 Slides

For PPTs over 10 pages:

1. Analyze the whole requested PDF scope first.
2. Create the complete slide count allocation and slide outline.
3. Split the outline into batches of 5–10 slides.
4. Use `Presentation` to drive batch slide layouts and call `img-gen` only for the backgrounds or image assets required by those layouts.
5. Use `Presentation` to assemble or merge the batches into one final `.pptx`.
6. Run consistency checks.

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
- Same selected template-library source or generated style-description/logo-derived template system
- Same title style
- Same body font style
- Same page numbering format
- Same spacing rules
- Same section divider style
- Same background image art direction based on the resolved visual source
- Consistent overlay treatment for content
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
- Template source, master layouts, or generated style-description/logo-derived template system consistency
- Speaker notes presence or absence according to the user preference

## Quality Check

Before final output, verify:

- Visual-source/template strategy was confirmed before PPT generation
- Exactly one visual source was used: style description, logo-derived template, or template-library PPT reuse
- Selected template-library PPT or generated style-description or logo/prompt-based template was applied before per-slide generation
- Generated style-description or logo-based templates were saved as reusable `.pptx` files
- Logo-based template generation used both a logo asset/reference and prompt when requested
- Total slide count matches the planned or requested count
- Slide count allocation matches the PDF content amount and importance analysis
- Slide order matches the outline
- Every slide has a generated or intentionally selected background image, and any extra `img-gen` assets were requested by `Presentation`
- Page numbers are continuous
- No repeated placeholder text
- No obvious batch-to-batch style drift
- Background images and assets leave enough contrast and whitespace for `Presentation` content
- Speaker notes are present only if requested, and absent when not requested

## Recovery Strategy

If a generated slide or batch looks weak, regenerate only that slide or batch instead of rebuilding the whole deck.

Common fixes:

- Reallocate slides if a section is too compressed or too sparse
- Reduce text density
- Turn long paragraphs into concise bullets
- Regenerate a background with larger safe areas or lower visual complexity
- Use PDF visuals as reference images rather than full-slide screenshots
- Recreate only the weak slide elements
- Simplify complex tables into presentation-friendly summaries

## Summary Principle

First ask which PDF content to use, whether to follow a PDF-content production idea or a user-provided custom production idea, which single visual source to use (style description, logo-derived template, or template-library PPT reuse), and whether to generate speaker notes. Use academic minimalist style by default only when the chosen visual source is style description and style details are unspecified. Then analyze the specified PDF content and decide slide allocation by amount and importance. Then create the outline. Then use `Presentation` to define image needs, call `img-gen` only for backgrounds and `Presentation`-requested image assets, and use `Presentation` for all remaining PPT construction, outline/PDF-derived content, speaker notes, batching, and merging.

For decks over 10 slides: split, generate, check, and merge.

From PDF: background and visual elements may be images when appropriate.
