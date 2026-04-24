---
date: '2026-04-07'
draft: false
title: 'Page Editing and Customization Guide'
description: 'A complete guide to editing the site yourself, including how to create new articles, write Markdown, add images, customize menus, and override theme styles.'
tags: ['Hugo', 'PaperMod', 'Markdown', 'Customization', 'Guide']
categories: ['Tech']
ShowToc: true
TocOpen: true
---

## Introduction

This page explains how to add new articles and edit existing pages on **Key-2014.github.io**.

It is recommended to read the "[Site Architecture and Deployment Guide](/post/site-architecture-guide/)" beforehand as prerequisite knowledge.

---

## Creating a New Article

### Method 1: Using the Hugo Command (Recommended)

```bash
hugo new content/post/my-new-article.md
```

This automatically generates a file with Front Matter based on the `archetypes/default.md` template:

```markdown
---
date: '2026-04-07T14:00:00+09:00'
draft: true
title: 'My New Article'
---
```

### Method 2: Creating the File Manually

You can also directly create a `.md` file in the `content/post/` directory. If you do, you'll need to manually write the Front Matter at the top.

---

## Front Matter (Article Metadata)

Every article file must start with a **Front Matter** block. This is a YAML-formatted data block enclosed by `---`.

### Basic Front Matter Example

```yaml
---
date: '2026-04-07'
draft: false
title: 'Article Title'
description: 'Description shown in search results and SNS shares'
tags: ['Hugo', 'Guide']
categories: ['Tech']
ShowToc: true
TocOpen: true
---
```

### Field Explanations

| Field | Required | Type | Description |
| --- | --- | --- | --- |
| `date` | ✅ | String | Article date (in `YYYY-MM-DD` format) |
| `draft` | ✅ | Boolean | If `true`, it won't be displayed in production |
| `title` | ✅ | String | The title of the article |
| `description` | - | String | Article summary used for SEO and cards |
| `tags` | - | List | Tags (keywords for the article) |
| `categories` | - | List | Categories |
| `ShowToc` | - | Boolean | Whether to show the Table of Contents |
| `TocOpen` | - | Boolean | Whether to open the ToC by default |
| `weight` | - | Number | Ordering weight (smaller numbers appear first) |
| `cover.image` | - | String | Path to the cover image |

### About Drafts

Articles marked with `draft: true` are:
- ✅ Viewable locally using `hugo server -D`
- ❌ Excluded from the production build (GitHub Actions)

When your article is ready, change it to `draft: false` before pushing.

---

## Writing Markdown

Hugo supports standard Markdown along with several extensions.

### Headings

```markdown
## Heading 2 (h2)
### Heading 3 (h3)
#### Heading 4 (h4)
```

> ⚠️ Do not use `# h1` headings within the article content. The `title` is automatically displayed as the h1.

### Text Formatting

```markdown
**Bold**
*Italic*
~~Strikethrough~~
`Inline code`
```

### Lists

```markdown
- Bullet point 1
- Bullet point 2
  - Nested point

1. Numbered list
2. Second item
```

### Links

```markdown
[Display Text](https://example.com)
[Internal Link](/post/site-architecture-guide/)
```

### Images

```markdown
![Alt text](/images/screenshot.png)
```

By placing an image file in the `static/images/` directory, you can reference it via the `/images/` path.

### Code Blocks

````markdown
```python
def hello():
    print("Hello, World!")
```
````

Specifying a language name applies **syntax highlighting**. The PaperMod theme also adds a copy button automatically.

### Tables

```markdown
| Column 1 | Column 2 | Column 3 |
| --- | --- | --- |
| Data 1 | Data 2 | Data 3 |
```

### Blockquotes

```markdown
> This is a blockquote.
> It can span multiple lines.
```

### Horizontal Rules

```markdown
---
```

### Emojis

Because `enableEmoji: true` is set in Hugo, you can use emoji shortcodes:

```markdown
:rocket: → 🚀
:sparkles: → ✨
:book: → 📖
```

---

## Adding Images

### 1. Placing in the `static` Directory

```text
static/
└── images/
    └── my-screenshot.png
```

Reference in Markdown:

```markdown
![Screenshot](/images/my-screenshot.png)
```

### 2. Placing Next to the Article (Page Bundles)

Page bundles allow you to manage the article and its images together:

```text
content/post/my-article/
├── index.md       # Article content
└── screenshot.png  # Image file
```

Reference via relative path in Markdown:

```markdown
![Screenshot](screenshot.png)
```

### Setting a Cover Image

By specifying `cover` in the Front Matter, you can display a cover image (eye-catch) for the article:

```yaml
---
title: 'Article Title'
cover:
  image: '/images/cover.png'
  alt: 'Cover image description'
  caption: 'Caption text'
  relative: false
---
```

---

## Editing the Menu

The navigation menu (in the header) is managed by `menu.main` in `hugo.yaml`.

### Adding a Menu Item

```yaml
menu:
  main:
    - name: "About Me"
      url: /aboutme
      weight: 1
    - name: "Posts"
      url: /post/
      weight: 3
    - name: "Archives"       # ← Example addition
      url: /archives/
      weight: 5
```

- `name`: Display name in the menu
- `url`: Path to link to
- `weight`: Display order (smaller is further left)

### Example: Creating an Archives Page

Create `content/archives.md`:

```markdown
---
title: "Archives"
layout: "archives"
url: "/archives/"
summary: "archives"
---
```

---

## Customizing Site Settings

Here are some of the main settings you can change in `hugo.yaml`.

### Changing the Site Title

```yaml
title: Key's Sandbox    # ← Change to your desired title
```

### Changing the Description

```yaml
params:
  description: "Writing about tech articles and daily events."
```

### Editing Social Links

```yaml
params:
  socialIcons:
    - name: github
      url: "https://github.com/Key-2014"
    - name: email
      url: "keionuma1214@gmail.com"
    - name: twitter           # ← Example addition
      url: "https://twitter.com/your_handle"
```

Available icon names can be found in the [PaperMod Documentation](https://adityatelange.github.io/hugo-PaperMod/posts/papermod/papermod-icons/).

### Switching Theme Colors

```yaml
params:
  defaultTheme: "auto"    # "auto" / "light" / "dark"
```

| Value | Behavior |
| --- | --- |
| `auto` | Follows the OS dark/light setting |
| `light` | Always light mode |
| `dark` | Always dark mode |

### Home Page Settings

```yaml
params:
  homeInfoParams:
    Title: "*Hello!"
    Content: |
      Welcome to my blog!
```

---

## Customizing the Theme

If you want to change the layout or styles of the PaperMod theme, **do not edit the theme files directly**. Instead, place files in the `layouts/` directory at the project root to override them.

### Overriding Layouts

Hugo prioritizes the `layouts/` folder in your project root over the theme's templates. For example, to customize the article footer:

1. Find the target file in the theme (e.g., `themes/PaperMod/layouts/partials/footer.html`)
2. Copy it to the same path in your project (`layouts/partials/footer.html`)
3. Edit the copied file

```bash
# Example: Overriding the footer layout
mkdir -p layouts/partials
cp themes/PaperMod/layouts/partials/footer.html layouts/partials/footer.html
```

### Adding Custom CSS

By placing CSS files in the `assets/css/extended/` directory, they will be loaded in addition to the theme's CSS:

```text
assets/
└── css/
    └── extended/
        └── custom.css
```

Example: Changing the link color:

```css
/* assets/css/extended/custom.css */
:root {
  --link-color: #3498db;
}

a {
  color: var(--link-color);
}
```

---

## Steps to Publish an Article

Here is a summary of the workflow from writing an article to publishing it on the site.

### 1. Create the Article

```bash
hugo new content/post/my-new-article.md
```

### 2. Edit the Article

Edit the Markdown using your preferred editor (like VS Code).

### 3. Preview Locally

```bash
hugo server -D
```

Check the result by accessing `http://localhost:1313/` in your browser.

### 4. Remove the Draft Flag

```yaml
draft: false    # Change from true to false
```

### 5. Commit & Push

```bash
git add content/post/my-new-article.md
git commit -m "Add: New article"
git push origin main
```

### 6. Verify Automated Deployment

Check that the workflow has succeeded in the **Actions** tab on GitHub. It will be reflected on your site within a few minutes.

---

## Common Operations

### Deleting an Article

Simply delete the target `.md` file and push the changes:

```bash
rm content/post/old-article.md
git add -A
git commit -m "Remove: Old article"
git push origin main
```

### Unpublishing an Article

Instead of deleting the file, you can change `draft: true` in the Front Matter:

```yaml
draft: true   # It will no longer appear on the site
```

### Updating the Theme

```bash
git submodule update --remote themes/PaperMod
git add themes/PaperMod
git commit -m "Update: PaperMod theme"
git push origin main
```

---

## Troubleshooting

### `hugo server` Won't Start Locally

```bash
# Check if Hugo is installed
hugo version

# Check if submodules are initialized
git submodule status
# If a line starts with "-", the submodule is uninitialized
git submodule init
git submodule update
```

### Articles Are Not Showing Up

Please check the following:

1. **Is `draft: false` set?** — Articles with `draft: true` won't show in production.
2. **Is the date set to the future?** — By default, Hugo won't display articles with a future date.
3. **Is the file in `content/post/`?** — It might not appear in the post list if placed elsewhere.

### Images Are Not Showing Up

- Make sure they are placed in the `static/` directory.
- Check if the path is `/images/file.png` (starting with `/`).
- Ensure the filename capitalization matches exactly (Linux is case-sensitive).

### GitHub Actions Build Fails

1. Check the error logs in the GitHub **Actions** tab.
2. Common causes:
   - YAML syntax errors in Front Matter (e.g., misaligned indentation).
   - Broken links or references.
   - Failure to retrieve submodules.

---

## Summary

| Goal | How to |
| --- | --- |
| Write an article | Create a `.md` file in `content/post/` |
| Preview | Check locally with `hugo server -D` |
| Publish | Set `draft: false` and push |
| Edit menu | Edit `menu.main` in `hugo.yaml` |
| Change styles | Place custom CSS in `assets/css/extended/` |
| Change layouts | Copy theme files to `layouts/` and edit |
| Update theme | Run `git submodule update --remote` |

Once you get used to it, you can update your site just by **writing Markdown and pushing**. Try it out! 🎉
