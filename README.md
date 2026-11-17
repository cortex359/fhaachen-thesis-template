# LaTeX-Vorlage für Abschlussarbeiten an der FH Aachen

LaTeX-Template für Bachelor- und Masterarbeiten an der FH Aachen, basierend auf KOMA-Script (`scrreprt`). Zweiseitiges Layout, Palatino-Schrift, automatische Literaturverwaltung mit biblatex/biber und Syntax-Highlighting mit minted.

## Schnellstart

1. Repository klonen oder als ZIP herunterladen
2. Template-Variablen in `main.tex` anpassen (Zeile 29–44)
3. Kompilieren:

```bash
latexmk -pdf -shell-escape main.tex
```

## Voraussetzungen

- TeX-Distribution (TeX Live oder MiKTeX)
- [Pygments](https://pygments.org/) für minted (`pip install Pygments`)
- [Inkscape](https://inkscape.org/) (optional, nur für SVG-Einbindung)
- biber (wird von biblatex benötigt, in TeX Live enthalten)

## Projektstruktur

```
.
├── main.tex                 # Hauptdokument mit Template-Variablen
├── doc/
│   ├── titlepage.tex        # Titelseite
│   ├── affidavit.tex        # Widmung & Eidesstattliche Erklärung
│   ├── abstract.tex         # Abstract
│   ├── 02_introduction.tex  # Einleitung
│   ├── 03_background.tex    # Grundlagen und verwandte Arbeiten
│   ├── 04_methods.tex       # Methoden
│   ├── 05_results.tex       # Ergebnisse
│   ├── 06_discussion.tex    # Diskussion
│   ├── 07_summary.tex       # Fazit und Ausblick
│   └── appendix.tex         # Anhang (mit minted-Beispiel)
├── pic/                     # Logos und Bilder
├── plots/                   # PGF-Plots (matplotlib-Export)
├── references.bib           # Literaturverzeichnis
├── biblatex-dm.cfg          # BibLaTeX-Datenmodell (paywalled-Feld)
├── unsrtdineng.bst          # Alternative: Legacy-BibTeX-Style (DIN 1505)
└── .vscode/settings.json    # VS Code LaTeX Workshop Konfiguration
```

## Template-Variablen

Alle Variablen werden einmalig in `main.tex` gesetzt:

| Variable | Beschreibung | Beispiel |
|---|---|---|
| `\titleDocument` | Titel der Arbeit | `My Thesis Title` |
| `\authorDocument` | Name des Autors | `Max Mustermann` |
| `\subjectDocument` | Art der Arbeit | `Bachelor's Thesis` |
| `\locationDocument` | Ort | `Aachen` |
| `\dateDocument` | Datum | `\today` |
| `\studentNumber` | Matrikelnummer | `1234567` |
| `\faculty` | Fakultät | `Faculty of Medical Engineering ...` |
| `\program` | Studiengang | `Applied Mathematics and ...` |
| `\firstSupervisor` | Erstprüfer | `Prof. Dr. Name` |
| `\secondSupervisor` | Zweitprüfer | `Name M.Sc.` |
| `\institution` | Institut (für Eidesstattliche Erklärung) | `Lehr- und Forschungsgebiet` |
| `\templateLanguage` | Sprache (`english` oder `ngerman`) | `english` |

## Sprachwechsel (Deutsch/Englisch)

Die Vorlage unterstützt Deutsch und Englisch über eine einzige Variable:

```latex
\newcommand{\templateLanguage}{ngerman}  % oder: english
```

Dies steuert automatisch:
- babel-Spracheinstellung
- PDF-Metadaten (`pdflang`)
- Beschriftungen (Inhaltsverzeichnis, Literaturverzeichnis)
- Titelseite (Erstprüfer/First Supervisor, Matrikelnummer/Student Number, etc.)

Die Dokumentklassen-Option muss ebenfalls angepasst werden:

```latex
\documentclass[
  ...
  ngerman,  % statt english
  ...
]{scrreprt}
```

## Literaturverwaltung

Das Template nutzt **biblatex** mit **biber** als Backend. Quellen werden in `references.bib` gepflegt.

### Paywalled-Feld

Über das benutzerdefinierte Feld `paywalled` in einer `.bib`-Datei werden DOI-Links automatisch über Sci-Hub aufgelöst:

```bibtex
@article{example2024,
  title     = {Example Article},
  author    = {Müller, Anna},
  journal   = {Journal of Examples},
  year      = {2024},
  doi       = {10.1234/example},
  paywalled = {true},
}
```

Die Basis-URL kann in `main.tex` angepasst werden:

```latex
\newcommand{\opendoibase}{https://sci-hub.se/}
```

### Feld-Bereinigung

Das Template entfernt automatisch redundante Felder aus den Literatureinträgen (abstract, editor, ISBN, etc.) und priorisiert DOI > eprint > URL.

## Abbildungen

### PGF-Plots (matplotlib)

Matplotlib-Figures können als PGF exportiert und nativ in LaTeX eingebunden werden. Der Vorteil: Die Schriften des Dokuments werden verwendet und die Grafiken sind Vektorgrafiken.

```python
import matplotlib
matplotlib.use('pgf')
import matplotlib.pyplot as plt

fig, ax = plt.subplots(figsize=(5, 3.5))
ax.plot([1, 2, 3], [1, 4, 2])
fig.savefig('plots/my_plot.pgf')
```

```latex
\begin{figure}[ht]
    \centering
    \resizebox{0.8\textwidth}{!}{\import{plots}{my_plot.pgf}}
    \caption{Beschreibung.}
    \label{fig:my_plot}
\end{figure}
```

### SVG-Grafiken

Benötigt Inkscape und `-shell-escape`:

```latex
\includesvg[width=0.6\textwidth]{./pic/example}  % ohne .svg-Endung
```

## Code-Listings (minted)

Syntax-Highlighting wird über das `minted`-Paket bereitgestellt. Benötigt Pygments und `-shell-escape`.

```latex
\begin{listing}[ht]
\begin{minted}{python}
def hello():
    print("Hello, World!")
\end{minted}
\caption{Beispiel-Listing.}
\label{lst:example}
\end{listing}
```

Die globale Konfiguration (Rahmen, Zeilennummern, Hintergrundfarbe) ist in `main.tex` unter `\setminted` definiert.

## Seitennummerierung

Das Template verwendet eine durchgehende Seitennummerierung: Der Hauptteil setzt die römische Nummerierung der Frontmatter als arabische Zahlen fort (z.B. Frontmatter endet bei VI, Hauptteil beginnt bei Seite 7).

Um den Hauptteil stattdessen bei Seite 1 zu starten, in `main.tex`:

```latex
% Diese Zeilen entfernen/auskommentieren:
\newcounter{lastromanpage}
\setcounter{lastromanpage}{\value{page}}
\addtocounter{lastromanpage}{1}
\setcounter{page}{\value{lastromanpage}}

% Und diese Zeile einkommentieren:
\setcounter{page}{1}
```

## VS Code

Die Datei `.vscode/settings.json` konfiguriert [LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop) mit den richtigen Build-Parametern (`-shell-escape` für minted). Einfach das Projekt in VS Code öffnen und `Ctrl+Alt+B` zum Kompilieren drücken.

## Credits

- Ursprünglich von Jennifer Eisele (FH Aachen)
- Struktur übernommen von Sven Hinz (FH Aachen)
- Template zusammengestellt und überarbeitet von Christian Rene Thelen (FH Aachen)
