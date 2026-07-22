# Mastering `grep`, `sed`, and `awk`: The Command-Line Data Processing Trinity

A comprehensive, hands-on beginner course designed to master Linux stream processing tools—`grep`, `sed`, and `awk`—from fundamental syntax to real-world applications in Data Analysis and System Administration.

---

## 📌 Course Table of Contents
- [Module 0: Course Overview & Setup](#module-0-course-overview--setup)
- [Module 1: Pattern Searching with `grep` (The Filter)](#module-1-pattern-searching-with-grep-the-filter)
- [Module 2: Stream Editing with `sed` (The Transformer)](#module-2-stream-editing-with-sed-the-transformer)
- [Module 3: Text & Data Processing with `awk` (The Mini Language)](#module-3-text--data-processing-with-awk-the-mini-language)
- [Module 4: Pipeline Mastery (Combining the Trinity)](#module-4-pipeline-mastery-combining-the-trinity)
- [Module 5: The Data Analyst Toolkit (Real-World CSV/Log Analysis)](#module-5-the-data-analyst-toolkit-real-world-csvlog-analysis)
- [Module 6: Curated Learning Resources & Cheat Sheets](#module-6-curated-learning-resources--cheat-sheets)

---

## Module 0: Course Overview & Setup

### Why Learn `grep`, `sed`, and `awk`?
In modern data engineering, system administration, and data analysis, files often reach sizes measured in gigabytes or terabytes. Loading a 10 GB CSV into Excel or Pandas can freeze your machine or crash Python due to memory constraints.

The Unix text-processing trinity—`grep`, `sed`, and `awk`—operates as **stream processors**. They process files line-by-line in memory, enabling near-instant searching, cleaning, and aggregation without heavy resource usage.

| Tool | Primary Purpose | Mental Model |
| :--- | :--- | :--- |
| **`grep`** | Searching & Filtering | A high-speed sieve for text lines matching patterns |
| **`sed`** | Stream Editing & Transformation | Find-and-replace on steroids |
| **`awk`** | Column Extraction & Data Aggregations | A full lightweight data reporting language |

---

### Setup & Practice Datasets
To follow along with all exercises in this course, create a test directory and populate it with sample datasets by running the following commands in your shell:

```bash
# Create practice directory
mkdir -p ~/cli_course && cd ~/cli_course

# 1. Create a sample web server log (access.log)
cat << 'EOF' > access.log
192.168.1.10 - - [10/Jul/2026:13:55:36 +0000] "GET /index.html HTTP/1.1" 200 1420
192.168.1.12 - - [10/Jul/2026:13:56:01 +0000] "POST /login HTTP/1.1" 401 512
10.0.0.5 - - [10/Jul/2026:13:57:15 +0000] "GET /api/v1/users HTTP/1.1" 200 48200
192.168.1.10 - - [10/Jul/2026:13:58:22 +0000] "GET /images/logo.png HTTP/1.1" 304 0
172.16.0.4 - - [10/Jul/2026:14:01:05 +0000] "GET /dashboard HTTP/1.1" 500 230
192.168.1.12 - - [10/Jul/2026:14:02:11 +0000] "POST /login HTTP/1.1" 200 1024
10.0.0.5 - - [10/Jul/2026:14:05:40 +0000] "DELETE /api/v1/user/99 HTTP/1.1" 403 128
EOF

# 2. Create a sample sales dataset (sales.csv)
cat << 'EOF' > sales.csv
ID,Date,Region,Category,Sales,Units
101,2026-01-15,North,Electronics,1200.50,4
102,2026-01-16,South,Furniture,450.00,2
103,2026-01-16,East,Electronics,890.00,3
104,2026-01-17,North,Clothing,120.25,5
105,2026-01-18,West,Electronics,2300.00,8
106,2026-01-19,South,Electronics,150.00,1
107,2026-01-20,East,Furniture,1150.75,4
108,2026-01-20,West,Clothing,85.00,2
EOF
```

---

## Module 1: Pattern Searching with `grep` (The Filter)

`grep` stands for **G**lobal **R**egular **E**xpression **P**rint. It scans input line-by-line and prints lines that match a specified pattern.

### 1.1 Basic Usage & Essential Flags
Basic syntax:
```bash
grep [OPTIONS] PATTERN [FILE...]
```

#### Key Flags Reference
- `-i`: Case-insensitive matching
- `-v`: Invert match (show lines that do **not** match)
- `-n`: Print line numbers alongside matches
- `-c`: Count matching lines instead of displaying them
- `-w`: Match whole words only
- `-r` / `-R`: Search recursively through subdirectories

#### Code Examples

```bash
# Search for case-insensitive occurrences of 'electronics'
grep -i "electronics" sales.csv
```
*Output:*
```text
101,2026-01-15,North,Electronics,1200.50,4
103,2026-01-16,East,Electronics,890.00,3
105,2026-01-18,West,Electronics,2300.00,8
106,2026-01-19,South,Electronics,150.00,1
```

```bash
# Count how many HTTP requests in access.log returned a status 200 OK
grep -c " 200 " access.log
```
*Output:*
```text
3
```

```bash
# Filter out CSV header and blank lines (invert match)
grep -v -e "^ID," -e "^$" sales.csv
```

---

### 1.2 Context Flags (`-A`, `-B`, `-C`)
When searching log files or codebases, seeing surrounding context is invaluable.

- `-B N`: Show **N** lines **Before** match
- `-A N`: Show **N** lines **After** match
- `-C N`: Show **N** lines of **Context** (both before and after)

```bash
# Find server errors (500) and display 1 line before and after
grep -C 1 " 500 " access.log
```
*Output:*
```text
192.168.1.10 - - [10/Jul/2026:13:58:22 +0000] "GET /images/logo.png HTTP/1.1" 304 0
172.16.0.4 - - [10/Jul/2026:14:01:05 +0000] "GET /dashboard HTTP/1.1" 500 230
192.168.1.12 - - [10/Jul/2026:14:02:11 +0000] "POST /login HTTP/1.1" 200 1024
```

---

### 1.3 Regular Expressions with `grep` (`-E`)
Using `grep -E` (or `egrep`) enables **Extended Regular Expressions (ERE)** for powerful pattern matching.

| Regex Symbol | Meaning | Example |
| :--- | :--- | :--- |
| `^` | Start of line | `^192\.168` |
| `$` | End of line | `0$` |
| `.` | Any single character | `2026-..-16` |
| `[abc]` | Any character in set | `[NSEW]orth` |
| `(A\|B)` | Alternation (OR) | `(POST\|DELETE)` |
| `+` | One or more occurrences | `[0-9]+` |

```bash
# Find all HTTP requests with method POST or DELETE
grep -E '"(POST|DELETE)' access.log
```
*Output:*
```text
192.168.1.12 - - [10/Jul/2026:13:56:01 +0000] "POST /login HTTP/1.1" 401 512
192.168.1.12 - - [10/Jul/2026:14:02:11 +0000] "POST /login HTTP/1.1" 200 1024
10.0.0.5 - - [10/Jul/2026:14:05:40 +0000] "DELETE /api/v1/user/99 HTTP/1.1" 403 128
```

---

## Module 2: Stream Editing with `sed` (The Transformer)

`sed` stands for **S**tream **Ed**itor. It performs text transformations line-by-line as data flows through it.

### 2.1 Basic Substitution Syntax
The most common `sed` operation is substitution (`s` command):

```bash
sed 's/pattern/replacement/flags' filename
```

#### Common Substitution Flags
- `g`: Replace **all** matches on each line (global replacement). Without `g`, only the first match per line is changed.
- `I` / `i`: Case-insensitive match.
- `2`: Replace only the 2nd match on each line.

#### Code Examples

```bash
# Replace 'North' with 'NORTH_REGION' (preview output, doesn't edit file)
sed 's/North/NORTH_REGION/g' sales.csv
```

```bash
# Replace date dashes (-) with slashes (/) for the first 2 occurrences
sed 's/-/\//g' sales.csv
```

---

### 2.2 In-Place File Editing (`sed -i`)
By default, `sed` writes its output to stdout without modifying the original file. Use `-i` to modify the file directly.

> [!CAUTION]
> Always test your `sed` command without `-i` first, or supply an extension like `-i.bak` to create a backup file before modifying in place.

```bash
# Safely edit in-place with a backup created automatically
sed -i.bak 's/Furniture/Home Decor/g' sales.csv

# Inspect modified file
head -n 4 sales.csv
```
*Output:*
```text
ID,Date,Region,Category,Sales,Units
101,2026-01-15,North,Electronics,1200.50,4
102,2026-01-16,South,Home Decor,450.00,2
103,2026-01-16,East,Electronics,890.00,3
```

---

### 2.3 Address Targeting & Deletions
You can restrict `sed` commands to specific lines by line numbers or pattern matching.

```bash
# Delete the header row (line 1)
sed '1d' sales.csv

# Delete lines 5 through 8
sed '5,8d' sales.csv

# Delete lines containing 'Clothing'
sed '/Clothing/d' sales.csv
```

```bash
# Selective substitution: Replace 'Electronics' with 'TECH' ONLY on line 2
sed '2s/Electronics/TECH/' sales.csv
```

---

### 2.4 Advanced `sed`: Capturing Groups (`\1`, `\2`)
Using capture groups allows reordering or extracting parts of matched strings.

```bash
# Convert date format YYYY-MM-DD to DD/MM/YYYY
sed -E 's/([0-9]{4})-([0-9]{2})-([0-9]{2})/\3\/\2\/\1/g' sales.csv
```
*Output snippet:*
```text
101,15/01/2026,North,Electronics,1200.50,4
```

---

## Module 3: Text & Data Processing with `awk` (The Mini Language)

`awk` (named after creators Aho, Weinberger, and Kernighan) is a full-fledged pattern-scanning and text-processing language. It excels at processing tabular data (CSVs, TSVs, log outputs).

### 3.1 Anatomy of an `awk` Program

An `awk` command consists of three main blocks:

```awk
BEGIN {
    # Executed ONCE before reading any lines (e.g., set delimiters, print headers)
}
PATTERN {
    # Executed for EVERY LINE that matches PATTERN (or all lines if omitted)
}
END {
    # Executed ONCE after processing all lines (e.g., print totals/summary)
}
```

---

### 3.2 Fields & Built-in Variables

When `awk` reads a line (called a **Record**), it automatically splits it into **Fields** (columns) based on the Field Separator (`FS`).

| Variable | Description |
| :--- | :--- |
| `$0` | The entire line/record |
| `$1, $2, ...` | 1st field, 2nd field, etc. |
| `$NF` | The last field of the current line |
| `NF` | Total Number of Fields in current line |
| `NR` | Current Record Number (line number) |
| `FS` | Input Field Separator (default: whitespace) |
| `OFS` | Output Field Separator (default: space) |

```bash
# Print Region (Col 3) and Sales (Col 5) from sales.csv
# Specify comma as field separator using -F','
awk -F',' '{ print $3, $5 }' sales.csv
```
*Output:*
```text
Region Sales
North 1200.50
South 450.00
East 890.00
...
```

---

### 3.3 Conditional Filtering & Formatting (`printf`)
You can use standard logical operators (`==`, `!=`, `>`, `<`, `>=`, `<=`, `&&`, `||`) inside `awk`.

```bash
# Filter sales > 500 in the North region, formatting output neatly with printf
awk -F',' 'NR > 1 && $3 == "North" && $5 > 500 {
    printf "ID: %-5s | Category: %-12s | Sales: $%.2f\n", $1, $4, $5
}' sales.csv
```
*Output:*
```text
ID: 101   | Category: Electronics  | Sales: $1200.50
```

---

### 3.4 Aggregations & Math Calculations
`awk` keeps track of variables across lines, making calculations trivial.

```bash
# Calculate Total Revenue and Average Sales per transaction (excluding header)
awk -F',' '
NR > 1 {
    sum += $5
    count++
}
END {
    printf "Total Revenue : $%.2f\n", sum
    printf "Average Sale  : $%.2f\n", sum / count
}' sales.csv
```
*Output:*
```text
Total Revenue : $6426.50
Average Sale  : $803.31
```

---

### 3.5 Group By with Associative Arrays
One of `awk`'s most powerful features is **associative arrays** (hash maps/dictionaries).

```bash
# Calculate Total Sales grouped by Region
awk -F',' '
NR > 1 {
    sales_by_region[$3] += $5
}
END {
    print "Region      Total Sales"
    print "-----------------------"
    for (region in sales_by_region) {
        printf "%-10s  $%10.2f\n", region, sales_by_region[region]
    }
}' sales.csv
```
*Output:*
```text
Region      Total Sales
-----------------------
North       $   1320.75
South       $    600.00
East        $   2040.75
West        $   2385.00
```

---

## Module 4: Pipeline Mastery (Combining the Trinity)

The real magic happens when you chain `grep`, `sed`, `awk`, and classic Unix tools (`sort`, `uniq`, `wc`, `cut`, `tr`, `column`) using pipes (`|`).

### 4.1 Real-World Log Analysis Pipeline
**Goal:** Identify the top 3 IP addresses causing client HTTP errors (status 4xx).

```bash
# Step 1: Filter 4xx errors with grep
# Step 2: Extract IP address (1st column) with awk
# Step 3: Sort & count frequency with sort | uniq -c
# Step 4: Sort descending numerically and grab top 3
grep -E ' HTTP/1\.[01]" 4[0-9]{2} ' access.log \
  | awk '{ print $1 }' \
  | sort \
  | uniq -c \
  | sort -nr \
  | head -n 3
```
*Output:*
```text
   1 192.168.1.12
   1 10.0.0.5
```

---

### 4.2 Cleaning & Formatting CSV Data
**Goal:** Clean a raw CSV by stripping quotes, filtering zero sales, and printing a clean aligned report.

```bash
# Chain sed (strip quotes) into awk (filter & calculate) into column (align tabular output)
cat sales.csv \
  | sed 's/"//g' \
  | awk -F',' 'NR > 1 && $5 > 0 { printf "%s|%s|%.2f\n", $3, $4, $5 }' \
  | column -t -s '|'
```

---

## Module 5: The Data Analyst Toolkit (Real-World CSV/Log Analysis)

For data analysts, the command line provides unmatched speed for **Exploratory Data Analysis (EDA)**, data validation, and pre-ingestion cleaning.

```
       ┌──────────────┐
       │   Raw Data   │
       │ (.csv/.log)  │
       └──────┬───────┘
              │
              ▼
   ┌──────────────────────┐
   │  grep: Filter rows   │
   └──────────┬───────────┘
              │
              ▼
   ┌──────────────────────┐
   │ sed: Clean & sanitize│
   └──────────┬───────────┘
              │
              ▼
   ┌──────────────────────┐
   │ awk: Group & Aggregate│
   └──────────┬───────────┘
              │
              ▼
    ┌────────────────────┐
    │  Clean Summary /   │
    │  Database / Python │
    └────────────────────┘
```

### 5.1 Quick Data Profiling (EDA)
Before writing a single line of SQL or Python, profile your file in seconds:

```bash
# 1. Total record count (excluding header)
expr $(wc -l < sales.csv) - 1

# 2. Check for missing / empty fields in column 5
awk -F',' '$5 == "" || $5 == "NA" || $5 == "null" { print "Missing value on line:", NR }' sales.csv

# 3. Unique value count per category
awk -F',' 'NR > 1 { print $4 }' sales.csv | sort | uniq -c | sort -nr
```

---

### 5.2 Data Standardization & Sanitization Recipe
Data collected from APIs or web forms often has formatting issues. Here is a standard pre-processing pipeline script:

```bash
cat << 'EOF' > clean_pipeline.sh
#!/usr/bin/env bash
# Clean raw sales data before SQL database insertion
# 1. Fix date format (YYYY/MM/DD -> YYYY-MM-DD)
# 2. Convert text to UPPERCASE for consistency
# 3. Remove trailing whitespace and carriage returns

cat sales.csv \
  | tr -d '\r' \
  | sed -E 's/([0-9]{4})\/([0-9]{2})\/([0-9]{2})/\1-\2-\3/g' \
  | awk -F',' 'BEGIN{OFS=","} NR==1{print} NR>1{$3=toupper($3); $4=toupper($4); print}'
EOF

chmod +x clean_pipeline.sh
./clean_pipeline.sh
```

---

### 5.3 Calculating Conversion & Error Rates from Logs
Calculate server response success vs failure percentage directly on the command line:

```bash
awk '
{
    total++
    status = $9
    if (status >= 200 && status < 300) success++
    else if (status >= 400) errors++
}
END {
    printf "Total Requests : %d\n", total
    printf "Success Rate   : %.2f%%\n", (success / total) * 100
    printf "Error Rate     : %.2f%%\n", (errors / total) * 100
}' access.log
```
*Output:*
```text
Total Requests : 7
Success Rate   : 42.86%
Error Rate     : 42.86%
```

---

## Module 6: Curated Learning Resources & Cheat Sheets

### 📺 Recommended YouTube Videos & Tutorials
1. **`grep` Fundamentals**:
   - [Corey Schafer - Linux/Mac Terminal Tutorial: grep Command](https://www.youtube.com/watch?v=VGgTmxXp7xU) – Great beginner guide on pattern searching flags.
   - [NetworkChunk - You NEED to learn grep RIGHT NOW!!](https://www.youtube.com/watch?v=0_fep9Y8Hag) – High energy introduction to practical system grep usage.

2. **`sed` Mastery**:
   - [Luke Smith - Learn sed in 15 Minutes](https://www.youtube.com/watch?v=AZ6997j4Dbg) – Fast-paced, practical explanation of regex substitutions and editing.
   - [FreeCodeCamp - Linux Command Line Tutorial / sed & awk](https://www.youtube.com/watch?v=78I5951_8Z4) – In-depth walkthrough of stream editing techniques.

3. **`awk` Programming**:
   - [The Cyber Mentor - AWK Tutorial: Beginner to Advanced](https://www.youtube.com/watch?v=9YOHp8N-a-o) – Excellent overview of fields, patterns, and logic.
   - [DistroTube - AWK: The Power of Text Processing](https://www.youtube.com/watch?v=gT8w_YxH7vI) – Concrete examples of data extraction and report generation.

---

### 🌐 Essential Websites & Reference Manuals
- [GNU Awk User's Guide (Official Manual)](https://www.gnu.org/software/gawk/manual/gawk.html) – The definitive reference for `awk`.
- [ExplainShell.com](https://explainshell.com/) – Paste any complex `grep`/`sed`/`awk` command pipeline to see a visual breakdown of every flag.
- [Regex101.com](https://regex101.com/) – Interactive regular expression tester and debugger for Unix regex patterns.
- [The Grymoire - Sed Tutorial](https://www.grymoire.com/Unix/Sed.html) – The classic, ultimate guide to `sed` stream editing.
- [The Grymoire - Awk Tutorial](https://www.grymoire.com/Unix/Awk.html) – Comprehensive tutorial for `awk` mastery.

---

### ⚡ Quick Cheat Sheet

```bash
# --- GREP CHEAT SHEET ---
grep "text" file              # Find string in file
grep -i "text" file           # Case-insensitive search
grep -v "text" file           # Invert match (lines WITHOUT text)
grep -rn "text" /path/        # Recursive search with line numbers
grep -E "pattern1|pattern2"   # Extended regex OR match

# --- SED CHEAT SHEET ---
sed 's/old/new/' file         # Replace 1st occurrence per line
sed 's/old/new/g' file        # Replace ALL occurrences per line
sed 's/old/new/gi' file       # Case-insensitive replacement
sed '5d' file                 # Delete line 5
sed -i 's/old/new/g' file     # In-place file replacement

# --- AWK CHEAT SHEET ---
awk '{print $1}' file         # Print 1st field (whitespace-separated)
awk -F',' '{print $1,$3}'     # Print 1st and 3rd fields of CSV
awk -F',' '$3 > 100' file     # Print rows where 3rd field > 100
awk '{s+=$1} END {print s}'   # Sum all values in column 1
awk 'END {print NR}' file     # Count total rows in file
```

---
*Created as part of the Practical Linux Notes collection. Last updated: July 2026.*
