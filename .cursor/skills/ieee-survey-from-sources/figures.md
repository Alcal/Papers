# Figures and media from `sources/`

## Principles

- Prefer **`TOPIC_DIR/figures/`** (or `media/`) for anything included with `\includegraphics`. Keeps paths stable and avoids multi-hundred-page PDFs as graphic inputs.
- **Attribution**: For third-party figures, credit in the caption (e.g. "Adapted from [12]") and ensure the paper is cited in `\bibitem`.
- Reuse for **scholarship/review** with proper citation; follow venue and copyright rules for the final submission target.

## Standalone files already in `sources/`

Copy or symlink into `figures/` with a stable name:

`fig_<shortSourceSlug>_<meaning>.png`

Example: `fig_kv_cache_block_diagram.png`.

## Figures inside a PDF page

1. Identify **page number** and crop region (full page vs box).
2. Raster export (common default):

```bash
pdftoppm -f N -l N -png -r 200 sources/some-paper.pdf figures/tmp_pageN
mv figures/tmp_pageN-N.png figures/fig_somepaper_architecture.png
```

3. Vector clip from a single page: extract with `pdfseparate` then `pdfcrop` if available.

4. Embedded images only: `pdfimages -list sources/some-paper.pdf` then `pdfimages -png ...`.

## LaTeX snippet (IEEE two-column)

```latex
\begin{figure}[htbp]
\centering
\includegraphics[width=\linewidth]{figures/fig_example.png}
\caption{Example diagram adapted from \cite{exampleKey}.}
\label{fig:example}
\end{figure}
```

## Subfigures without extra packages

Use `minipage` + two `\includegraphics` side by side, or load `subcaption` only if the user accepts an extra package.

## `latexmkrc` and `TEXINPUTS`

From `TOPIC_DIR`, LaTeX must find `../template/IEEEtran.cls`. Example `latexmkrc`:

```perl
$ENV{'TEXINPUTS'} = '.:../template//:' . ($ENV{'TEXINPUTS'} // '');
```

Adjust `../template` depth if `TOPIC_DIR` is nested deeper.
