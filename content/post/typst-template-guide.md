---
date: '2026-04-07'
draft: false
title: 'Introduction and Usage of Typst Template'
description: 'Explains how to introduce a personal Typst template (Typst_Template) as a Git submodule, configure VS Code snippets, set up CI/CD, and efficiently create reports.'
tags: ['Typst', 'Template', 'Git', 'Submodule', 'LaTeX Alternative', 'CI/CD']
categories: ['Tech']
ShowToc: true
TocOpen: true
---

## Introduction

Are you tired of copy-pasting the same formatting settings every time you create a report or assignment using Typst for your university classes?

If you have separate repositories for each academic year or class, you'll end up manually updating all repositories whenever the template needs to be modified...

To solve this, I've built a system that **consolidates the template into a single repository and reuses it as a Git submodule**. Recently, I've completely overhauled this template to make setup automatic and add powerful developer features such as VS Code snippets and GitHub Actions CI/CD workflows.

👉 Repository: [**Typst_Template**](https://github.com/Key-2014/Typst_Template)

---

## Why a Submodule?

| Method | Pros | Cons |
| --- | --- | --- |
| Copy & Paste | Easy to start | Manual updates required for all repos on modification |
| Typst Package | Official mechanism | Requires registering a public package |
| **Git Submodule** | **Centralized management, instant updates** | Requires some Git knowledge |

With a submodule, you only need to run `git submodule update --remote` in each project to sync with the latest version after updating the template.

---

## Template Features

### 🇯🇵 Japanese Optimization

- Fonts: **Harano Aji Mincho** (Japanese) + **New Computer Modern** (English) and **New Computer Modern Math** (Math).
- Paragraph Style: Fully justified, line spacing optimized for Japanese (`leading: 0.8em`). Indentation options available.
- Language Setting: Optimized for Japanese documents using `lang: "ja"`.

### 📐 Math Support

- Physics notation via the `physica` package.
- Unified unit formatting with the `unify` package (`qty`, `num`).
- Customizable equation numbering: Support for standard `(1)` and section-linked `(1.1)`.
- Block equations are configured to allow page breaks (`breakable: true`), preventing awkward spacing in large mathematical derivations.

### 📊 Figures and Drawing

- Vector drawing with the `cetz` package.
- Decorative boxes using `showybox`.
- Chemical formula notation with `whalogen`.
- Figure/Table captions: Automatically localized to Japanese (e.g., "図", "表") and table captions placed at the top by default.

### 📝 Advanced Layout Features

- **Standalone Title Page**: Vertically-centered covers (`title-page: true`) that reset the page counter.
- **Table of Contents**: Automatic table of contents generation (`toc: true`).

### ⚡ VS Code Snippets

- Optimized auto-completion triggers (starting with `typ-`) for initializing documents, creating figures, equations, and inserting custom macros.

### 🚀 Automated CI/CD (GitHub Actions)

- Automatically compiles your Typst source files to PDF and attaches them to GitHub Releases on push. It even downloads the Japanese fonts automatically on the server!

---

## Installation & Setup

You can set up the entire environment—including submodules, VS Code snippets, and CI/CD workflows—with just a couple of commands.

### 1. Add the Submodule

Run the following in the root directory of the project where you want to use the template:

```powershell
# 1. Add the submodule to your lib directory
mkdir -p lib
git submodule add https://github.com/Key-2014/Typst_Template.git lib/Typst_Template
```

### 2. Run the Setup Script

Run the automated setup script included in the template to instantly configure your workspace:

```powershell
# 2. Run the automatic setup script
powershell -ExecutionPolicy Bypass -File .\lib\Typst_Template\init.ps1
```

> **What does `init.ps1` do?**
> 1. **CI/CD Workflow**: Copies `compile-typst.yml` into your parent repository's `.github/workflows/` folder.
> 2. **VS Code Snippets**: Copies `.vscode/typst.code-snippets` into your project, activating high-productivity shortcuts.
> 3. **Git Ignore**: Appends `*.pdf` to your `.gitignore` to prevent generated PDFs from bloating your repository.

---

### Manual Fallbacks

#### Cloning an Existing Repository
When cloning a repository that already contains the submodule, use the `--recursive` flag:

```bash
git clone --recursive https://github.com/<username>/<repository-name>.git
```

If you forgot to initialize the submodule after cloning, run:

```bash
git submodule init
git submodule update
```

#### Updating the Template to the Latest Version
To fetch new updates from the template:

```bash
git submodule update --remote lib/Typst_Template
git add lib/Typst_Template
git commit -m "Update Typst_Template to latest"
```

---

## Usage

### Basic Usage

Import the template in your main `.typ` file and apply the `project` function:

```typ
// Import the template library (this also loads the macros from utils.typ)
#import "lib/Typst_Template/lib.typ": *

// Apply the template to the entire document
#show: project.with(
  title: "Report Title",
  author: "Your Name",
  student-id: "Student ID",
  date: datetime.today().display(),
  heading-numbering: "1.1",  // Optional: Enable section numbering
  equation-numbering: "1.1", // Optional: Enable section-based equation numbering ("1" or "1.1")
  title-page: true,          // Optional: Create a standalone cover page
  toc: true,                 // Optional: Generate a Table of Contents
)

= Introduction
Start writing your content here. Japanese fonts and paragraph settings are applied automatically.

$ x = (-b +- sqrt(b^2 - 4a c)) / (2a) $

// Add references at the end
#pagebreak()
#bibliography("refs.bib")
```

### `project` Function Options

| Argument | Type | Default | Description |
| --- | --- | --- | --- |
| `title` | `string` / `content` | `""` | Document title. |
| `author` | `string` / `content` | `""` | Author name. |
| `student-id` | `string` / `content` | `""` | Student ID. |
| `date` | `datetime` / `content` | `none` | Date (e.g., `datetime.today().display()`). |
| `indent` | `boolean` | `false` | Set to `true` to indent the first line of each paragraph by 1em. |
| `heading-numbering` | `string` / `none` | `none` | Heading numbering style (e.g., `"1.1"`). |
| `heading-supplement` | `content` / `none` | `none` | Heading prefix supplement (e.g., `[Chapter]`). |
| `equation-numbering` | `string` / `none` | `none` | Equation numbering style (`"1"` for continuous, `"1.1"` for section-based, or `none`). |
| `title-page` | `boolean` | `false` | Set to `true` to generate a dedicated cover page that resets the page counter. |
| `toc` | `boolean` | `false` | Set to `true` to generate a Table of Contents (目次). |

---

## Equation Numbering Styles

### Continuous Style
If you want continuous numbering throughout the document (e.g., `(1)`, `(2)`, `(3)`):

```typ
#show: project.with(
  title: "Continuous Style",
  equation-numbering: "1",
)
```

### Section-Based Style
If you want the equation numbers to reset and link to major sections (e.g., `(1.1)`, `(1.2)`, `(2.1)`):

```typ
#show: project.with(
  title: "Section Style",
  heading-numbering: "1.1",
  equation-numbering: "1.1",
)
```
*Note: If `heading-numbering` is not configured (`none`), section-based equation numbering will automatically fall back to continuous `"1"`` style.*

---

## Utility Macros

`utils.typ` contains convenient macros to streamline report writing.

### Math & Physics

| Macro | Description | Output Example |
| --- | --- | --- |
| `#combination(n, r)` | Combination symbol | $_n\text{C}_r$ |
| `#permutation(n, r)` | Permutation symbol | $_n\text{P}_r$ |
| `#hcombination(n, r)` | Combination with repetition | $_n\text{H}_r$ |
| `#qed` | QED symbol (right-aligned) | ∎ |
| `#lhs` / `#rhs` | "(LHS)" / "(RHS)" in Japanese | (左辺) / (右辺) |
| `#bk` | Inserts a horizontal space | 1em space |

### Boxes

| Macro | Description | Use Case |
| --- | --- | --- |
| `#crect(body)` | Centered frame block | Emphasizing theorems or definitions |
| `#frect(body)` | Full-width frame block | Highlighting long equations or theorems |
| `#ans(body)` | Answer box | Enclosing the final answer (auto-adjusts baseline for fractions!) |

### Units

Using the `unify` package, units are kept in an upright font:

```typ
$10 #u("kg")$         // → 10 kg
$5 #u("m/s^2")$       // → 5 m/s²
$3 #u("m", b: true)$  // → 3 [m] (with brackets)
```

### Equation Tags

To tag a single equation with a specific label (like `*` or `A`) without incrementing the equation counter:

```typ
#eqtag($E = m c^2$, "Einstein")
// → Displays as: E = mc²  (Einstein)
```

---

## VS Code Snippets

By running the `init.ps1` script, an optimized `.vscode/typst.code-snippets` file is added to your workspace. Simply type **`typ-`** inside VS Code to trigger auto-completion shortcuts:

### Template & Layout
- **`typ-report`**: Inserts the boilerplate code for the `project` function.
- **`typ-png`**: Inserts a figure wrapped image block with caption and label.
- **`typ-table`**: Inserts a table wrapped inside a figure.
- **`typ-code`**: Inserts a formatted source code block.
- **`typ-cetz`**: Inserts a canvas environment with a helper coordinate grid.

### Custom Macros
- **`typ-ans`**: Inserts an answer box (`#ans[...]`).
- **`typ-frect`** / **`typ-crect`**: Inserts framed box macros.
- **`typ-u`**: Inserts physical units (`#u("...")`).

### Equation Controls
- **`typ-eqnum`**: Inserts continuous numbering settings.
- **`typ-eqtag`**: Inserts a manually tagged equation block.
- **`typ-eq-manual`**: Overrides the current equation counter prefix and resets the count.
- **`typ-eq-none`**: Disables numbering from that point onward.

---

## Package Auto-Updater

Typst pins package versions (e.g., `@preview/physica:0.9.8`) to guarantee document reproducibility.
To keep the packages in your template up-to-date with their latest registry releases:

1. **Tinymist VS Code Extension**: Delete the version suffix (e.g., `#import "@preview/physica:"`), press `Ctrl + Space`, and select the newest version.
2. **Auto-Updater Script**: Run the included PowerShell script in the template directory:
   ```powershell
   powershell -ExecutionPolicy Bypass -File .\update_packages.ps1
   ```

---

## Automated PDF releases (GitHub Actions CI/CD)

The template includes a CI/CD workflow (`compile-typst.yml`) that is automatically copied to your repository when running `init.ps1`. 

Every time you run `git push`, the runner will:
1. Spin up a container and automatically download **"Harano Aji Mincho"** fonts (keeping your repository light and clean).
2. Find all `.typ` files in your project root.
3. Compile them to high-quality PDFs.
4. Auto-publish a new release on GitHub containing the compiled PDFs!

---

## Project Structure Example

Here is a typical directory layout for an academic report project using this template:

```text
my-report/
├── .github/
│   └── workflows/
│       └── compile-typst.yml   # ← Auto-copied by init.ps1 (CI/CD)
├── .vscode/
│   └── typst.code-snippets     # ← Auto-copied by init.ps1 (Snippets)
├── lib/
│   └── Typst_Template/         # ← Git Submodule (Typst_Template)
│       ├── lib.typ             # Main template configurations
│       ├── utils.typ           # Utility macros
│       └── ...
├── main.typ                    # Your main report file
├── refs.bib                    # Bibliography citations
├── figures/                    # Images and illustrations
├── .gitmodules                 # Submodule mapping
└── .gitignore                  # PDF exclusion rules (Updated by init.ps1)
```

---

## Included Packages

The template comes pre-bundled with the following optimized Typst packages:

| Package | Version | Purpose |
| --- | --- | --- |
| [physica](https://typst.app/universe/package/physica) | 0.9.8 | Physics notation (derivatives, brackets, etc.) |
| [unify](https://typst.app/universe/package/unify) | 0.8.0 | SI units and quantities |
| [cetz](https://typst.app/universe/package/cetz) | 0.5.2 | Vector drawing and plotting |
| [showybox](https://typst.app/universe/package/showybox) | 2.0.4 | Colorful emphasis boxes |
| [whalogen](https://typst.app/universe/package/whalogen) | 0.3.0 | Chemical formula renderer |

---

## Summary

- **Typst_Template** offers a highly-polished academic setup optimized for Japanese report writing.
- Using it as a **Git submodule** centralizes your styling across multiple homework assignments and repositories.
- The **`init.ps1` automatic setup** configures your local environment, sets up VS Code macros, and connects cloud CI/CD PDF publishing.
- Keep your template synced across all your classes using `git submodule update --remote`.

For source code, issues, and contributions, visit the [**GitHub Repository**](https://github.com/Key-2014/Typst_Template) 📄
