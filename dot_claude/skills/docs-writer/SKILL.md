---
name: docs-writer
description: |
  Write, review, and edit technical documentation following Harrison.ai standards. Use when:
  (1) Creating or editing files in `docs/` directory (product/project documentation),
  (2) Creating or editing files in `adr/` directory (Architecture Decision Records),
  (3) Creating or editing files in `diagrams/` directory (D2 diagram files),
  (4) Writing or reviewing any `.md` file,
  (5) User asks to "write docs", "document this", "create an ADR", "/docs", or similar,
  (6) Reviewing documentation for style, accuracy, or completeness.
  Enforces sentence case headers, second-person voice, and active language.
---

# Documentation Writer Skill

Write and maintain technical documentation following Harrison.ai standards.

## Directory Conventions

| Directory | Purpose | Example |
|-----------|---------|---------|
| `docs/` | Product and project documentation | `docs/harrison-open-platform/architecture.md` |
| `adr/` | Architecture Decision Records | `adr/0001-use-d2-for-diagrams.md` |
| `diagrams/` | D2 diagram source files | `diagrams/system-context.d2` |
| `static/` | Images for documentation rendering | `static/annalise-wide.png` |

## Workflow

### Step 1: Clarify the request

Determine the documentation type:

| Request Contains | Document Type | Action |
|------------------|---------------|--------|
| "ADR", "decision", "why we chose" | ADR | See `references/adr-template.md` |
| "diagram", "visualize", "architecture" | D2 Diagram | Create in `diagrams/`, use d2-diagrams skill |
| Product/project name | Product docs | Create in `docs/<product-name>/` |
| General documentation | Standard docs | Follow standard structure below |

Ask if unclear:
- Is this a new document or an edit to existing content?
- What audience will read this documentation?
- Are there related documents to reference or link?

### Step 2: Investigate

Before writing:

1. **Read existing content** - Never propose changes without reading the file first
2. **Check related docs** - Look for documents that reference or link to this content
3. **Review code** (if documenting features) - Verify accuracy against implementation
4. **Check `static/`** - Identify existing images that could be reused
5. **Check `diagrams/`** - Look for existing D2 diagrams to reference or update

### Step 3: Write or edit

Apply writing standards from `references/writing-standards.md`:

**Structure:**
- Prerequisites at the start of procedural content
- Second-person voice ("you")
- Sentence case for headers ("Getting started" not "Getting Started")
- Language tags on all code blocks

**Tone:**
- Active voice and direct language
- No promotional language ("breathtaking", "exceptional")
- No editorializing ("it's important to note", "in conclusion")
- Limit conjunctions ("moreover", "furthermore", "additionally")

**Technical accuracy:**
- Verify all links work
- Test code examples before including
- Use relative paths for internal links

### Step 4: Verify

After writing:

1. **Re-read** - Check formatting and content accuracy
2. **Verify links** - Test all internal and external links
3. **Run formatters** if available (e.g., `npm run format`, `prettier`)

## Quick Reference: Common Tasks

### Create new product documentation

```bash
# Create directory structure
mkdir -p docs/<product-name>
```

### Create an ADR

```bash
# Create ADR directory if needed
mkdir -p adr

# Name format: NNNN-short-description.md
# Example: adr/0001-use-d2-for-diagrams.md
```

See `references/adr-template.md` for the complete ADR template with metadata table.

### Create a diagram

```bash
# Create diagrams directory if needed
mkdir -p diagrams

# Create D2 file
# Example: diagrams/system-architecture.d2
```

Render with:
```bash
d2 --layout tala --theme 5 diagrams/system-architecture.d2 static/system-architecture.png
```

### Add images to documentation

1. Place image in `static/` directory
2. Reference with relative path: `![Alt text](../static/image-name.png)`
3. Use descriptive filenames (e.g., `system-architecture-overview.png`)

## Reference Files

- **`references/writing-standards.md`** - Complete writing style guide
- **`references/adr-template.md`** - Architecture Decision Record template