---
name: backlog-document-management
description: Use when you need to create, view, update, or search documents in the project backlog. Documents are markdown files for storing reference material, design docs, specs, and other project documentation.
---

# Backlog Document Management

This skill provides guidance for managing documents using the Backlog.md CLI tool.

## CLI Commands Reference

### Listing Documents

```bash
backlog doc list --plain
backlog doc list --search "keyword" --plain
```

**Always use `--plain` flag for AI-friendly text output.**

### Viewing Document Details

```bash
backlog doc view <id> --plain
```

Returns document metadata (ID, title, type, dates, tags) and content.

### Creating Documents

```bash
backlog doc create "Document Title" --content "Document content here"
```

Documents are auto-assigned an ID and stored as markdown files.

### Updating Documents

```bash
backlog doc update <id> --title "New Title" --content "Updated content"
```

### Searching Documents

```bash
backlog doc search "query" --plain
```

Performs fuzzy search across document titles and content.

## Document Use Cases

Documents are ideal for:
- **Design documentation** - Architecture decisions, system design
- **Reference material** - API specs, configuration guides
- **Project documentation** - README content, onboarding guides
- **Meeting notes** - Decisions, action items
- **Technical specs** - Requirements, constraints

## Best Practices

1. **Use descriptive titles** - Make documents easy to find
2. **Keep content focused** - One topic per document
3. **Use markdown formatting** - Headers, lists, code blocks for readability
4. **Tag appropriately** - Use tags for categorization
5. **Update regularly** - Keep documentation current

## Tips for AI Agents

- **Always use `--plain` flag** when listing or viewing documents
- **Search before creating** to avoid duplicate documentation
- **Reference documents** in tasks when relevant context exists
- **Keep documents atomic** - focused on a single topic or purpose
