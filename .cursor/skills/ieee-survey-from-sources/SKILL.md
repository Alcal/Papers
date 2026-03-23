---
name: ieee-survey-from-sources
description: Builds a lean IEEE conference LaTeX survey from PDFs in a topic folder's sources directory, including thebibliography entries, abstract, section outline stubs, latexmk TEXINPUTS for shared IEEEtran.cls, and guidance for importing figures from sources. Use when the user adds a literature survey under Papers, points to a topic folder with sources PDFs, or asks to replicate the ieee-survey workflow.
---

# IEEE survey paper from PDF sources

## Parameters

- **TOPIC_DIR**: Path to the paper folder (e.g. `deep-learning/`, `nlp-topic/`). All paths relative to the workspace root unless absolute.
- **Template**: [`template/IEEEtran.cls`](template/IEEEtran.cls) at repo root; do not copy the class unless the user asks.
- **Sources**: `TOPIC_DIR/sources/` — PDFs (primary) and optional standalone images (PNG/SVG/PDF).

## Workflow checklist

1. **Scaffold** `TOPIC_DIR/main.tex`: `\documentclass[conference]{IEEEtran}`, `amsmath`, `graphicx`, `url`; placeholders for title, author(s), affiliation, email/ORCID, optional funding thanks; `\begin{abstract}` / `\begin{IEEEkeywords}`; section skeleton with short stubs only; `\section*{References}` + `\begin{thebibliography}`.
2. **Build support** `TOPIC_DIR/latexmkrc`: set `TEXINPUTS` so `IEEEtran.cls` resolves from `../template` when compiling from `TOPIC_DIR` (see [figures.md](figures.md) for the pattern).
3. **Bibliography**: One `\bibitem` per file in `sources/*.pdf` (and any image-only sources the user wants cited). Stable keys like `authorYearTopic`.
4. **Metadata pipeline** (in order): `pdfinfo FILE.pdf`; if Title/Author missing, `pdftotext -f 1 -l 2 FILE.pdf`; if still ambiguous, resolve venue/arXiv/DOI via web search.
5. **Abstract + outline**: Draft a survey-style abstract (no math, symbols, or footnotes in abstract per IEEE); thematic `\section`s with 2–4 bullet stubs; `\cite{keys}` in stubs matching `\bibitem` keys.
6. **Figures**: Follow [figures.md](figures.md). Never leave `\includegraphics{sources/...}` as the long-term solution for whole PDFs.
7. **Review pass** (after the user writes body text): fix LaTeX escapes, citation keys, IEEE tone, abstract/title rules; re-run `latexmk` or `pdflatex` until clean.

## IEEE constraints (reminders)

- Abstract: plain text; no equations, footnotes, or special symbols in title/abstract.
- References: numbered `[1]` style with `\cite`; `thebibliography` format consistent with the stock IEEE conference template in `template/IEEE-conference-template-062824.tex`.

## When the topic folder differs

Replace `deep-learning` with the user's `TOPIC_DIR` in all paths. Keep `figures/` under that same topic folder.

## TeX packages and editor diagnostics

- **`cite.sty` missing (Problems tab / LaTeX Workshop):** The stock IEEE template uses `\usepackage{cite}` for nicer citation sorting and range compression. That file ships with full TeX Live (often under `texlive-latex-recommended` or `texlive-latex-extra`). **Minimal or partial installs** may not include it. `\cite` with `\begin{thebibliography}` still works **without** the `cite` package; omit `\usepackage{cite}` unless the user installs the package and wants IEEE-style citation tweaks.
- **`IEEEtran.cls` not found:** Ensure `TEXINPUTS` includes `../template//` (see `latexmkrc` in [figures.md](figures.md)) or compile from a directory on TeX’s input path.
- **`ptmr7t` / `Font OT1/ptm/m/n/10 ... Metric (TFM) file not found`:** `IEEEtran` sets the main font to Adobe Times (`ptm`). The metric `ptmr7t.tfm` is part of **PSNFSS**, typically in **`texlive-fonts-recommended`** (Debian/Ubuntu). Scaffolded `main.tex` uses `\IfFileExists{ptmr7t.tfm}{}{...}` to load **`lmodern`** when Times is missing so the PDF still builds; body text will be Latin Modern instead of Times until PSNFSS is installed. Remove the fallback block (or install fonts) for camera-ready IEEE typography.