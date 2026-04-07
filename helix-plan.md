# Helix for Longform Writing: The Learning Plan

Helix (`hx`) is a modal editor that uses a **Selection → Action** (Object → Verb) approach. For writers, this means you see exactly what you are about to change before you change it. This plan is designed to turn Helix into a distraction-free environment for novels, documentation, and longform prose.

---

## 🟢 Phase 1: The Helix Mindset (Getting Started)
*Goal: Move from "typing" to "editing at the speed of thought."*

- [ ] **Run the Tutor:** Open your terminal and type `hx --tutor`. Complete it daily for 3 days to build muscle memory.
- [ ] **Master the "Object-Verb" Flow:** 
    - In Helix, you select the object first, then perform the action (e.g., `w` then `d` to delete a word).
- [ ] **Basic Movement:**
    - `w`, `e`, `b`: Jump by words (essential for navigating sentences).
    - `f` / `t`: Find or "Till" a specific character in a line.
- [ ] **Paragraph Navigation:** Master `(` and `)` to jump between blocks of text. This is your primary way to travel through a manuscript.
- [ ] **Selection Expansion:** Use `v` (select mode) combined with movement to highlight exactly what you want to move or delete.

## 🟡 Phase 2: Configuring the Writing Environment
*Goal: Optimize the UI for prose rather than code.*

- [ ] **Create your `config.toml`:** Open `~/.config/helix/config.toml` and add settings for better readability.
- [ ] **Enable Soft Wrap:** Critical for prose so text doesn't run off the screen.
  ```toml
  [editor]
  soft-wrap.enable = true
  line-numbers = "relative" # Helps with jumping lines
  cursorline = true
  gutters = ["diagnostics", "line-numbers"]
  ```
- [ ] **Choose a Low-Contrast Theme:** Use themes like `gruvbox`, `solarized_dark`, or `everforest` to reduce eye strain.
- [ ] **Select a Prose Font:** Set your terminal to use a clean monospaced font like *JetBrains Mono*, *Cascadia Code*, or *iA Writer Quattro*.

## 🟠 Phase 3: The Prose Workflow (Markdown & Docs)
*Goal: Use Helix's built-in power to structure your writing.*

- [ ] **Markdown Mastery:** Helix has native tree-sitter support for Markdown.
    - Use `gs` (Go to Symbol) to see a table of contents (headings) and jump between chapters.
- [ ] **Multiple Cursors:** Use `C` to copy a selection to the next line or `s` to select specific words (like a character's name) to change them globally.
- [ ] **Spell Check & Grammar:**
    - Install `ltex-ls` (LTeX Language Server) for real-time grammar and spell-checking in Markdown files.
    - Errors will appear in the diagnostics gutter; use `d` to view suggestions.

## 🔴 Phase 4: Organizing Large Projects (Novels/Manuals)
*Goal: Managing 50,000+ words without getting lost.*

- [ ] **Project Navigation:** Use `space + f` to jump between chapter files or scene files instantly.
- [ ] **Global Search:** Use `space + /` to search your entire project for a specific keyword, character, or plot point.
- [ ] **Vertical Splits:** Use `ctrl-w + v` to keep your "Outlines" or "Character Sheet" open on one side while you write on the other.
- [ ] **Buffers:** Use `ctrl-p` and `ctrl-n` to cycle through your recently opened chapters.

---

## 💡 Tips & Tricks for Longform Content

### 1. The "Zen Mode" Workaround
Helix doesn't have a native "Zen Mode," but you can simulate it:
- Temporarily hide line numbers with `:set line-numbers false`.
- Use a terminal emulator (like Alacritty or Kitty) that allows you to add "padding" to the window borders to center the text.

### 2. Scratchpads for World Building
Keep a `notes.md` file in your project root. Use `space + b` to quickly switch between your manuscript and your notes without losing your flow.

### 3. Version Control as "Save States"
Treat your writing like code. Use **Git** to commit your progress:
- `git commit -m "Finish Chapter 5 - The Confrontation"`.
- This allows you to experiment with different plot paths and "revert" if a scene goes nowhere.

### 4. Instant Word Count
To check your word count without leaving the editor:
- Press `%` to select the entire file.
- Press `|` (pipe) and type `wc -w`.
- The result will appear in the status bar (remember to `u` undo the change immediately so you don't keep the count in your text!).

### 5. Using "Marks" for Quick Returns
When you need to fact-check an earlier chapter:
- Press `m` then `a` to "mark" your current writing position.
- Go find your information.
- Press `'` then `a` to instantly teleport back to where you were.

---

> **The Writer's Mandate:** "The tool should disappear. Master the keys so your fingers can keep up with your imagination."
