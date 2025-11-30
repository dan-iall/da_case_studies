# Data Analysis Walkthrough Manual

## 1. File Reading Guideline

This guideline explains how to load data into a pandas DataFrame, based on the methods used in the `ch02-hotels-data-prep` case study.

### Prerequisites
Ensure you have pandas imported in your notebook:
```python
import pandas as pd
import os
```

### Method 1: Reading from a Local File
This is the preferred method when working with data stored in your project directory. It involves constructing a dynamic path to ensure the code runs correctly regardless of where the project is located.

1. **Define your directory paths:**
   Set up variables for your input and output directories.
   ```python
   # Get current working directory
   current_path = os.getcwd()
   
   # Define base directory (assuming a specific project structure)
   dirname = current_path.split("da_case_studies")[0]
   
   # Define specific data directories
   data_in = dirname + "da_data_repo/hotels-vienna"
   data_in_clean = data_in + "/clean/"
   ```

2. **Load the CSV file:**
   Use `pd.read_csv` with the constructed path.
   ```python
   data = pd.read_csv(data_in_clean + "hotels-vienna.csv")
   ```

### Method 2: Reading from a URL
You can also load data directly from a web source. This is useful for sharing notebooks without requiring the user to download data files manually, though it requires an internet connection.

```python
# Load directly from a URL
data = pd.read_csv("https://osf.io/y6jvb/download")
```

### Best Practices
- **Path Management:** Always use relative paths or dynamic path construction (like Method 1) instead of hardcoding absolute paths (e.g., `C:/Users/Name/...`). This makes your code reproducible on other machines.
- **Separation of Concerns:** Keep your raw data and clean data in separate folders (e.g., `/raw/` and `/clean/`) to avoid accidental overwrites.
