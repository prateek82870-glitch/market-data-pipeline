# Market Data Pipeline

An automated ETL (Extract, Transform, Load) pipeline that pulls live cryptocurrency market data from a public API, cleans it, stores it in SQL Server, runs SQL analysis, and generates a formatted Excel report.

## Architecture

```
CoinGecko API
     ↓
Python (requests)
     ↓
Data cleaning + validation (pandas)
     ↓
SQL Server (SQLAlchemy + pyodbc)
     ↓
SQL analysis
     ↓
Excel report (openpyxl)
     ↓
Logging (pipeline.log)
```

## What it does

1. **Extract**: Sends a GET request to the [CoinGecko API](https://www.coingecko.com/en/api) and receives market data for the top coins as JSON.
2. **Transform**: Converts the JSON into a pandas DataFrame, selects the useful columns, renames them, checks for missing values, and drops incomplete rows.
3. **Load**: Writes the cleaned data into a SQL Server table (`coin_prices`).
4. **Analyze**: Runs SQL queries against the table (top gainers and losers, average 24h change, top coins by market cap) and reads the results back into pandas.
5. **Report**: Exports the results to `crypto_report.xlsx` with a styled header row and auto-fitted column widths.
6. **Log**: Every stage is wrapped in `try/except` and writes timestamped success and error messages to `pipeline.log`.

## Tech stack

- **Python 3.13**
- **requests**: HTTP calls to the API
- **pandas**: data cleaning and transformation
- **SQLAlchemy + pyodbc**: connecting Python to SQL Server
- **SQL Server (Express)**: data storage and analysis
- **openpyxl**: Excel report formatting
- **logging**: run history and error tracking

## Setup

### Prerequisites

- Python 3.10 or newer
- Microsoft SQL Server (Express edition works) and SQL Server Management Studio (SSMS)
- [ODBC Driver 17 for SQL Server](https://learn.microsoft.com/en-us/sql/connect/odbc/download-odbc-driver-for-sql-server)

### Installation

```bash
git clone https://github.com/<your-username>/market-data-pipeline.git
cd market-data-pipeline
pip install -r requirements.txt
```

### Database

1. Open SSMS and connect to your SQL Server instance.
2. Create an empty database named `crypto_pipeline` (right-click **Databases**, then **New Database**).
3. In the notebook, set the `server` variable to your instance name (for example `localhost\\SQLEXPRESS`).

The connection uses Windows Authentication. If you use SQL login instead, change the connection string accordingly and keep your credentials out of the notebook (for example in a `.env` file).

## Usage

Open `Auto_data_pipeline.ipynb` in VS Code or Jupyter and run the cells from top to bottom. After a successful run you will have:

- A `coin_prices` table in the `crypto_pipeline` database
- A `crypto_report.xlsx` file in the project folder
- A `pipeline.log` file recording what happened

> **Tip:** Close `crypto_report.xlsx` in Excel before re-running, otherwise Windows will block the file write with a `PermissionError`.

## Project structure

```
market-data-pipeline/
├── Auto_data_pipeline.ipynb   # main pipeline notebook
├── requirements.txt           # Python dependencies
├── README.md                  # this file
└── .gitignore                 # files excluded from git
```

## Roadmap

- [x] MVP: API → pandas → SQL Server → SQL analysis → Excel report → logging
- [ ] Refactor into reusable functions and a `main()` entry point
- [ ] Move to `.py` modules and packages
- [ ] Schedule daily runs (Windows Task Scheduler or the `schedule` library)
- [ ] Append daily data instead of replacing the table, to build price history
- [ ] Wrap the pipeline in a class (OOP) and add type hints
- [ ] Data validation rules and API retries
- [ ] Richer Excel reports (charts, conditional formatting, currency formats)
- [ ] Email the report automatically

## What I learned

Working with REST APIs and JSON, cleaning data with pandas, connecting Python to a relational database, writing SQL for analysis, generating formatted Excel reports, and building error handling and logging into a data workflow.
