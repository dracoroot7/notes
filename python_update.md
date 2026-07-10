# Modern Python Learning Roadmap

A structured learning plan for beginners, integrating foundational Python with the modern, idiomatic features (Python 3.8 through 3.13+) highlighted in Reuven Lerner's *"python --update"* curriculum.

---

## Phase 1: Core Fundamentals (The Prerequisites)
Before jumping into advanced features like asynchronous programming or structural pattern matching, you need a solid grasp of how data moves in Python. Focus on your environment setup and basic syntax first.

* **Tooling Setup:** * Set up **VS Code** with the Python extension.
  * Get comfortable using **Jupyter Notebooks** for quick experimentation and code testing.
* **The Basics:**
  * Variables, Integers, Floats, and Booleans.
  * Control flow (`if`, `elif`, `else`) and loops (`for` and `while`).
  * Basic function syntax (`def my_function():`).

---

## Phase 2: Modern String Handling & Data Structures
This is where you begin incorporating modern updates immediately into your daily workflow instead of learning outdated patterns.

### 1. Modern String Formatting (`f-strings`)
* **What it is:** A cleaner, faster, and more readable way to format text.
* **What to learn:** * Basic interpolation: `f"Hello, {name}"`
  * *Modern Update feature (Python 3.8+):* The "equal sign" debugging shortcut: `f"{variable=}"` which prints both the variable name and its value instantly (e.g., `f"{x=}"` outputs `x=10`).

### 2. Core Collections & Unpacking
* **What to learn:** Lists, Dictionaries (`dict`), Sets, and Tuples.
* **Modern Twist:** * Learn how to merge dictionaries natively using the union operators (Python 3.9+): `dict_a | dict_b`.
  * Master **Iterable Unpacking** (e.g., `a, *rest, b = [1, 2, 3, 4, 5]`), which lays the conceptual foundation for advanced structural matching later.

---

## Phase 3: Structural Pattern Matching (Python 3.10+)
Once you understand control flow and basic data structures, you are ready for one of the most powerful modern additions to the language.

* **The Concept:** It looks like a traditional `switch/case` statement from other languages, but it actually extracts and matches the *shape*, *type*, and *contents* of your data structures.
* **How to practice:**
  * Move past standard, deeply nested `if/elif` chains for complex data parsing.
  * Write a script that processes commands or multi-part sequences using `match` and `case`.
  * *Example to try:*
    ```python
    match command.split():
        case ["move", ("north" | "south" | "east" | "west") as direction]:
            print(f"Moving {direction}")
        case ["quit"]:
            print("Goodbye!")
        case _:
            print("Unknown command")
    ```

---

## Phase 4: Modern Function Mechanics & Type Hinting
Modern Python heavily emphasizes code readability, catchable bugs, and precise tool behavior.

### 1. Advanced Arguments (Python 3.8+)
* **What to learn:** * Keyword-only arguments (using `*`).
  * *Modern Update feature:* Positional-only parameters using the `/` syntax. This forces certain arguments to be passed without their names, which is vital when designing clean APIs or building reusable utility functions.

### 2. Robust Type Hinting
* **What it is:** Adding annotations to your code to explicitly declare what type of data a variable or function expects.
* **What to focus on:**
  * Use VS Code's type checker (like Mypy or Pyright) to catch bugs before your code even runs.
  * Utilize modern native pipe syntax for unions (Python 3.10+): `def process_data(data: int | str):` instead of importing the older, verbose `typing.Union` module.

---

## Phase 5: Concurrency & Asyncio (The Capstone)
The final pillar of the update curriculum handles how Python deals with waiting—such as fetching data from the internet, reading massive files, or executing system tasks.

* **The Concept:** Learning the difference between synchronous execution (blocking) and asynchronous execution (non-blocking).
* **What to learn:**
  * The `async` and `await` keywords.
  * How to structure a program using the `asyncio` library to run tasks concurrently instead of one after another.

---

## Weekly Execution Strategy

To blend this effectively into your self-education, structure your learning around **theory, active typing, and tools**:

1. **Read/Watch the Concept:** Focus heavily on the *why* behind a feature, not just the syntax. 
2. **Code in Jupyter/VS Code:** Type out the exercises manually. If a tutorial shows an old method (like `%` formatting or `str.format()`), challenge yourself to rewrite it using modern f-strings or structural pattern matching instead.
3. **Reflect on Modernity:** When reviewing code or tutorials, always ask: *"Is this the idiomatic, modern way to write this in Python 3.13+, or is there a newer feature I should use?"*