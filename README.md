# MHTML to HTML Converter

A zero-dependency, client-side tool for converting `.mhtml` and `.mht` files into standalone HTML files. All processing happens locally in your browser; files are never uploaded to a server.

[中文版](README.zh-CN.md)

## Features

- Convert one or multiple `.mhtml` / `.mht` files at once.
- Select files by clicking the drop zone or drag and drop them into the page.
- Inline referenced images, stylesheets, frames, and other resources as Base64 Data URIs.
- Resolve resources referenced by `cid:`, absolute URLs, relative paths, filenames, `src`, `href`, `srcset`, and CSS `url(...)` values.
- Optimize HTML exported by Microsoft Word, WPS, and similar office software:
  - Remove Office conditional comments and metadata.
  - Remove Office namespace elements and declarations.
  - Remove `mso-*` styles, empty tags, and Office-only classes.
  - Deduplicate repeated inline styles into reusable CSS classes.
  - Compress redundant whitespace while preserving `pre`, `textarea`, and `code` content.
- Preview converted HTML in a new browser tab or download it as a `.html` file.
- Switch between Chinese and English; the selected language is remembered locally.

## Quick Start

No build step or dependency installation is required.

1. Open [`index.html`](index.html) directly in a modern browser.
2. Drop one or more `.mhtml` / `.mht` files onto the upload area, or click it to select files.
3. Choose the conversion options:
   - **Optimize output**: enabled by default; useful for Word/WPS-generated HTML.
   - **Inline resources**: enabled by default; embeds referenced resources into the output.
4. Click **Download HTML** to save the converted file, or **Preview** to open it in a new tab.

You can also serve the folder with any static HTTP server if your browser or environment restricts opening local files.

## How It Works

The converter is implemented entirely in [`index.html`](index.html):

1. Reads the selected file with the browser File API.
2. Extracts the MIME `boundary` from the MHTML header.
3. Parses each MIME part and decodes Base64, quoted-printable, or raw binary content.
4. Uses the first `text/html` part as the main document.
5. Replaces matching resource references with generated Data URIs.
6. Optionally applies HTML cleanup and style deduplication.
7. Normalizes the output charset to UTF-8 and adds a doctype when needed.

## Privacy

This is a local-only utility. The source file and converted content stay in the browser tab. No network request is made to upload files or process them remotely.

The generated Blob URLs are revoked when results are cleared or replaced. The selected language is stored in browser `localStorage` under `mhtml2html-language`.

## Limitations

- The input must contain a valid MIME boundary and at least one `text/html` part.
- The first `text/html` MIME part is treated as the main document.
- Resource matching is based on MHTML `Content-Location` / `Content-ID` values and filename fallbacks; ambiguous duplicate filenames may resolve to the first match.
- Very large files may require substantial browser memory because the file is read and converted in memory.
- The output is always saved as UTF-8. Resource inlining can increase file size when the original resource was not already Base64 encoded.
- Browser support is required for `File.arrayBuffer()`, `TextDecoder`, `Blob`, `URL.createObjectURL()`, and Data URI handling.

## License

This project is licensed under the [MIT License](LICENSE).
