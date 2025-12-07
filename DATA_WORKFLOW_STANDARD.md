# Standard Data Workflow Guide

This document establishes the standard for organizing data analysis projects within this codebase. By following this convention, you ensure your code is **portable**, **organized**, and **collaborator-friendly**.

## 1. The Philosophy

The core principle is **relative path independence**. Your notebook should never hardcode an absolute path (e.g., `/Users/username/...`). Instead, it should dynamically discover its location relative to the project root.

This allows the project to run on:

- Your local machine
- A colleague's machine
- A cloud server (like Colab or GitHub Codespaces)
...without changing a single line of code.

## 2. Directory Structure

We strictly separate **raw data**, **analysis code**, and **helper functions**.

```text
root/
├── da_data_repo/            <-- READ-ONLY: Raw cleaned data
│   └── [domain]/            <-- e.g., 'football', 'hotels'
│       └── clean/
│           └── data.csv
│
├── da_case_studies/         <-- WRITE: Your analysis
│   ├── ch00-tech-prep/      <-- SHARED: Helper functions
│   │   └── py_helper_functions.py
│   │
│   ├── ch##-[project-name]/ <-- YOUR PROJECT
│   │   ├── analysis.ipynb
│   │   └── output/          <-- Generated tables/charts
│   │
│   └── DATA_WORKFLOW_STANDARD.md
```

## 3. The Standard Header (Copy-Paste This)

Every notebook should start with this setup block. It anchors your paths dynamically.

```python
import os
import sys

# 1. Get the current working directory of the notebook
current_path = os.getcwd()

# 2. Find the project root (assuming we are inside 'da_case_studies')
#    Splitting allows us to go uplevels to the shared base directory.
dirname = current_path.split("da_case_studies")[0]

# 3. Define Standard Paths
#    data_in:  Where to read file from (The Data Repo)
#    data_out: Where to save intermediate files (The Project Folder)
#    output:   Where to save final results (The Output Subfolder)
#    func:     Where to find shared tools (The Tech Prep Folder)
data_in  = dirname + "da_data_repo/[domain]/clean/"   # <--- UPDATE [domain]!
data_out = dirname + "da_case_studies/[project]/"      # <--- UPDATE [project]!
output   = dirname + "da_case_studies/[project]/output/"
func     = dirname + "da_case_studies/ch00-tech-prep/"

# 4. Enable helper functions
sys.path.append(func)
import py_helper_functions as da
```

## 4. Path Definitions

| Variable | Description | Typical Use |
| :--- | :--- | :--- |
| `data_in` | Path to the *clean* data repository. | `df = pd.read_csv(data_in + "file.csv")` |
| `data_out`| Path to the current project folder. | `df.to_csv(data_out + "processed.csv")` |
| `output` | Path for artifacts (plots, tables). | `plt.savefig(output + "figure1.png")` |
| `func` | Path to shared python scripts. | `sys.path.append(func)` |

## 5. How to Adopt This Standard

For every new analysis tasks:

1. **Create a Folder**: Make a new folder in `da_case_studies`.
2. **Create an Output Folder**: Inside your new folder, create an empty `output` directory.
3. **Start Your Notebook**: Create a new `.ipynb`.
4. **Paste the Header**: Copy the block from section #3 above.
5. **Configure**: Change `[domain]` to the relevant folder in `da_data_repo` and `[project]` to your new folder name.

Now you are ready to work with a professional, robust standard!
