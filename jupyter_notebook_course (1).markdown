# Jupyter Notebook: A Mini Course for Data Engineering

## 📚 Introduction

Welcome to this beginner-friendly mini course on **Jupyter Notebook**, designed by Mark Tsyrul as a graduation project to teach interactive data analysis and automation for data engineering. Jupyter Notebook is a powerful tool for creating, testing, and visualizing data pipelines, making it ideal for prototyping ETL (Extract, Transform, Load) workflows. Through five original examples, you’ll learn to clean data, integrate APIs, query databases, create visualizations, and automate tasks. This course enhances your CS portfolio for university applications and complements tools like Apache Airflow and Power BI.

### 🎯 Learning Objectives
By the end of this course, you will:
- Set up and configure Jupyter Notebook for data engineering tasks.
- Perform data cleaning and transformation using `pandas`.
- Fetch data from APIs with `requests`.
- Query PostgreSQL databases using `sqlalchemy`.
- Create visualizations with `matplotlib`.
- Automate repetitive tasks with Python scripts.
- Build portfolio-ready Jupyter Notebooks for academic and professional showcases.

### 🛠️ Tech Stack
| Category          | Tools/Technologies       | Purpose |
|-------------------|--------------------------|---------|
| **Notebook**      | Jupyter Notebook        | Interactive computing |
| **Scripting**     | Python 3.9+             | Core logic |
| **Data Processing**| pandas                  | Data cleaning/transformation |
| **API**           | requests                | External data fetching |
| **Database**      | PostgreSQL, sqlalchemy  | Data storage and queries |
| **Visualization** | matplotlib              | Data plotting |
| **System**        | systemd (Linux)         | Daemon service |

### 📋 Prerequisites
- **Jupyter Notebook**: Installed and configured.
- **Python**: 3.9+ with `pip` and `venv`.
- **PostgreSQL**: Configured with a database (e.g., `data_engineering`).
- **Dependencies**: `notebook`, `pandas`, `requests`, `sqlalchemy`, `psycopg2-binary`, `matplotlib`.
- **Working Directory**: `/jupyter_notebook_files/` for `.ipynb` files.

For setup, refer to the guide at [JUPYTERNOTEBOOK_INSTALLING.markdown](https://github.com/TsMark01/DeSql/blob/main/JUPYTERNOTEBOOK_INSTALLING.markdown).

Install dependencies:
```bash
pip install notebook pandas requests sqlalchemy psycopg2-binary matplotlib
```

## 🏗️ Course Outline

This course includes five modules, each with a unique Jupyter Notebook example, code, explanations, and exercises. Save `.ipynb` files in `/jupyter_notebook_files/`.

### Module 1: Data Cleaning with Pandas
**Objective**: Learn to clean and preprocess datasets using `pandas` in Jupyter Notebook.

**What You’ll Learn**:
- Load and explore datasets.
- Handle missing values and data types.
- Save cleaned data to CSV.

**Example Notebook** (`data_cleaning.ipynb`):
```python
# Import libraries
import pandas as pd

# Load sample dataset (e.g., product sales)
data = {
    'product': ['Milk', 'Sugar', None, 'Rice', 'Apples'],
    'price': [1.0, 1.0, 2.0, None, 1.0],
    'sales': [324, 412, 943, 324, None]
}
df = pd.DataFrame(data)

# Display data
print("Original DataFrame:")
print(df)

# Handle missing values
df['product'].fillna('Unknown', inplace=True)
df['price'].fillna(df['price'].mean(), inplace=True)
df['sales'].fillna(df['sales'].median(), inplace=True)

# Convert data types
df['sales'] = df['sales'].astype(int)

# Save cleaned data
df.to_csv('/jupyter_notebook_files/cleaned_products.csv', index=False)
print("\nCleaned DataFrame:")
print(df)
```

**Explanation**:
- **Data Loading**: Creates a sample `pandas` DataFrame.
- **Cleaning**: Replaces missing values (`None`) with defaults or aggregates (mean, median).
- **Output**: Saves cleaned data to CSV for further use.

**Exercise**:
1. Create `data_cleaning.ipynb` with the code above.
2. Run the notebook in Jupyter (`http://localhost:8989`) and verify the output CSV.
3. Add a new column `category` (e.g., ‘Food’) and update the CSV.
4. **Question**: How would you handle outliers in the `sales` column?

### Module 2: API Integration for Exchange Rates
**Objective**: Fetch USD/RUB exchange rates from an API and process them in Jupyter.

**What You’ll Learn**:
- Use `requests` to fetch API data.
- Parse JSON responses.
- Store results in a DataFrame.

**Example Notebook** (`exchange_rate_api.ipynb`):
```python
import requests
import pandas as pd
from datetime import datetime

# Fetch USD/RUB rate
url = "https://www.cbr-xml-daily.ru/daily_json.js"
response = requests.get(url)

if response.status_code != 200:
    raise Exception("API Request Failed")

data = response.json()
usd_rub = data["Valute"]["USD"]["Value"]

# Create DataFrame
df = pd.DataFrame({
    'currency': ['USD/RUB'],
    'rate': [usd_rub],
    'timestamp': [datetime.utcnow()]
})

# Display and save
print("Exchange Rate Data:")
print(df)
df.to_csv('/jupyter_notebook_files/usd_rub_rates.csv', index=False)
```

**Explanation**:
- **API Call**: Fetches data from the CBR API.
- **Processing**: Extracts USD/RUB rate and stores it in a `pandas` DataFrame.
- **Output**: Saves data to CSV for analysis or pipeline integration.

**Exercise**:
1. Create `exchange_rate_api.ipynb` and run it.
2. Verify the CSV output in `/jupyter_notebook_files/`.
3. Modify the notebook to fetch EUR/RUB rates.
4. **Question**: How would you schedule this notebook to run daily?

### Module 3: Database Queries with SQLAlchemy
**Objective**: Query a PostgreSQL database using `sqlalchemy` in Jupyter.

**What You’ll Learn**:
- Connect to PostgreSQL.
- Execute SQL queries.
- Visualize query results.

**Example Notebook** (`database_query.ipynb`):
```python
from sqlalchemy import create_engine
import pandas as pd

# Database connection
SQLALCHEMY_DATABASE_URI = "postgresql://user:password@localhost:5432/data_engineering"
engine = create_engine(SQLALCHEMY_DATABASE_URI)

# Query table
query = "SELECT product_name, total_sales FROM products2 WHERE total_sales > 500"
df = pd.read_sql(query, engine)

# Display results
print("High Sales Products:")
print(df)

# Save to CSV
df.to_csv('/jupyter_notebook_files/high_sales_products.csv', index=False)
```

**Explanation**:
- **Connection**: Uses `sqlalchemy` to connect to a PostgreSQL database.
- **Query**: Retrieves products with sales > 500 from `products2` (assumes table from Airflow course).
- **Output**: Saves results to CSV for further analysis.

**Exercise**:
1. Create `database_query.ipynb` and update `SQLALCHEMY_DATABASE_URI` with your credentials.
2. Ensure `products2` exists in PostgreSQL (e.g., from Airflow’s data mart DAG).
3. Run the notebook and check the CSV.
4. **Question**: How would you modify the query to include `price`?

### Module 4: Data Visualization with Matplotlib
**Objective**: Create visualizations from processed data in Jupyter.

**What You’ll Learn**:
- Use `matplotlib` for plotting.
- Visualize sales data.
- Export plots for reports.

**Example Notebook** (`sales_visualization.ipynb`):
```python
import pandas as pd
import matplotlib.pyplot as plt

# Load data
df = pd.DataFrame({
    'product': ['Milk', 'Sugar', 'Rice', 'Apples'],
    'sales': [324, 412, 324, 733]
})

# Create bar plot
plt.figure(figsize=(8, 5))
plt.bar(df['product'], df['sales'], color='skyblue')
plt.title('Product Sales Analysis')
plt.xlabel('Product')
plt.ylabel('Total Sales')
plt.savefig('/jupyter_notebook_files/sales_plot.png')
plt.show()
```

**Explanation**:
- **Data**: Uses a sample sales dataset.
- **Visualization**: Creates a bar plot with `matplotlib`.
- **Output**: Saves the plot as a PNG for inclusion in reports.

**Exercise**:
1. Create `sales_visualization.ipynb` and run it.
2. Verify the PNG in `/jupyter_notebook_files/`.
3. Add a line plot for `price` vs. `sales`.
4. **Question**: How would you customize the plot colors?

### Module 5: Task Automation with Python
**Objective**: Automate data processing tasks in Jupyter for pipeline prototyping.

**What You’ll Learn**:
- Combine API, database, and visualization tasks.
- Automate repetitive processes.
- Export results for integration with Airflow or Power BI.

**Example Notebook** (`automated_pipeline.ipynb`):
```python
import requests
import pandas as pd
from sqlalchemy import create_engine
from datetime import datetime
import matplotlib.pyplot as plt

# Step 1: Fetch API data
url = "https://www.cbr-xml-daily.ru/daily_json.js"
response = requests.get(url)
if response.status_code != 200:
    raise Exception("API Request Failed")
data = response.json()
usd_rub = data["Valute"]["USD"]["Value"]

# Step 2: Store in PostgreSQL
SQLALCHEMY_DATABASE_URI = "postgresql://user:password@localhost:5432/data_engineering"
engine = create_engine(SQLALCHEMY_DATABASE_URI)
df = pd.DataFrame({
    'currency': ['USD/RUB'],
    'rate': [usd_rub],
    'timestamp': [datetime.utcnow()]
})
df.to_sql('exchange_rates', engine, if_exists='append', index=False)

# Step 3: Query and visualize
query = "SELECT rate, timestamp FROM exchange_rates ORDER BY timestamp DESC LIMIT 5"
df_rates = pd.read_sql(query, engine)
plt.figure(figsize=(8, 5))
plt.plot(df_rates['timestamp'], df_rates['rate'], marker='o', color='green')
plt.title('Recent USD/RUB Exchange Rates')
plt.xlabel('Timestamp')
plt.ylabel('Rate')
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig('/jupyter_notebook_files/exchange_rate_trend.png')
plt.show()
```

**Explanation**:
- **Pipeline**: Fetches USD/RUB rates, stores them in PostgreSQL, queries recent rates, and plots a trend.
- **Automation**: Combines multiple steps into a single notebook.
- **Output**: Saves data to database and plot to PNG.

**Exercise**:
1. Create `automated_pipeline.ipynb` and update database credentials.
2. Run the notebook and verify the database table and PNG.
3. Add error handling for API failures.
4. **Question**: How would you extend this to include EUR/RUB rates?

## 🧪 Testing and Running

1. **Setup**:
   - Follow [JUPYTERNOTEBOOK_INSTALLING.markdown](https://github.com/TsMark01/DeSql/blob/main/JUPYTERNOTEBOOK_INSTALLING.markdown).
   - Save `.ipynb` files in `/jupyter_notebook_files/`.

2. **Run Jupyter**:
   ```bash
   jupyter notebook --config ~/.jupyter/jupyter_notebook_config.py
   ```
   Access at `http://localhost:8989`.

3. **Test Notebooks**:
   - Open each notebook and execute all cells.
   - Verify CSV files, database entries, and PNG outputs in `/jupyter_notebook_files/`.
   - Check Jupyter logs for errors.

## 📝 Portfolio Tips

- **For UCAS**: Highlight this course in your personal statement: “I developed a Jupyter Notebook mini course (jupyter_notebook_course.markdown) for my graduation project, showcasing my ability to teach data engineering concepts like API integration, database queries, and visualization, essential for computer science.”
- **For IELTS**: Use examples for Speaking Part 2 (e.g., “Describe a project you completed” – discuss the automated pipeline) or Writing Task 1 (e.g., describe the visualization process). Practice terms like “data pipeline,” “interactive computing,” and “visualization” for your 6.0–6.5 target by November 2025.
- **GitHub**: Add this to your `DeSql` repo alongside your Power BI project ([DE_api_airflow_project_pbi](https://github.com/TsMark01/DE_api_airflow_project_pbi)) and Airflow course to demonstrate a complete data engineering skillset.

## 🔮 Further Learning

- Add error handling and logging to notebooks.
- Integrate with Airflow for scheduled execution.
- Use Power BI to visualize notebook outputs.
- Explore `seaborn` for advanced visualizations.

**Related Course**: Check out the complementary Apache Airflow mini course at [AIRFLOW_DAGS_COURSE.markdown](https://github.com/TsMark01/DeSql/blob/main/AIRFLOW_DAGS_COURSE.markdown) for workflow orchestration skills.

**Author**: Mark Tsyrul