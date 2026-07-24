# Git & Commit Guidelines

## Conventional Commits
All commits must follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
type(scope): description

[optional body]
```

**Do NOT include Co-authored-by trailers** - commits should be attributed directly to the author/user.

### Common Scopes
- `docs(linux)`: Linux & shell notes
- `docs(color-science)`: Color science & math notes
- `docs(data-analytics)`: Data analytics & CLI wrangling
- `docs(python)`: Python language roadmaps and notes
- `docs(editors)`: Helix, Vim, tmux editor guides
- `docs(legal)`: Media law, business, and podcasting notes
- `docs(roadmap)`: Learning plan updates

### Examples
```
docs(color-science): add AgX tone mapper matrix transformation guide

- Include practical 3x3 inset matrix example
- Add LaTeX formula for Rec.709 color space
```

```
docs(editors): update Helix modal navigation plan
```

---
[Return to Agent Configuration Index](../agent.md)
