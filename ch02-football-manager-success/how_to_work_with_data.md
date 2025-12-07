# How to Work with Data in `ch02-football-manager-success`

This case study is part of a larger repository structure. The notebook `ch02-football-manager-success.ipynb` relies on a specific folder hierarchy to locate data and helper functions.

## Directory Structure

 The script automatically determines its location and sets up paths relative to the `da_case_studies` directory.

- **`current_path`**: The folder where the notebook resides.
- **`dirname`**: The root directory containing both `da_case_studies` and `da_data_repo`.

## Data Locations

### Input Data (`data_in`)

- **Path**: `[root]/da_data_repo/football/clean/`
- **Purpose**: Contains the cleaned football datasets (e.g., `epl_games.csv`) used for analysis.

### Output Data (`data_out` & `output`)

- **Path**: `[root]/da_case_studies/ch02-football-manager-success/`
- **Output Subfolder**: `[root]/da_case_studies/ch02-football-manager-success/output/`
- **Purpose**: Where the notebook saves processed results, charts, or tables.

## Helper Functions (`func`)

- **Path**: `[root]/da_case_studies/ch00-tech-prep/`
- **Purpose**: Contains shared utility scripts.
- **Usage**: The notebook appends this path to `sys.path` to import `py_helper_functions`.

## Setup

Ensure your project directory looks like this:

```text
project_root/
├── da_case_studies/
│   ├── ch00-tech-prep/
│   │   └── py_helper_functions.py
│   └── ch02-football-manager-success/
│       └── ch02-football-manager-success.ipynb
└── da_data_repo/
    └── football/
        └── clean/
            └── [data_files]
```
