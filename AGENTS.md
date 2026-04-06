# Agent Configuration for Linux Notes Repository

## Project Overview
This repository contains practical Linux notes covering system administration, networking, shell scripting, and containerization. The primary goal is to document Linux concepts with clarity and concrete, working code examples.

## Agent Priorities & Guidelines

### Style & Presentation
- **Clarity first**: Explain concepts clearly before diving into details
- **Code examples required**: Every technical concept should include working, tested code examples
- **Markdown format**: All notes use markdown with consistent structure
- **Practical focus**: Prioritize real-world use cases over theoretical background

### Content Structure
- Use markdown headings for organization (h2 for major sections, h3 for subsections)
- Include code blocks with appropriate language syntax highlighting
- Provide bash/shell examples with copy-paste ready code
- Add explanatory comments in code where non-obvious
- Include expected output or behavior examples

### Topic Areas
Agents should have expertise in:
- **Linux System Administration**: User management, permissions, processes, services
- **Networking**: DNS, TCP/IP, firewalls, routing, package managers
- **Shell Scripting**: Bash, scripting best practices, automation
- **Containers**: Docker, container networking, image management
- **Markdown Note Taking**: Organization, formatting, documentation practices

### Agent Instructions
When working on notes:
1. Verify code examples work (test when possible)
2. Keep examples concise but complete
3. Explain why something works, not just how
4. Include common pitfalls and troubleshooting tips
5. Link related concepts within the notes
6. Use consistent formatting across all notes

### File Organization
- Root-level files: General/reference information
- Category subdirectories (if added): Grouped by topic
- Each note: Self-contained but cross-referenced

## Git & Commit Guidelines

### Conventional Commits
All commits must follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
type(scope): description

[optional body]
```

**Do NOT include Co-authored-by trailers** - commits should be attributed directly to the author/user.

**Common Types**:
- `docs`: Documentation changes (notes, README updates)
- `feat`: New features or notes
- `fix`: Bug fixes
- `refactor`: Code/content reorganization
- `perf`: Performance improvements
- `test`: Test additions or updates
- `chore`: Maintenance tasks

**Examples**:
```
docs(networking): add TCP/IP configuration guide

- Include practical examples for common scenarios
- Add troubleshooting section
```

```
feat(scripting): add bash best practices section
```

## Last Updated
2026-04-06

---

**Note**: This file guides Copilot agents in understanding project context and preferences. Update this as priorities or focus areas change.
