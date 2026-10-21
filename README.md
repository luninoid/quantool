# Quantool

Quick Annotation Tool - a portable, offline image and PDF annotation app in one HTML file.

Import images or PDF pages, mark them up, and save an editable copy or export the annotated pages. No account, server, installation, or internet connection is required to use the downloaded file.

This is a vibe coded proof of concept and provided as-is.

## Getting started

1. Download [quantool.html](quantool.html). On GitHub, download the file itself rather than saving the source-view webpage.
2. Open it in a modern browser with JavaScript enabled.
3. Choose **Import** to select images or a PDF. You can select several images together, drop files into the workspace, or paste an image.
4. Review each imported page: **Auto-detected**, **Full/unchanged**, **Manual**, or **Ignore**.
5. Annotate, then choose **Export**. Save as HTML or project JSON when you want to keep editing later.

You can also start with a blank, ruled, or gridded page from the bottom toolbar.

## Features

- **Drawing:** freehand pen, separate translucent highlighter, object eraser, and undoable Clear.
- **Shapes:** rectangle, rounded rectangle, pill, cloud, ellipse, circle, diamond, line, arrow, double-ended arrow, and polygon.
- **Snapping:** horizontal/vertical lines and polygon segments, existing-corner snapping, and Shift constraints for equal-proportion shapes.
- **Rich text:** size, weight, italic, underline, strikethrough, subscript/superscript, monospace, colors, tables, and left/center/right/justified alignment.
- **Math:** Markdown, inline `$…$` and display `$$…$$` math, visible fraction bars, and shorthand such as `\ohm` → Ω.
- **Special characters:** math, science, geometry, annotation marks, and outline-style emoji.
- **Reusable content:** rich-text stamps and templates, current-date stamps, sequential numbering, and symbol stamps.
- **Sticky notes:** compact markers with content shown on hover; note content is included in flattened exports.
- **Graphs and images:** 10×10 and 14×14 Cartesian planes, number lines, polar graphs, diamond-method X diagrams, and imported images. Resize them proportionally and add attached annotations that move and scale with them.
- **Pages:** multipage documents; white or black blank, college-ruled, wide-ruled, square-grid, and dot-grid pages.
- **Image correction:** four-corner perspective crop, classical corner detection, optional stretch to Letter, contrast, monochrome, sharpening, and rotation.
- **Workspace:** light/dark/system themes, draggable side and page toolbars, a fixed top bar with the Quantool name in small caps, and zoom presets with Fit.
- **Portable settings:** paper size/proportions, defaults, stamps, templates, optional custom color picker, imported-page deletion protection, and Reset to defaults.

Factory sizes are **6 pt pen** and **24 pt text**. The preset palette is black/gray/white, blue/red/green, and purple/yellow/orange.

## Everyday controls

Both the main workspace and image editor open with **Pen** selected. Use **Move** to drag annotations. Double-click text in Move to edit it; double-click a graph or placed image in Move or Image mode to open its own annotation editor. Drag graphic corner handles to resize while retaining proportions.

Click polygon corners in sequence, then click the first corner to close the polygon. Drag the dots next to the pen and A pictograms to adjust sizes, or enter point values directly.

Stamps are centered on the point you click. Add custom stamps and templates through the full text editor. Custom items have remove controls. Factory items can be removed by omitting them from an imported settings profile; Reset to defaults restores them. The starting template contains Who?, What?, When?, Where?, and Why?, each on its own line.

## Keyboard shortcuts

Shortcuts are inactive while typing in text fields and editors.

| Key | Action |
| --- | --- |
| Q | Move |
| W | Pen |
| S | Activate the remembered shape; press again while a shape is active to cycle shapes |
| T | Text |
| Y | Stamp |
| G | Image |
| H | Highlighter |
| N | Sticky note |
| E | Erase |
| I | Image adjustments |
| C | Cycle colors |
| F | Fit view |
| R | Rotate clockwise |
| Escape | Return to Move / close tool popovers |
| Delete or Backspace | Delete the selected annotation |
| Ctrl/Cmd+Z | Undo |
| Ctrl/Cmd+Shift+Z or Ctrl/Cmd+Y | Redo |
| Ctrl/Cmd+Enter | Commit text |

## Saving and export

| Format | Use |
| --- | --- |
| HTML | Self-contained editable document with embedded pages, annotations, assets, and app |
| Project JSON | Editable document data for importing back into Quantool |
| PNG | Flattened current page |
| PDF | Flattened current page or all pages |
| Settings JSON | Reuse preferences, sizes, stamps, and templates across documents |

Settings export is separate from project export. It excludes page content, annotation history, and toolbar positions. Toolbar positions reset when a saved HTML document opens.

Quantool retains the existing `annotation-engine` project and `annotation-engine-settings` settings format identifiers for compatibility with earlier files.

## Offline operation and limitations

The HTML contains the application code and PDF reader resources. Imported files are processed in the browser; no upload is required. Save a document file to retain your work rather than relying on the open browser tab.

PDF pages are imported as rendered images, and exported PDFs are flattened raster pages. Searchable PDF text, OCR, collaboration, and full LaTeX package support are outside the current scope. Automatic corner detection can fail on difficult photos; Full/unchanged and Manual remain available. Large documents consume browser memory.

The app uses modern browser APIs, including canvas, local file access through file pickers, and native dialogs. Full browser visual compatibility testing has not been completed; existing checks cover DOM, native-canvas, PDF, and save/reopen workflows.

## Development

The distributed application is [quantool.html](quantool.html); no build step or package installation is required to run it. It contains inline CSS/JavaScript and embedded PDF.js resources. Keep dependencies and assets embedded when changing the app so the output remains a single offline file.

See the [recreation specification](quantool-spec.md) for interaction details, data schemas, offline requirements, and acceptance checks. After changes, verify import/crop, drawing, rich text/math, attached graph annotations, settings round trips, all export formats, and repeated HTML save/reopen cycles. Check that generated controls are not duplicated and no network requests are needed.

## Licensing

The application includes bundled third-party notices, including PDF.js licensing information. Preserve those notices when redistributing the HTML. A separate license for the Quantool application code has not yet been selected.
