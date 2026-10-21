# Quick Annotation Tool (Quantool) — Recreation Specification

**Baseline:** `quantool.html`, revision delivered October 15, 2026, following the shape-cycling and fixed-header changes.  
**Product name:** Quick Annotation Tool; short name **Quantool**.

**Purpose:** Recreate the current app as one self-contained HTML file, while retaining the interactions and editable document formats described below.

## 1. Product and delivery requirements

Build an image and PDF annotation workspace that runs entirely in a browser. Deliver one HTML file containing the interface, styles, application code, PDF reader, and required resources. It must open from a local file and work without an internet connection, installation, account, server, or build step for the end user.

Users must be able to import pages, annotate them, save an editable HTML copy, export a portable JSON project, and export flattened images or PDFs. Saving HTML must include the document’s image data, annotations, graph assets, preferences, stamps, and templates. Opening the saved file must restore an editable workspace.

Use pictograms and concise hover labels. Avoid instructional paragraphs, subtitles, onboarding overlays, or persistent help text in the product. Use short questions where a dialog requires user input. Settings labels and import choice labels may use plain text where needed to make the choice clear.

A development implementation may use multiple source files and a build process, but the delivered artifact must be a single offline HTML file. Preserve notices for any bundled third-party software.

## 2. Workspace layout

### Fixed top bar

Show **Quantool** left aligned in small caps, using the existing system font and CSS small-caps styling; do not add fonts. Keep the top bar fixed and non-draggable. Right-align these groups in order:

1. Zoom out, zoom level / Fit menu, zoom in.
2. Import.
3. Export.
4. Settings.

The Import pictogram must match Export’s tray-and-arrow design with the arrow reversed: Import points down into the tray; Export points up out of it.

### Left tool palette

Provide Move, Pen, Highlighter, Text, Sticky note, Shapes, Stamps, Image, and Erase. Shapes, Stamps, and Image expose their respective pickers. Make this palette draggable using a visible grip; dragging a control must not accidentally drag the palette.

### Right palette

Place the nine-color palette, optional custom color input, pen/highlighter size control, default text size control, Undo, Redo, and Clear here. Make this palette draggable.

### Bottom page toolbar

Provide Previous, page selector/count, Image adjustments, Crop, Rotate, Delete page, Add page, and Next. Make this toolbar draggable. Disable navigation at document boundaries, and disable page-dependent actions when there is no page. Respect the setting that prevents deletion of imported pages.

### Position behavior

Keep floating toolbars within the viewport while dragging. Toolbar coordinates are transient interface state: exclude them from settings exports and project data. The current baseline resets their positions when an HTML file opens. Reset to defaults also restores their initial positions.

## 3. Keyboard and pointer behavior

Shortcuts apply when the user is not typing in an input, select, text area, or rich-text editor. Do not intercept normal typing. Tool hover labels show the appropriate uppercase shortcut.

| Key | Behavior / hover label |
| --- | --- |
| Q | Move (Q) |
| W | Pen (W) |
| S | Shape (S): activate the remembered shape; if a shape tool is already active, cycle to the next shape |
| T | Text (T) |
| Y | Stamp (Y) |
| G | Image (G) |
| H | Highlighter (H) |
| N | Sticky note (N) |
| E | Erase (E) |
| I | Image Adjustments (I) |
| C | Cycle through the nine palette colors |
| F | Fit the current page / graph editor to its workspace |
| R | Rotate the active page clockwise by 90° |
| Escape | Return to Move and close tool popovers |
| Delete / Backspace | Delete the selected annotation when not typing |
| Ctrl/Cmd+Z | Undo |
| Ctrl/Cmd+Shift+Z or Ctrl/Cmd+Y | Redo |
| Ctrl/Cmd+Enter | Commit the text editor |

Repeated letter presses should not toggle back to Move. Shape cycling wraps around and updates the shape pictogram. A graph’s annotation editor supports the annotation shortcuts but does not permit recursively placing another graph through G. Modal dialogs prevent unrelated document shortcuts.

With Move active, click and drag an annotation to move it. Move operations preserve content; they do not change text or shape geometry. Double-click text to edit it. Double-click a graph/image in Move or Image mode to open its attached-annotation editor. The main workspace and the graphic editor both open with Pen selected. Selection must remain usable even though text box selection borders are hidden. Other annotation selection indicators and graph resize handles remain visible.

## 4. Pages and importing

### Accepted sources

- One or several image files selected in a single file-picker action.
- Image files dropped into the workspace.
- Images pasted from the clipboard when no modal editor is active.
- PDFs, including all pages of a multipage PDF.
- The app’s editable project JSON.
- Separately, a settings JSON file through Settings.

An import can append multiple images and PDF pages to the existing document. Preserve each file’s page order. Show loading/progress feedback for batch processing and handle malformed or unsupported files without corrupting the existing document.

### Per-page review

Scan each imported image or PDF page with classical page-boundary detection. For every page, show its position in the import queue and four choices:

| Choice | Result |
| --- | --- |
| Auto-detected | Perspective-correct using the detected quadrilateral |
| Full/unchanged | Keep the entire page without cropping |
| Manual | Open the four-corner crop editor, initially using detected corners when available |
| Ignore | Skip this page and continue to the next queued page |

Show previews for crop choices. Disable Auto-detected when there is no usable boundary. Cancel stops processing the remaining queue. Cancelling a manual crop must return to that page’s review without adding a page or changing the document.

### Classical corner detection

No machine-learning download is required. A compatible baseline algorithm is:

1. Downsample the image so its longest side is approximately 600 pixels.
2. Convert to grayscale and lightly blur.
3. Use Otsu thresholding to evaluate light and dark page regions.
4. Compute Sobel edges and dilate to connect boundaries.
5. Extract connected components, convex hulls, and four-corner candidates.
6. Rank candidates by area, shape plausibility, and support from image boundaries/edges.
7. Convert the best candidate back to original image coordinates and return confidence, or return no detection.

Full/unchanged and Manual must remain available even if detection fails. Do not silently crop based solely on detection.

### Generated pages

Offer white and black backgrounds, each with Blank, College rule, Wide rule, Square grid, and Dot grid patterns. Draw these locally; do not load background assets from a server. Rule/grid spacing should correspond to physical page units.

New pages use the paper size and orientation in Settings. Available sizes are US Letter, US Legal, A4, A5, Square, and Custom. Custom dimensions are entered in points (72 points = 1 inch), with a baseline range of 72–2,000 points per dimension. Support portrait and landscape proportions.

Changing the new-page size preference does not reshape existing pages. Preserve imported image proportions unless the user explicitly chooses a crop/stretch operation. The chosen physical page height calibrates point sizes and PDF output.

## 5. Drawing tools

### Pen and Highlighter

Pen records a freehand path with its color and width. Factory pen width is **6 pt**. Highlighter is its own tool, using a translucent multiply blend; the baseline opacity is 0.38 and factory width is 12 pt. Keep its size separate from the pen size.

Size controls contain a pen pictogram, draggable dot, numeric point value, and “pt” unit. Dragging the dot or numeric field horizontally or vertically adjusts the value; clicking still permits direct entry. Clamp pen/highlighter widths to 0.1–72 pt.

### Shapes

Use this order for the picker and S cycling:

1. Rectangle.
2. Rounded rectangle.
3. Pill.
4. Cloud.
5. Ellipse.
6. Circle.
7. Diamond.
8. Line.
9. Arrow.
10. Double-ended arrow.
11. Polygon.

Rectangle, rounded rectangle, pill, cloud, ellipse, and diamond use a corner-to-corner bounding box. Circle uses center-to-edge radius. Cloud has evenly distributed scallops around its entire boundary, including the bottom. Diamond connects the midpoint of each side of its dragged bounding box.

Shift constrains the bounding-box shapes to equal width and height. Near-horizontal and near-vertical lines/arrows snap to horizontal/vertical; Shift forces the nearer axis. Arrowheads point to the endpoint; double-ended arrows have a head at both ends.

Polygon is click-to-add-corners. Clicking its first corner after at least three vertices closes it. Polygon segments support horizontal/vertical snapping. Lines/arrows and polygons also snap to existing annotation corners. Existing-corner snapping takes priority over axis constraints. Scale screen-space snap tolerance with zoom so it remains practical at different magnifications.

### Erase and Clear

Erase removes entire touched annotations while the pointer is dragged. It is object erasure, not pixel erasure. An eraser gesture should be undoable as one operation.

Clear removes all annotations on the active page immediately, without a confirmation dialog. It must be undoable and redoable. Clear must also clear in-progress drawing/selection state. Removed objects must not appear in exported HTML project data, JSON, PNG, or PDF. History must not be serialized into saved deliverables.

Deleting a page is distinct from Clear: it removes the page and may ask a short confirmation question. Its effect is undoable. A protected imported page cannot be deleted through either the UI or the action handler.

## 6. Color and physical sizing

Display colors in three rows, ordered left-to-right:

| Row | Colors and baseline values |
| --- | --- |
| 1 | Black `#171c26`, Gray `#808080`, White `#ffffff` |
| 2 | Blue `#2864df`, Red `#e13c40`, Green `#24965e` |
| 3 | Purple `#8b4bc1`, Yellow `#f1c62b`, Orange `#ed8b23` |

The same palette is available in the text editor and graph editor. Factory active color is Red. Selecting a color changes the surrounding selection circle/ring to blue; the swatch’s own fill must keep its actual color. Provide an optional native custom color input. Settings can hide that custom picker without removing the nine preset colors.

Factory text size is **24 pt**. The default text control uses a capital A pictogram, draggable dot, numeric point value, and “pt” unit. Support direct entry and dragging; clamp text sizes to 1–200 pt. Built-in/plain-text stamps use the default size; a rich custom stamp can retain its explicitly chosen editor size.

Store annotation coordinates and rendered widths in page pixels. Convert user-facing point values through the page’s physical height. For an ordinary Letter page, pixels per point = image height / 792. Respect an explicit page physical height and a graph editor’s local scale. Zoom must not change stored annotation size.

## 7. Text and math editor

Click the page with Text active to open a full rich-text dialog. Double-click existing text in Move to edit it. Provide:

- Size with numeric entry and drag adjustment.
- Weight, italic, underline, strikethrough, subscript, and superscript.
- Sans-serif and monospace font options.
- Left, center, right, and justified alignment.
- The nine preset text colors.
- Table insertion with a short rows × columns question.
- Markdown source mode and rendered mode.
- LaTeX math and a special-character picker.
- Templates.
- Commit and Cancel controls.

Use pictograms for font style, weight, and alignment. Dropdowns/popovers must follow the active theme rather than showing unreadable bright-white controls. Hide the editor’s outer border and on-page text selection box borders; retain actual table cell rules and mathematical fraction bars.

Preserve the insertion caret when opening formatting controls, inserting symbols, or inserting a table. Tab moves between table cells; tabbing beyond the last cell can add a row. The baseline limits are 30 rows and 12 columns.

Markdown includes headings, emphasis/bold, inline code, strikeout, underline, sub/superscript, simple bullet lines, and pipe tables. Rendered math retains its TeX source so toggling back to source mode shows the code. Do not flatten math into an image in editable text.

Support `$…$` inline math and `$$…$$` display math. Required examples include `$\frac{1}{2}$`, roots, Greek symbols, subscripts, superscripts, and basic matrices. Fractions require a visible bar in the editor, saved HTML, PNG, and PDF. Use a self-contained renderer; the baseline is a lightweight TeX parser converted to HTML/CSS. Full LaTeX document compatibility is not required.

Recognize named shorthand such as `\ohm` → Ω both in normal text and inside math. Include common science/math names such as degree, celsius, angstrom, micro, hbar, pi, alpha, beta, Delta, and infty. Do not replace commands inside literal code spans. Unknown commands should remain understandable rather than breaking the editor.

The character picker has Math, Science, Shapes and geometry, Text annotations, and Outline emoji groups. Include operators, inequalities, Greek letters, units, arrows, geometry signs, common annotation marks, and text-style smile/frown, hearts, stars, weather, and hand symbols. Prefer text/outline presentation over colored emoji where the font supports it.

Sanitize rich HTML on commit/import. Remove scripts, event handlers, remote content, and arbitrary elements/styles while preserving supported formatting, tables, and math source metadata.

## 8. Sticky notes

Sticky Note opens the text editor and stores rich content with the note. Show a compact note marker on the page; in Move, hovering reveals its content in a nearby popover. Hide or reposition the popover as appropriate when moving, drawing, scrolling, or leaving the note. Notes can move and be erased. Flattened exports must include note content rather than requiring hover to read it.

## 9. Stamps and templates

### Stamps

Stamps are repeatable text-based annotations. Keep graphs and diagrams in the separate Image tool. Factory custom-text entries are APPROVED, REVIEW, and CONFIDENTIAL. Built-in choices include:

- Current date, evaluated when placed, formatted like **Sept 03, 2022** using local time.
- Sequential numbering: each click places the next integer; expose the next-number value.
- Smiley ☺︎, frowny ☹︎, star ☆, plus +.
- Right →, left ←, double-ended ↔, and double-stem ⇒ arrows.

Adding a stamp opens the full rich-text editor, including Markdown, math, characters, colors, and formatting. Committing saves a reusable stamp and activates Stamp; it must not place an unwanted page annotation. Each later click places an independent annotation centered horizontally and vertically on the click. Measure the rendered stamp, including rich multiline content, before positioning it.

Show remove controls for user-added stamps. Do not show remove controls for factory/default stamps. Defaults can only be removed by omitting them from an imported settings profile. Do not silently reinsert defaults that an imported profile intentionally excludes.

### Templates

Provide a Templates pictogram in the text dialog. Factory content is five separate lines:

```text
Who?
What?
When?
Where?
Why?
```

A template inserts reusable rich content into the text editor, with its formatting. It is not a separate annotation tool. Adding a template uses the full editor; retain the user’s original text draft and return to it after saving or cancelling template creation. An empty template commit must not discard that draft.

Allow removal of custom templates. The factory template has no remove button; remove it only through omission from an imported settings profile. Export/import custom and default collection membership with preferences. Reset to defaults restores factory stamps and templates.

## 10. Image and graph tool

Use a Cartesian-plane pictogram and the hover label **Image (G)**. Include:

| Graphic | Geometry |
| --- | --- |
| 10 × 10 Cartesian plane | Axes/ticks from −5 to +5; square |
| 14 × 14 Cartesian plane | Axes/ticks from −7 to +7; square |
| Number line | −7 through +7 with arrows at both ends |
| Diamond-method X | Two crossing diagonal lines |
| Polar graph, 15° | Concentric circles and radial spokes |
| Polar graph, 30° | As above, at 30° increments |
| Polar graph, 45° | As above, at 45° increments |
| Outline smile, frown, star, plus | Additional baseline graphic choices |
| Imported image asset | User-supplied image retaining its original aspect ratio |

Graphs are generated locally. Use legible, thicker lines: the current baseline uses approximately 2.4 canonical pixels for primary axes and 1.25 for grid/radial lines in a 320-pixel graph. Grid lines may be lighter than axes. Do not let a large pen size make the graph unusably heavy.

Place graphics by dragging from one corner toward the opposite corner. Lock proportions and support all drag directions. A click without a meaningful drag places nothing. In Move, provide corner resize handles that maintain proportions.

Double-click a placed graphic in Move or Image mode to open an editor dedicated to that graphic, initially with Pen selected. Permit pen, highlighter, shapes, text, stamps, notes, color, sizes, Undo/Redo, Clear, and Fit within it. Store its annotations as children in the graphic’s local coordinate system. Moving/resizing the parent must move/scale all children together. Commit updates the parent; Cancel leaves it unchanged. Local undo history must not overwrite the main document’s history. Preserve child annotations in HTML, JSON, PNG, and PDF.

## 11. Crop, adjustments, rotation, and zoom

Crop is a direct bottom-toolbar action. Image adjustments contains contrast/high contrast, monochrome, and sharpening. Preview adjustment changes, then Apply or Cancel. Preserve annotations while correcting the base image.

Crop exposes four draggable corners and draggable edges, Auto-detect, Reset, an optional **Stretch to US Letter** toggle, Apply, and Cancel. Edge dragging moves its supporting line parallel to itself: keep every edge’s direction fixed and recompute intersections/adjacent lengths. Corner dragging may reshape the quadrilateral. Perspective-correct the base image and apply the same homography to annotations, including grouped graph content, so marks remain registered to the image.

Without stretch, derive output width/height from opposite-edge lengths. Stretch explicitly produces Letter proportions. Bound output dimensions/memory; the baseline limits a crop to roughly 40 megapixels. Crop and rotation must be undoable.

Rotation advances in 90° clockwise steps. Fit accounts for rotation and the available workspace. Clicking the displayed zoom opens preset levels (25, 50, 75, 100, 125, 150, 200, 300, 400 percent) and **Fit (computed%)**. The plus/minus controls adjust zoom incrementally. Coordinate conversion, snapping, hit testing, and moving must remain correct at any supported zoom and rotation.

## 12. Settings and reset

Settings includes:

- Light, dark, and system theme controls.
- Custom color picker on/off.
- New-page paper size, portrait/landscape orientation, and custom point dimensions.
- Disable deleting imported pages.
- Import/export a preferences file containing settings, stamps, templates, built-in stamp membership, and defaults.
- Reset to defaults.

Settings exports include current pen/text/highlighter sizes, next sequence number, theme, and active/default color. Exclude pages, annotations, imported page image data, undo history, toolbar coordinates, and temporary editor state.

Reset immediately restores 6 pt pen, 24 pt text, 12 pt highlighter, sequence starting at 1, Red, system theme, Letter portrait, enabled custom color picker, ordinary imported-page deletion, factory collections, and initial toolbar positions. It must preserve page images and annotations, and be undoable. Imported settings must validate completely before mutating state; reject malformed profiles with a short dialog.

## 13. Save and export behavior

| Output | Scope and properties |
| --- | --- |
| HTML | Whole editable document; all resources/data embedded; no network needed on reopen |
| Project JSON | Whole editable document with pages, assets, groups, settings, stamps, and templates |
| PNG | Flattened current page, including annotations and note content; no selection UI |
| PDF | Flattened current page or all pages in order; preserve page proportions, physical sizing, rotation, corrections, and all annotations |
| Settings JSON | Reusable configuration and collections only |

PDF export may be raster-based; searchable/selectable PDF text and vector-preserving PDF import are not baseline requirements. PDF import renders pages locally through an embedded reader. The current implementation embeds PDF.js 6.4.299, its worker logic, CMaps, standard fonts, WASM, and color resources. Equivalent fully offline implementations are acceptable.

Disable conflicting controls during long exports, restore the original active page/zoom/selection afterward, and do not accidentally serialize loading states. Reopening HTML must rebuild runtime event listeners and controls exactly once. Ensure dynamically generated markup is parsed before the application initialization script runs. Repeated save/reopen cycles must not duplicate settings dialogs, theme controls, template buttons, or toolbar grips.

## 14. Data model and compatibility contract

### Project envelope

The current interchange envelope is `format: "annotation-engine"`, `version: 5`. `units: "pt-letter"` is the legacy unit tag; explicit page physical dimensions may override Letter calibration.

```json
{
  "format": "annotation-engine",
  "version": 5,
  "units": "pt-letter",
  "pages": [],
  "activePage": -1,
  "assets": {},
  "defaultSize": 24,
  "penSize": 6,
  "highlighterSize": 12,
  "nextNumber": 1,
  "stamps": ["APPROVED", "REVIEW", "CONFIDENTIAL"],
  "builtinStamps": ["@date", "@sequence", "@text:smile", "@text:frown", "@text:star", "@text:plus", "@text:right", "@text:left", "@text:both", "@text:double"],
  "templates": [],
  "theme": "system",
  "settings": {
    "colorPicker": true,
    "paperSize": "letter",
    "orientation": "portrait",
    "customWidth": 612,
    "customHeight": 792,
    "disableImportedDeletion": false,
    "defaultColor": "#e13c40"
  }
}
```

A normal factory document populates `templates` with the five-question template. An explicitly empty collection in imported data means empty; only a missing collection in a legacy project should receive migration defaults.

A page stores `id`, `image` (embedded data URL), `w`, `h` (pixels), `rotation`, `annotations`, `correction`, `name`, optional `paper` pattern/color, `imported`, and `paperSize: {w,h}` in points. Assets are keyed by ID and contain `src`, pixel dimensions `w`/`h`, and `name`.

### Annotation records

All annotations have stable IDs and a type. Relevant fields include:

| Kind | Additional fields |
| --- | --- |
| Pen/highlighter | `points: [{x,y}]`, pixel `stroke`, `color`, `blend`, `opacity` |
| Bounding/endpoint shape | `x`, `y`, `x2`, `y2`, pixel `stroke`, `color`, optional `headSize` |
| Polygon | `points`, `color`, `stroke` |
| Text/note | `x`, `y`, pixel `width` and `size`, sanitized `html`, `font`, `align`, `weight`, `color`, optional source |
| Graphic | `x`, `y`, pixel `width`/`height`, `kind`, optional `assetId`, `color`, `children` |

Highlighter uses a pen record with multiply blending and reduced opacity. Text stamps become ordinary text annotations when placed. Cropped annotations may carry a 3 × 3 homography (`warp`) instead of destructively rewriting all geometry. A graph’s children use canonical local graphic coordinates.

A rich stamp/template stores `id`, sanitized `html`, point `size`, `font` (`sans` or `mono`), `align`, numeric `weight`, `color`, and preferred point `width`. Plain string stamps remain supported for compatibility. The default question template carries `defaultId: "questions"` to protect it from UI deletion.

### Settings envelope

Use `format: "annotation-engine-settings"`, `version: 1`, and fields `settings`, `stamps`, `builtinStamps`, `templates`, and `defaults`. `defaults` contains `defaultSize`, `penSize`, `highlighterSize`, `nextNumber`, and `theme`. Import replaces the exported collections; omission of default entries removes those defaults. A missing collection in an older settings profile is treated as omitted, rather than silently repopulated.

Use bounded numeric values and sanitize every imported rich item. Do not accept settings data as executable code. For future schema changes, retain a migration path from these project and settings versions.

## 15. Implementation guidance

Use a page-local coordinate model with independent viewport transforms. A compatible renderer uses canvas for page imagery and drawing, plus HTML overlays for editable rich text and hover content. Flatten overlays with their actual formatting during image/PDF export. Use the same annotation model across view, hit testing, export, and serialization.

Separate document data from runtime state: DOM nodes, loaded image caches, gestures, selection, popover state, toolbar locations, and undo stacks do not belong in saved project data. Graph editing and manual import review should use isolated temporary contexts, restoring the parent state on cancellation.

Snapshot meaningful operations before mutation. Coalesce dragging and freehand/erase gestures into single undo steps. Clear Redo when a new operation follows Undo. Bound history by count and memory; the current baseline uses at most 75 snapshots and approximately 64 MB where practical.

Use native browser APIs where possible. Bundle every required dependency. Do not depend on CDNs, external fonts, service workers fetched over the network, analytics, remote rendering, OCR, or machine-learning services. Keep imported files local.

## 16. Acceptance checklist

1. Open the delivered HTML from disk with networking disabled. Import images and a text/vector/raster multipage PDF successfully.
2. Select several images at once. Review Auto, Full, Manual, and Ignore; verify queue order and cancellation without accidental additions.
3. Create each white/black patterned page and every paper format. Verify A4 landscape PDF dimensions and Letter point sizing.
4. Verify all shortcuts, including E and S cycling/wraparound. Typing inside editors must not activate tools.
5. Draw every shape in each drag direction. Check Shift proportions, axis snapping, polygon closure, and existing-corner snapping at different zooms.
6. Confirm selected swatches retain their fill and display only a changed selection ring.
7. Enter a fraction, table, shorthand `\ohm`, monospace text, underlining, and justified text. Verify source/rendered round trips and visible fraction bars in exports.
8. Add, repeat, and remove a rich custom stamp. Verify date format and sequence progression. Factory items must lack UI remove controls.
9. Insert the five-question template with five lines. Add a rich template; saving, cancelling, or committing it empty must preserve the previous text draft.
10. Export settings, omit default stamps/templates, and import. Confirm omissions persist across HTML/JSON save/reopen. Verify changed sizes round-trip and toolbar coordinates are absent.
11. Place and resize each graph. Annotate one with text and ink, move/resize it, save/reopen, and export. Children must remain attached and readable.
12. Drag the left, right, and bottom toolbars. Confirm the top bar has no drag grip, displays Quantool in small caps at the left without added fonts, and remains fixed.
13. Verify text selection has no box border, while graph resize handles and actual table/math rules still work.
14. Clear without confirmation, Undo, and Redo. Export each format and confirm deleted/erased/cleared objects do not reappear.
15. Exercise corner and edge cropping after rotation and after annotations exist. Edges retain directions during edge dragging; marks remain aligned after perspective correction.
16. Toggle themes in Settings, then save/reopen twice. Verify working controls and exactly one instance of each generated UI component.
17. Reset defaults, verify factory sizes/collections and unchanged page content, then Undo the reset.
18. Check native browser layout and interaction in addition to DOM/canvas/PDF tests. The delivered baseline was checked through DOM/native-canvas/PDF workflows; full browser visual validation was not available in its build environment.

## 17. Scope boundaries and historical gap

This baseline does not require real-time collaboration, remote storage, OCR, handwriting recognition, a sentence-diagramming engine, arbitrary LaTeX packages, editable vector PDF output, or automatic cropping without review.

An earlier request mentioned a “Good job” stamp inside a star. The current default catalog contains a star symbol and a star graphic, but no dedicated “Good job” star preset. Preserve that distinction when recreating the delivered tool; treat the dedicated preset as an explicit follow-up rather than assuming it is already implemented.
