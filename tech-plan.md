# The Way: Tech Career Learning Plan

Inspired by **rwxrob** (Rob Muhlestein), this plan emphasizes becoming a "Value-Driven Engineer" by mastering the "Standard Model" of computing: Linux, the Shell, and the tools that power the internet.

---

## 🟢 Phase 1: The Foundation (Becoming Shell Native)
*Goal: Stop using a mouse. Make the terminal your home.*

- [ ] **Burn the Boats:** Install **Linux** (Ubuntu or Debian) as your primary OS. No VMs, no dual-booting.
- [ ] **The Multiplexer:** Master **tmux**. Learn to manage sessions, windows, and panes to organize your workspace.
- [ ] **The Editor:** Learn **Vim** (or Neovim). Start with `vimtutor`. Aim to edit text at the speed of thought.
- [ ] **The Shell:** Deep dive into **Bash**. Understand environment variables, aliases, functions, and the `~/.bashrc`.
- [ ] **Unix Utilities (The Standard Model):**
    - `ls`, `cd`, `mkdir`, `rm`, `cp`, `mv`
    - `grep`, `sed`, `awk` (The holy trinity of text processing)
    - `find`, `xargs`, `cat`, `less`, `tail`, `head`

## 🟡 Phase 2: Networking & Infrastructure Fundamentals
*Goal: Understand how the world connects. Don't just use the web; understand the wires.*

- [ ] **The Networking Toolkit:** Master the commands that reveal the network:
    - `ip addr`, `ip route` (Manage interfaces and routing)
    - `ss` or `netstat` (View active connections and ports)
    - `dig` and `nslookup` (Master DNS—it's always a DNS problem)
    - `curl` and `wget` (The command-line browser)
    - `nmap` (Network exploration and security auditing)
- [ ] **SSH as a Power Tool:** Move beyond simple logins. Learn SSH keys, config files, and **SSH Tunneling** (Local/Remote port forwarding).
- [ ] **Protocols:** Understand the "Big Three" of the modern web: **IP**, **TCP/UDP**, and **HTTP/S**. Learn to "see" the headers.
- [ ] **The OSI Model:** Focus on Layers 3 (Network), 4 (Transport), and 7 (Application).

## 🟠 Phase 3: Data Analysis (The Unix Way)
*Goal: Process data at the speed of thought using shell pipelines.*

- [ ] **Text Processing Mastery:**
    - `grep`: Advanced searching and filtering with Regex.
    - `sed`: Stream editing and complex transformations.
    - `awk`: Use it as a programming language for tabular data and reporting.
- [ ] **Structured Data Tools:**
    - `jq`: The "Swiss Army Knife" for **JSON** data.
    - `yq`: The equivalent for **YAML**.
- [ ] **The Pipeline Philosophy:** Master `cut`, `sort`, `uniq`, `wc`, and `xargs`. Learn to pipe them together to turn raw logs into business insights.
- [ ] **Databases (SQL):** Learn **PostgreSQL** or **SQLite**. Focus on querying from the CLI using `psql` or `sqlite3`. Learn joins, aggregations, and indexing.
- [ ] **Programming for Data:** 
    - **Go (Golang):** Use it to build high-performance data parsers and CLI utilities.
    - **Python:** Learn `Pandas` and `NumPy` for complex statistical analysis once shell basics are mastered.

## 🔴 Phase 4: Systems, Version Control & Automation
*Goal: Build tools that solve real problems and track them properly.*

- [ ] **Git Mastery:** Use Git strictly from the command line. Understand the "why" of every commit.
- [ ] **Containers:** Master **Docker** (or Podman). Understand images, volumes, and container networking.
- [ ] **Zettelkasten (Second Brain):** Document every command, bug, and fix in a local "notes" repository.
- [ ] **AI-Assisted Engineering:** Learn "vibe coding" and effective prompting to accelerate research, but maintain deep technical understanding.

## 🟣 Phase 5: Career Strategy & The Industry
*Goal: Become a "Hireable Human."*

- [ ] **Industry Mapping:** Identify your "Top 10" dream companies. Research their tech stacks and problems.
- [ ] **Public Portfolio:** Build and publish at least 3 useful CLI tools or APIs on GitHub (e.g., a Go-based log analyzer or a Bash networking dashboard).
- [ ] **Soft Skills (The Real Hard Skills):** Practice empathy, active listening, and technical writing. Remember: *Software is a people business.*
- [ ] **Networking:** Join the `rwxrob` Discord or Twitch community. Start "paying it forward" by helping others who are one step behind you.

---

## 📜 The Core Tenets to Live By
1.  **Value over Tools:** Don't learn a tool because it's "cool"; learn it because it allows you to provide more value.
2.  **RTFM (Read The Fabulous Manual):** Before asking for help, read the `man` pages.
3.  **Show Your Work:** Learn in public. Commit daily.
4.  **Be Kind:** Technical skill without character is a liability.

> *"The shell is the great equalizer. Master the pipeline, and you master the data."* — **rwxrob**
