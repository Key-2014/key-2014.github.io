---
date: '2026-04-07'
draft: false
title: 'Introduction and Usage of Typst Template'
description: 'Explains how to introduce a personal Typst template (Typst_Template) as a Git submodule to efficiently create reports and assignments with a unified layout.'
tags: ['Typst', 'Template', 'Git', 'Submodule', 'LaTeX Alternative']
categories: ['Tech']
ShowToc: true
TocOpen: true
---

## Introduction

Are you tired of copy-pasting the same formatting settings every time you create a report or assignment using Typst for your university classes?

If you separate repositories for each academic year or class, you'll end up manually updating all repositories whenever the template needs to be modified...

To solve this, I've built a system that **consolidates the template into a single repository and reuses it as a Git submodule**.

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

- Fonts: **Harano Aji Mincho** + **New Computer Modern Math**
- Paragraph Style: Fully justified, indentation supported
- Language Setting: Optimized for Japanese documents using `lang: "ja"`

### 📐 Math Support

- Physics notation via the `physica` package
- Unified unit formatting with the `unify` package (`qty`, `num`)
- Custom equation numbering: Switchable between standard `(1)` and section-linked `(1.2.3)`

### 📊 Figures and Drawing

- Vector drawing with the `cetz` package
- Decorative boxes using `showybox`
- Chemical formula notation with `whalogen`
- Figure/Table captions: Automatically localized to Japanese (e.g., "図", "表")

---

## Installation

### 1. Adding as a Submodule

Run the following in the root directory of the project where you want to use it:

```bash
# Add as a submodule in the lib directory
mkdir -p lib
git submodule add https://github.com/Key-2014/Typst_Template.git lib/my-templates
```

### 2. Cloning an Existing Repository

When cloning a repository that already contains the submodule, use the `--recursive` flag:

```bash
git clone --recursive https://github.com/<username>/<repository-name>.git
```

If you forgot to initialize the submodule after cloning:

```bash
git submodule init
git submodule update
```

### 3. Updating the Template to the Latest Version

```bash
git submodule update --remote lib/my-templates
git add lib/my-templates
git commit -m "Update Typst_Template to latest"
```

---

## Usage

### Basic Usage

Import the template in your main `.typ` file and apply the `project` function:

```typ
// Import the template library
#import "lib/my-templates/lib.typ": *

// Apply the template to the entire document
#show: project.with(
  title: "Report Title",
  author: "Your Name",
  student-id: "Student ID",
  date: "April 7, 2026",
)

= Introduction
Start writing your content here.
Japanese fonts and math environments are already configured.
```

### `project` Function Options

| Argument | Type | Default | Description |
| --- | --- | --- | --- |
| `title` | `content` | `""` | Document title |
| `author` | `content` | `""` | Author name |
| `student-id` | `content` | `""` | Student ID |
| `date` | `content` | `none` | Date |
| `textbook-numbering` | `boolean` | `false` | Set to `true` for textbook-style equation numbering |
| `indent` | `boolean` | `true` | Set to `false` to disable indentation |
| `heading-numbering` | `string` | `none` | Heading numbering format (e.g., `"1.1.1"`) |
| `heading-supplement` | `content` | `none` | Supplemental text for headings |

---

## Utility Macros

`utils.typ` contains convenient macros to streamline report writing.

### Math & Physics

| Macro | Description | Output Example |
| --- | --- | --- |
| `#combination(n, r)` | Combination symbol | ₙCᵣ |
| `#permutation(n, r)` | Permutation symbol | ₙPᵣ |
| `#qed` | QED symbol (right-aligned) | ∎ |
| `#lhs` / `#rhs` | "(LHS)" / "(RHS)" in Japanese | (左辺) / (右辺) |

### Boxes

| Macro | Description | Use Case |
| --- | --- | --- |
| `#crect(body)` | Centered frame | Highlighting definitions or theorems |
| `#frect(body)` | Full-width frame | Highlighting important equations |
| `#ans(body)` | Answer box | Enclosing the final answer |

### Units

```typ
// Display units in upright font
$10 #u("kg")$         // → 10 kg
$5 #u("m/s^2")$       // → 5 m/s²
$3 #u("m", b: true)$  // → 3 [m] (with brackets)
```

### Equation Tags

```typ
// Add a tag without affecting the global equation counter
#eqtag($E = m c^2$, "Einstein")
// → The equation number will be displayed as (Einstein)
```

---

## Equation Numbering Styles

### Standard Style (Default)

```typ
#show: project.with(title: "Standard Style")
// Equation numbers: (1), (2), (3), ...
```

### Section-Linked Style

By setting heading numbering, equation numbers will link to sections:

```typ
#show: project.with(
  title: "Textbook Style",
  heading-numbering: "1.1",
)
// Equation numbers: (1.1), (1.2), (2.1), ...
```

---

## Project Structure Example

Here is an example directory structure when using it as a submodule:

```text
my-report/
├── lib/
│   └── my-templates/        # ← Git Submodule (Typst_Template)
│       ├── lib.typ           # Main template
│       └── utils.typ         # Utility macros
├── main.typ                  # Main report file
├── figures/                  # Figures and images
├── .gitmodules               # Submodule configuration
└── README.md
```

---

## Included Packages

List of Typst Preview packages bundled with the template:

| Package | Version | Purpose |
| --- | --- | --- |
| [physica](https://typst.app/universe/package/physica) | 0.9.7 | Physics notation (partial derivatives, bra-kets, etc.) |
| [unify](https://typst.app/universe/package/unify) | 0.7.1 | Unified SI unit formatting |
| [cetz](https://typst.app/universe/package/cetz) | 0.4.2 | Vector drawing |
| [showybox](https://typst.app/universe/package/showybox) | 2.0.4 | Decorative boxes |
| [whalogen](https://typst.app/universe/package/whalogen) | 0.3.0 | Chemical formula notation |

---

## Summary

- **Typst_Template** is a template packed with all necessary settings for writing Japanese reports.
- By introducing it as a **Git submodule**, you can centrally manage it across multiple projects.
- Template updates can be reflected instantly with `git submodule update --remote`.
- You can flexibly customize the layout and equation numbering styles using the arguments of the `project` function.

For source code and the latest information, please visit the [GitHub Repository](https://github.com/Key-2014/Typst_Template) 📄
