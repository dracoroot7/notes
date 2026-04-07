# Data Analyst Learning Plan: The Shell-Native Path

This plan treats data analysis as an engineering discipline. You will learn to manipulate data at the source using the command line and Python, while mastering the industry-standard tools for business communication (Excel, Tableau, etc.).

---

## 🟢 Phase 1: The Shell-Native Foundation
*Goal: Learn to "see" and "clean" data using Unix pipes before touching a programming language.*

- [ ] **Shell Basics:** Master `cd`, `ls`, `cat`, `less`, `head`, and `tail`.
- [ ] **The "Holy Trinity" for Data:**
    - `grep`: Filter rows based on patterns.
    - `sed`: Bulk-replace or transform text in a stream.
    - `awk`: The original data processing language. Perfect for columns and basic math on CSVs.
- [ ] **Structured Data on the CLI:**
    - `jq`: Expert slicing and dicing of **JSON**.
    - `csvkit`: A suite of tools to convert, query, and summarize **CSV** files directly from the terminal.
- [ ] **The Pipeline:** Master the pipe (`|`) and redirection (`>`). Learn to turn `cat data.csv | awk ... | sort | uniq -c` into a power-user habit.

## 🟡 Phase 2: Mathematical Foundations
*Goal: Understand the "Why" behind the numbers. Data analysis is just applied statistics.*

- [ ] **Descriptive Statistics:** Mean, Median, Mode, Standard Deviation, and Distributions.
- [ ] **Inferential Statistics:** Hypothesis Testing (P-values, T-tests), A/B Testing, and Confidence Intervals.
- [ ] **Probability Theory:** Conditional Probability and Bayes' Theorem.
- [ ] **Linear Algebra & Calculus:** Basic Vectors/Matrices (for NumPy) and Derivatives (understanding how models "learn").
- [ ] **Regression:** Linear and Logistic Regression (the foundations of predictive analysis).

## 🟠 Phase 3: Spreadsheet Mastery (Excel & Google Sheets)
*Goal: Master the world's most popular data tools for quick analysis and reporting.*

- [ ] **Foundations:**
    - **Lookup Functions:** VLOOKUP, XLOOKUP, and INDEX/MATCH.
    - **Logical Functions:** IF, AND, OR, and Nested IFs.
- [ ] **Data Cleaning & Aggregation:**
    - **Pivot Tables:** Summarizing thousands of rows in seconds.
    - **Power Query (Excel):** Learn to build repeatable data cleaning steps without code.
- [ ] **Google Sheets Specifics:**
    - **QUERY Function:** Use a "SQL-like" syntax inside your spreadsheet.
    - **Importing Data:** Use `IMPORTXML` and `IMPORTHTML` to pull data from the web.
- [ ] **Interfacing with the Shell:** Learn to export and import `.csv` and `.tsv` files to move data between your spreadsheet and your CLI tools.

## 🔵 Phase 4: Python for Data Science
*Goal: Use Python to build reproducible, automated data pipelines.*

- [ ] **Python Fundamentals:** Lists, Dictionaries, List Comprehensions, and Functions.
- [ ] **The Data Stack:**
    - **NumPy:** Numerical computing and array manipulation.
    - **Pandas:** The industry standard for DataFrames. Master `.groupby()`, `.merge()`, and `.pivot_table()`.
- [ ] **Visualization (Static):** **Matplotlib** and **Seaborn**. Learn to create clear, non-distracting charts.
- [ ] **Automation:** Write scripts to clean raw files and export them directly to `.xlsx` for your stakeholders.

## 🔴 Phase 5: Data Storage & Retrieval (SQL)
*Goal: Most data lives in databases. You must be able to talk to them.*

- [ ] **SQL Mastery:**
    - Learn `SELECT`, `WHERE`, `JOIN` (Inner, Left, Right), and `GROUP BY`.
    - Window Functions (`RANK`, `LEAD`, `LAG`).
    - Common Table Expressions (CTEs) for readable, complex queries.
- [ ] **CLI Clients:** Use `sqlite3` for local files and `psql` (Postgres) or `mycli` for remote databases.

## 🟣 Phase 6: Data Storytelling & BI (Tableau)
*Goal: Turn raw analysis into interactive dashboards that drive business decisions.*

- [ ] **Tableau Fundamentals:**
    - Connecting to data sources (CSVs, SQL Databases, Google Sheets).
    - **Calculated Fields:** Building custom logic and metrics inside Tableau.
- [ ] **Visualization Best Practices:**
    - When to use a Bar Chart vs. a Line Chart vs. a Scatter Plot.
    - Using color and layout to highlight the "Main Insight."
- [ ] **Dashboards:** Building interactive filters and "drill-down" capabilities for non-technical users.

## 🟤 Phase 7: Version Control & Portfolio
*Goal: Show the world you can solve problems.*

- [ ] **Git for Data:** Track your scripts and SQL queries. Use `.gitignore` for large datasets.
- [ ] **GitHub Portfolio:**
    - **Project 1:** A Python script that cleans data and exports a Tableau-ready CSV.
    - **Project 2:** A SQL-heavy project answering a business question.
    - **Project 3:** An interactive Tableau dashboard (shared via Tableau Public).

---

## 📜 Tips for the Data Analyst
1.  **Don't Over-Tool:** If you can answer a question in 2 minutes with `awk` or Excel, don't spend 20 minutes writing a Python script.
2.  **Automate the Boring Stuff:** If you have to make the same Excel chart every Monday, use Python (`openpyxl` or `pandas`) to generate it automatically.
3.  **Know Your Audience:** Tech leads want to see your Python/SQL code; managers want to see your Tableau dashboard or Excel summary.
