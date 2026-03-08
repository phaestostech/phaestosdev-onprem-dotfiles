# Writing Standards

Documentation standards for Harrison.ai technical writing.

## Structure

### Headers

Use sentence case for all headers:

```markdown
# Getting started          ✓
# Getting Started          ✗

## API reference           ✓
## API Reference           ✗

### Code block titles      ✓
### Code Block Titles      ✗
```

### Prerequisites

Place prerequisites at the start of procedural content:

```markdown
## Deploy the application

**Prerequisites:**
- Configured AWS credentials
- Docker installed and running
- Access to the container registry

### Step 1: Build the image
...
```

### Code blocks

Always include language tags:

```markdown
    ```python
    def example():
        pass
    ```

    ```bash
    npm install
    ```

    ```yaml
    key: value
    ```
```

### Links

Use relative paths for internal links:

```markdown
See [architecture overview](../docs/architecture.md)     ✓
See [architecture overview](/docs/architecture.md)       ✗
```

## Voice and tone

### Second-person voice

Address the reader directly:

```markdown
You can configure the timeout by...     ✓
The user can configure the timeout...   ✗
One can configure the timeout by...     ✗
```

### Active voice

Prefer active over passive constructions:

```markdown
The system validates the input...       ✓
The input is validated by the system... ✗

Run the migration script...             ✓
The migration script should be run...   ✗
```

### Direct language

Remove unnecessary words:

```markdown
Configure the timeout...                ✓
In order to configure the timeout...    ✗

This enables caching...                 ✓
This serves to enable caching...        ✗
```

## Prohibited patterns

### Promotional language

Never use:
- "breathtaking"
- "exceptional value"
- "cutting-edge"
- "revolutionary"
- "game-changing"

### Editorializing

Remove phrases like:
- "It's important to note that..."
- "This article will..."
- "In conclusion..."
- "As mentioned earlier..."
- "Needless to say..."

### Conjunction overuse

Limit use of:
- "Moreover"
- "Furthermore"
- "Additionally"
- "On the other hand"
- "Consequently"

Replace with direct statements or simple transitions.

### Undue emphasis

Avoid overstating routine concepts:

```markdown
The API returns a JSON response.                        ✓
The API returns a critically important JSON response.   ✗
```

## Technical accuracy

### Link verification

Test all links before committing:
- Internal links resolve correctly
- External links are accessible
- Anchors point to existing headers

### Code examples

Test all code examples:
- Syntax is valid
- Examples run without errors
- Output matches documentation

### Terminology consistency

Use consistent terms throughout:
- Pick one term and stick with it (e.g., "endpoint" not sometimes "route")
- Match terminology used in the codebase
- Define acronyms on first use

## Formatting checklist

Before committing documentation:

- [ ] Sentence case headers
- [ ] Language tags on code blocks
- [ ] Relative paths for internal links
- [ ] All links verified
- [ ] Code examples tested
- [ ] No promotional language
- [ ] Active voice throughout
- [ ] Second-person voice ("you")