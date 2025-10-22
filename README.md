# Project Result Generation and Publishing

This repository demonstrates a complete workflow for data processing, analysis, and result publishing using Python, Pandas, and GitHub Actions. It includes a Python script (`execute.py`) that processes an Excel file (`data.xlsx`), converts it to CSV (`data.csv`), performs calculations, and generates a JSON output (`result.json`). This output is then automatically published to GitHub Pages via a CI/CD pipeline, where it's displayed by a simple HTML web application (`index.html`).

## Project Structure

```
.github/
└── workflows/
    └── ci.yml             # GitHub Actions workflow for CI/CD
data.xlsx                # Original data in Excel format
data.csv                 # Converted data in CSV format (committed)
execute.py               # Python script for data processing
index.html               # Web application to display result.json
LICENSE                  # MIT License
README.md                # This README file
```

## Getting Started

### Prerequisites

Ensure you have the following installed:

*   Python 3.11+ (tested with Python 3.11)
*   `pip` (Python package installer)
*   `venv` (Python virtual environment module)

### Local Setup

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/your-username/your-repo-name.git
    cd your-repo-name
    ```

2.  **Create and activate a virtual environment:**

    ```bash
    python -m venv .venv
    # On Windows:
    .venv\Scripts\activate
    # On macOS/Linux:
    source .venv/bin/activate
    ```

3.  **Install dependencies:**

    ```bash
    pip install pandas==2.3.0 openpyxl ruff
    ```

### Data Conversion: `data.xlsx` to `data.csv`

The `execute.py` script expects `data.csv`. You need to convert `data.xlsx` to `data.csv` locally and commit it.

To do this, you can use a simple Python one-liner in your terminal after installing pandas and openpyxl:

```bash
python -c "import pandas as pd; df = pd.read_excel('data.xlsx'); df.to_csv('data.csv', index=False)"
```

After generating `data.csv`, ensure you commit it to your repository:

```bash
git add data.csv
git commit -m "Add data.csv converted from data.xlsx"
```

### Running `execute.py` Locally

To test the script locally and generate `result.json`:

```bash
python execute.py > result.json
```

This will create a `result.json` file in your project root containing the processed data.

## `execute.py` Script Details

The `execute.py` script reads `data.csv`, performs a simple aggregation, and outputs the result as a JSON string to standard output. Specifically, it groups data by a 'Category' column, sums a 'Value' column, and includes a timestamp of generation.

**Non-trivial Error Fix:**

*   **Original Issue:** The initial version of `execute.py` was susceptible to errors if the 'Value' column in `data.csv` contained non-numeric entries (e.g., text, empty strings). This would lead to a `TypeError` during aggregation attempts.
*   **Fix Implemented:** The script now robustly handles potential non-numeric values in the 'Value' column by converting it to a numeric type using `pd.to_numeric(..., errors='coerce')`. This replaces any non-convertible values with `NaN`, which are then gracefully handled during the summation, preventing script crashes and ensuring consistent output even with messy input data.

## GitHub Actions CI/CD Workflow (`.github/workflows/ci.yml`)

A GitHub Actions workflow is configured to automate the following steps on every push to the `main` branch:

1.  **Checkout Code:** Clones the repository.
2.  **Setup Python:** Configures Python 3.11 for the environment.
3.  **Install Dependencies:** Installs `ruff`, `pandas==2.3.0`, and `openpyxl`.
4.  **Run Ruff Linter:** Executes `ruff .` to check for code quality and style issues, displaying results in the CI log.
5.  **Execute Script:** Runs `python execute.py > result.json` to generate the `result.json` file.
6.  **Setup Pages & Upload Artifact:** Prepares the GitHub Pages environment and uploads the generated `result.json` along with `index.html` as artifacts.
7.  **Deploy to GitHub Pages:** Deploys the artifacts to GitHub Pages, making `result.json` and `index.html` publicly accessible.

This ensures that `result.json` is always up-to-date with the latest data processing logic and committed data, and the web application is continuously deployed.

## Viewing the Results

The `index.html` file acts as a simple web application to display the contents of the generated `result.json`. Once the GitHub Actions workflow completes successfully, your `result.json` and `index.html` will be deployed to GitHub Pages.

You can typically find your GitHub Pages site at:

`https://your-username.github.io/your-repo-name/`

Navigate to this URL in your browser to see the `result.json` content dynamically loaded and displayed by `index.html`.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
