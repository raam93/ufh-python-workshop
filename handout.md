# Python Automation — Quick Reference
**Automate Your Boring Stuff · UFH · April 7, 2025**

---

## Building Blocks

| Concept | Example |
|---|---|
| Variable | `folder = "downloads"` |
| Constant | `MAX_SIZE = 10` (ALL_CAPS = do not change) |
| None | `x = None` — means "no value" |
| f-string | `f"Found {n} files"` — embed values in text |
| Loop | `for f in os.listdir(folder):` |
| Condition | `if filename.endswith(".pdf"):` |
| Try/except | `try: ... except FileNotFoundError: ...` |
| Function | `def merge_pdfs(files, output="merged.pdf"):` |
| Class | `class DataAnalyst:` |

---

## Part 1 · Files and Folders

`os` and `shutil` come built into Python — nothing to install.

```python
import os, shutil

os.listdir("folder")                       # list all files
os.makedirs("new_folder", exist_ok=True)   # create a folder
os.rename("old.txt", "new.txt")            # rename a file
os.remove("file.txt")                      # delete a file
os.path.isdir("path")                      # True if it's a folder

shutil.copy("file.txt", "backup.txt")      # copy a file
shutil.move("file.txt", "folder/file.txt") # move a file
```

**Get a file's extension:**
```python
ext = "." + filename.rsplit(".", 1)[-1].lower()   # → ".pdf"
```

**Loop through files, skip subfolders:**
```python
for filename in os.listdir("my_folder"):
    if os.path.isdir(os.path.join("my_folder", filename)):
        continue
    # do something with filename
```

**Common string checks on filenames:**
```python
filename.lower().startswith("invoice")   # starts with?
filename.lower().endswith(".pdf")        # ends with?
"2024" in filename.lower()              # contains?
```

---

## Part 2 · PDF Toolkit

Install: `!pip install pypdf`

```python
from pypdf import PdfReader, PdfWriter

# Merge PDFs
writer = PdfWriter()
writer.append("file1.pdf")
writer.append("file2.pdf")
with open("merged.pdf", "wb") as f:
    writer.write(f)

# Extract a page range (humans count from 1, Python from 0)
reader = PdfReader("document.pdf")
print(len(reader.pages))              # count pages

writer = PdfWriter()
for i in range(start_page - 1, end_page):
    writer.add_page(reader.pages[i])
with open("output.pdf", "wb") as f:
    writer.write(f)

# Split into individual pages
for i, page in enumerate(reader.pages, start=1):
    writer = PdfWriter()
    writer.add_page(page)
    with open(f"page_{str(i).zfill(3)}.pdf", "wb") as f:
        writer.write(f)
```

---

## Part 3 · Data with pandas

Install: `!pip install pandas openpyxl matplotlib seaborn`

```python
import pandas as pd

# Load
df = pd.read_csv("file.csv")
df = pd.read_excel("file.xlsx")

# Explore
df.shape          # (rows, columns)
df.columns        # column names
df.info()         # types + non-null counts
df.describe()     # numeric stats
df.head()         # first 5 rows
df.isnull().sum() # missing values per column
df.duplicated().sum()  # duplicate row count
```

**Clean:**
```python
# Standardise text columns
df["region"] = df["region"].str.strip().str.lower().str.capitalize()

# Parse dates (handles mixed formats)
df["date"] = pd.to_datetime(df["date"], errors="coerce")

# Fill missing values with median
df["units"] = df["units"].fillna(df["units"].median())

# Drop duplicates
df = df.drop_duplicates()

# Compute a new column
df["revenue"] = (df["units"] * df["unit_price"]).round(2)
```

**Filter:**
```python
df[df["revenue"] > 5000]                            # single condition
df[(df["region"] == "North") & (df["units"] > 100)] # AND — use &
df[(df["region"] == "North") | (df["region"] == "South")] # OR — use |
```

**Summarise:**
```python
df["revenue"].sum()    # total
df["revenue"].mean()   # average
df["revenue"].max()    # maximum

# Group by a column
df.groupby("region")["revenue"].sum()

# Multiple aggregations
df.groupby("product").agg(
    total  = ("revenue", "sum"),
    average= ("revenue", "mean"),
    count  = ("revenue", "count"),
)
```

**Combine multiple files:**
```python
import os
frames = []
for filename in os.listdir("monthly_sales"):
    d = pd.read_excel(os.path.join("monthly_sales", filename))
    frames.append(d)
combined = pd.concat(frames, ignore_index=True)
```

**Save:**
```python
df.to_csv("output.csv",   index=False)
df.to_excel("output.xlsx", index=False)
```

**Charts:**
```python
import matplotlib.pyplot as plt
import seaborn as sns

# Bar chart
df.groupby("region")["revenue"].sum().plot(kind="bar")
plt.title("Revenue by Region")
plt.tight_layout()
plt.savefig("chart.png", dpi=150)
plt.show()

# Distribution
sns.histplot(df["revenue"], bins=25, kde=True)
plt.show()
```

---

## Common Error Messages

| Error | Meaning | Fix |
|---|---|---|
| `ModuleNotFoundError` | Library not installed | `!pip install <name>` |
| `FileNotFoundError` | Wrong filename or path | Check spelling exactly — Python is case-sensitive |
| `IndentationError` | Bad spacing inside a block | Use 4 spaces consistently |
| `KeyError` | Column or key does not exist | Print `df.columns` to check names |
| `ValueError` | Wrong type or format | Check the data — often a non-numeric value in a number column |
| `AttributeError` | Method does not exist on that type | Check `type(x)` — you may have a string where you expect a DataFrame |

---

## Installing Python Locally

Guide: **realpython.com/installing-python**

```bash
pip install jupyter pandas openpyxl pypdf matplotlib seaborn reportlab
jupyter notebook    # launches in your browser — same as Colab
```

Use **VS Code** (free) for a better editing experience.

---

## Keep Learning

| | |
|---|---|
| 📖 Free Python book | automatetheboringstuff.com |
| 📊 pandas docs | pandas.pydata.org/docs |
| 🎓 Python for Everybody | coursera.org/learn/python |
| 💬 Community Q&A | reddit.com/r/learnpython |
| 🤖 Stuck on an error? | Paste it into Claude or ChatGPT |

---
*Session notebook → colab link on the workshop page*
