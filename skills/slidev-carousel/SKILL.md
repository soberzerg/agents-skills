---
name: slidev-carousel
description: Create vertical Instagram/social media carousel presentations using Slidev. Generates slide decks with proper structure, global styles, code highlighting, and image generation for cover slides.
---

# Slidev Carousel Generator

Create vertical (4:5) carousel presentations for Instagram/social media using Slidev with Tailwind CSS styling.

## When to Use This Skill

- User asks to create a carousel, slide deck, or social media post
- User wants to create educational content slides (tips, principles, guides)
- User asks to generate Instagram/LinkedIn carousel

## Project Setup

### Prerequisites

Slidev project at `slidev/` in the working directory:

```
slidev/
├── package.json        # @slidev/cli dependency
├── slides.md           # Main presentation file
├── style.css           # Global styles (CRITICAL)
└── public/images/      # Generated images
```

### package.json (minimal)

```json
{
  "name": "slidev",
  "type": "module",
  "private": true,
  "scripts": {
    "build": "slidev build",
    "dev": "slidev --open",
    "export": "slidev export"
  },
  "dependencies": {
    "@slidev/cli": "^52.14.1",
    "vue": "^3.5.29"
  },
  "devDependencies": {
    "playwright-chromium": "^1.58.2"
  }
}
```

If the project does not exist, scaffold it:

```bash
mkdir -p slidev/public/images
cd slidev
# create package.json, then:
pnpm install
```

## Critical Architecture Rules

### 1. Vertical Format — Headmatter

```yaml
---
theme: none
title: Carousel Title
drawings:
  persist: false
transition: none
aspectRatio: 4/5
canvasWidth: 1080
export:
  withClicks: false
---
```

- `aspectRatio: 4/5` — portrait format for Instagram (1080x1350)
- `canvasWidth: 1080` — real pixel width
- `theme: none` — no built-in theme, full Tailwind control
- `transition: none` — no animations for export

### 2. Global Styles in `style.css` (MANDATORY)

**NEVER use per-slide `<style>` blocks for shared styles.** Slidev `<style>` tags are SCOPED to the slide where they appear. If you put `.slide-card` in slide 1's `<style>`, slides 2-7 will NOT have it.

Create `slidev/style.css` (auto-loaded by Slidev):

```css
.slide-card {
  position: absolute;
  inset: 0;
  width: 1080px;
  height: 1350px;
}

.slidev-code-wrapper {
  margin: 0 !important;
}

.slidev-code-wrapper pre.slidev-code {
  border-radius: 0.5rem;
  font-size: 1.25rem;
  line-height: 1.75;
}
```

### 3. Every Slide Needs a `.slide-card` Wrapper

Without `<div class="slide-card ...">`, the content will NOT fill 100% height. The `class:` frontmatter does NOT work reliably for full-bleed vertical slides with `layout: none`.

```markdown
---

<!-- Slide N -->

<div class="slide-card bg-[#1a1a2e] text-white flex flex-col p-16">
  <!-- content here -->
</div>
```

### 4. Slide Separator Syntax

Use bare `---` without per-slide frontmatter (since we use `.slide-card` for everything):

```markdown
</div>

---

<!-- Slide 2: Title -->

<div class="slide-card bg-[#1a1a2e] text-white flex flex-col p-16">
```

**Do NOT use:**
- `layout: none` / `layout: center` in per-slide frontmatter — it breaks vertical layout
- `class:` in per-slide frontmatter — does not fill 100% height

### 5. Code Blocks — Use Markdown, NOT HTML

Slidev renders fenced code blocks with Shiki syntax highlighting. Use them inside `.slide-card` divs:

```markdown
<div class="bg-[#0f3460] rounded-2xl p-8 mb-6">
  <div class="text-[#e94560] font-mono text-lg mb-3">BAD</div>

` ` `go
result, _ := doSomething()
` ` `

</div>
```

(Remove spaces between backticks — shown here for escaping.)

**NEVER use:**
- `<code>` tags
- `<div class="font-mono">` with `&nbsp;` for indentation
- Manual HTML for code examples

### 6. Cover Image Generation

Use `nano-banana` skill to generate a cover image, then copy to `public/images/`:

```bash
# After generating with nano-banana:
cp generated_imgs/generated-*.png slidev/public/images/cover.png
```

Reference in slides:
```html
<img src="/images/cover.png" class="w-[420px] h-[420px] object-contain rounded-2xl opacity-90" />
```

## Slide Templates

### Cover Slide (first)

```html
<div class="slide-card bg-gradient-to-br from-[#1a1a2e] to-[#16213e] text-white flex flex-col justify-between p-16">
  <div>
    <div class="text-[#e94560] font-bold text-2xl tracking-widest uppercase mb-6">Category</div>
  </div>

  <div class="flex items-center gap-10">
    <div class="flex-1">
      <h1 class="text-7xl font-black leading-tight mb-8">
        Main<br/>Title
      </h1>
      <p class="text-2xl text-gray-300 leading-relaxed">
        Subtitle text
      </p>
    </div>
    <img src="/images/cover.png" class="w-[420px] h-[420px] object-contain rounded-2xl opacity-90" />
  </div>

  <div class="flex items-center gap-4">
    <div class="w-14 h-14 rounded-full bg-[#e94560] flex items-center justify-center text-2xl font-bold">S</div>
    <div>
      <div class="text-xl font-semibold">@handle</div>
      <div class="text-base text-gray-400">Swipe for more</div>
    </div>
  </div>
</div>
```

### Content Slide (with code)

```html
<div class="slide-card bg-[#1a1a2e] text-white flex flex-col p-16">
  <div class="flex items-center justify-between mb-12">
    <div class="text-[#e94560] font-bold text-xl tracking-widest uppercase">Section N / Total</div>
    <div class="w-12 h-12 rounded-full border-2 border-[#e94560] flex items-center justify-center text-2xl font-bold text-[#e94560]">N</div>
  </div>

  <h2 class="text-6xl font-black leading-tight mb-10">
    Slide<br/>Title
  </h2>

  <p class="text-2xl text-gray-300 leading-relaxed mb-12">
    Description text.
  </p>

  <div class="bg-[#0f3460] rounded-2xl p-8 mb-6">
    <div class="text-[#e94560] font-mono text-lg mb-3">BAD</div>

` ` `go
// bad code
` ` `

  </div>

  <div class="bg-[#0f3460] rounded-2xl p-8">
    <div class="text-[#4ecca3] font-mono text-lg mb-3">GOOD</div>

` ` `go
// good code
` ` `

  </div>

  <!-- Progress bar -->
  <div class="mt-auto pt-10 flex justify-between items-center">
    <div class="h-1.5 flex-1 bg-[#0f3460] rounded-full overflow-hidden">
      <div class="h-full w-[20%] bg-[#e94560] rounded-full"></div>
    </div>
    <span class="text-gray-500 ml-4 text-lg">1/5</span>
  </div>
</div>
```

### Content Slide (with cards/icons, no code)

```html
<div class="slide-card bg-[#1a1a2e] text-white flex flex-col p-16">
  <!-- header + title + description same as above -->

  <div class="grid grid-cols-2 gap-6">
    <div class="bg-[#0f3460] rounded-2xl p-8 flex flex-col items-center">
      <div class="text-8xl mb-4">EMOJI</div>
      <div class="text-[#e94560] font-bold text-xl mb-2">Bad Label</div>
      <div class="text-gray-400 text-lg text-center">Description</div>
    </div>
    <div class="bg-[#0f3460] rounded-2xl p-8 flex flex-col items-center">
      <div class="text-8xl mb-4">EMOJI</div>
      <div class="text-[#4ecca3] font-bold text-xl mb-2">Good Label</div>
      <div class="text-gray-400 text-lg text-center">Description</div>
    </div>
  </div>

  <!-- progress bar -->
</div>
```

### Content Slide (bullet list)

```html
<div class="slide-card bg-[#1a1a2e] text-white flex flex-col p-16">
  <!-- header + title + description same as above -->

  <div class="flex flex-col gap-6">
    <div class="flex items-center gap-6 bg-[#0f3460] rounded-2xl p-6">
      <div class="text-5xl">EMOJI</div>
      <div class="text-xl text-gray-300">Item text</div>
    </div>
    <!-- repeat -->
  </div>

  <!-- progress bar -->
</div>
```

### CTA Slide (last)

```html
<div class="slide-card bg-gradient-to-br from-[#1a1a2e] to-[#16213e] text-white flex flex-col justify-center items-center text-center p-16">
  <div class="text-8xl mb-10">EMOJI</div>

  <h2 class="text-6xl font-black leading-tight mb-8">
    Call to<br/>Action
  </h2>

  <p class="text-2xl text-gray-300 leading-relaxed mb-16">
    Closing message
  </p>

  <div class="flex gap-8 mb-16">
    <!-- social action buttons -->
  </div>

  <div class="flex items-center gap-4">
    <div class="w-14 h-14 rounded-full bg-[#e94560] flex items-center justify-center text-2xl font-bold">S</div>
    <div class="text-xl font-semibold">@handle</div>
  </div>
</div>
```

## Color Palette (default dark theme)

| Token | Color | Usage |
|-------|-------|-------|
| Background | `#1a1a2e` | Slide background |
| Background (gradient end) | `#16213e` | Cover/CTA slides |
| Accent | `#e94560` | Headers, badges, progress bar, "bad" labels |
| Success | `#4ecca3` | "Good" labels, positive highlights |
| Code background | `#0f3460` | Code block containers |
| Text primary | `text-white` | Headings |
| Text secondary | `text-gray-300` | Body text |
| Text muted | `text-gray-400` / `text-gray-500` | Captions, progress |

## Workflow

1. **Ask** user for topic, number of slides, style preference
2. **Generate** cover image with `nano-banana` skill, copy to `public/images/`
3. **Create** `style.css` with global `.slide-card` styles
4. **Write** `slides.md` with headmatter + all slides
5. **Build** to verify: `cd slidev && pnpm build`
6. **Export** to PNG: `cd slidev && pnpm export` (requires `playwright-chromium`)

## Common Mistakes to Avoid

| Mistake | Why it breaks | Fix |
|---------|--------------|-----|
| `<style>` in slide 1 for shared CSS | Slidev scopes `<style>` per slide | Use `style.css` file |
| `layout: none` + `class:` frontmatter | Container doesn't fill 100% height | Use `.slide-card` wrapper div |
| `layout: center` on vertical slides | Breaks flex column layout | Use `.slide-card` with `justify-center items-center` |
| `<code>` tags for code examples | No syntax highlighting | Use markdown fenced code blocks |
| `&nbsp;` for indentation in code | Fragile, ugly | Use real indentation in code blocks |
| Per-slide `<style>` blocks (7x duplication) | Maintenance nightmare | One `style.css` for globals |

## Export to PNG

```bash
cd slidev
pnpm export          # exports to PDF
# For individual PNGs:
slidev export --format png
```
