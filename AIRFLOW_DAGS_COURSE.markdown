# Apache Airflow DAGs: A Mini Course with Practical Examples

## 📋 Overview

This mini course introduces **Apache Airflow DAGs** (Directed Acyclic Graphs) for orchestrating data pipelines. It covers creating and configuring DAGs using operators like `BashOperator`, `PostgresOperator`, and custom sensors, integrating with external APIs, and managing PostgreSQL databases. The examples demonstrate real-world use cases, such as fetching exchange rates, processing product data, and implementing conditional task execution with sensors. This course is designed for beginners in data engineering and enhances your CS portfolio for university applications.

### 🎯 Objectives
- Understand DAG structure and configuration.
- Implement `BashOperator` for script execution.
- Use `PostgresOperator` for database operations.
- Create and apply custom sensors for conditional workflows.
- Integrate APIs and databases for ETL pipelines.
- Provide practical, portfolio-ready examples.

## 🛠️ Tech Stack

| Category          | Tools/Technologies       | Purpose |
|-------------------|--------------------------|---------|
| **Orchestration** | Apache Airflow          | Workflow management |
| **Operators**     | BashOperator, PostgresOperator | Task execution |
| **Custom Sensor** | BaseSensorOperator      | Conditional checks |
| **Database**      | PostgreSQL              | Data storage |
| **API**           | Requests                | External data fetching |
| **ORM**           | SQLAlchemy              | Database interaction |
| **Scripting**     | Python 3.9+             | Task logic |

## 🏗️ Course Structure

This course includes five practical DAG examples:
1. **Basic DAG with BashOperator**: Executes sequential Python scripts.
2. **Exchange Rate DAG with Arguments**: Fetches USD/RUB rates and stores them in PostgreSQL.
3. **Sensor-Based DAG**: Uses a custom sensor to check table data.
4. **Data Mart DAG**: Manages product data with SQL operations.
5. **Variable-Driven DAG**: Uses Airflow Variables for dynamic task execution.

## 🚀 Setup Requirements

- **Airflow**: Version 2.7.3+ with `[postgresql]` extra.
- **Python**: 3.9+.
- **PostgreSQL**: Configured with a database (e.g., `airflow_metadata`).
- **Dependencies**: `psycopg2-binary`, `requests`, `sqlalchemy`.
- **Airflow Connection**: `postgres_connection_main` (host, schema, login, password, port: 5432).

For installation, refer to the comprehensive guide at [airflow_installing.markdown](https://github.com/TsMark01/DeSql/blob/main/AIRFLOW_INSTALLING.markdown).

Install dependencies:
```bash
pip install apache-airflow[postgresql]==2.7.3 psycopg2-binary requests sqlalchemy
```

Place scripts in `/airflow/scripts/`, DAGs in `/airflow/dags/`, and plugins in `/airflow/plugins/`.

## 📚 DAG Examples

### 1. Basic DAG with BashOperator
This DAG runs two Python scripts sequentially, demonstrating simple task orchestration.

```python
from datetime import datetime
from airflow.models import DAG
from airflow.operators.bash import BashOperator

default_args = {
    "owner": "etl_user",
    "depends_on_past": False,
    "start_date": datetime(2024, 9, 19),
}

dag = DAG('basic_dag', default_args=default_args, schedule_interval='* * * * *', catchup=True,
          max_active_tasks=3, max_active_runs=1, tags=["Test", "Basic"])

task1 = BashOperator(
    task_id='task1',
    bash_command='python3 /airflow/scripts/dag1/task1.py',
    dag=dag)

task2 = BashOperator(
    task_id='task2',
    bash_command='python3 /airflow/scripts/dag1/task2.py',
    dag=dag)

task1 >> task2
```

**Key Points**:
- `schedule_interval='* * * * *'`: Runs every minute.
- `BashOperator`: Executes scripts in `/airflow/scripts/dag1/`.
- `task1 >> task2`: Defines task dependency.

### 2. Exchange Rate DAG with Arguments
This DAG fetches the USD/RUB exchange rate from an API, stores it in PostgreSQL, and passes database connection details via command-line arguments.

**DAG File** (`exchange_rate_dag.py`):
```python
from datetime import datetime
from airflow.models import DAG
from airflow.operators.bash import BashOperator
from airflow.hooks.base_hook import BaseHook

connection = BaseHook.get_connection("postgres_connection_main")

default_args = {
    "owner": "etl_user",
    "depends_on_past": False,
    "start_date": datetime(2024, 9, 27),
}

dag = DAG('exchange_rate_dag', default_args=default_args, schedule_interval='0 * * * *', catchup=True,
          max_active_tasks=3, max_active_runs=1, tags=["Test", "Exchange Rate"])

task1 = BashOperator(
    task_id='task1',
    bash_command='python3 /airflow/scripts/dag2/task1.py --date {{ ds }} ' + 
                 f'--host {connection.host} --dbname {connection.schema} --user {connection.login} ' +
                 f'--jdbc_password {connection.password} --port 5432',
    dag=dag)

task2 = BashOperator(
    task_id='task2',
    bash_command='python3 /airflow/scripts/dag2/task2.py',
    dag=dag)

task1 >> task2
```

**Script File** (`task1.py`):
```python
import datetime
import requests
from sqlalchemy import create_engine, Column, Integer, Float, TIMESTAMP
from sqlalchemy.orm import sessionmaker, declarative_base
import argparse

Base = declarative_base()

parser = argparse.ArgumentParser()
parser.add_argument("--date", dest="date")
parser.add_argument("--host", dest="host")
parser.add_argument("--dbname", dest="dbname")
parser.add_argument("--user", dest="user")
parser.add_argument("--jdbc_password", dest="jdbc_password")
parser.add_argument("--port", dest="port")
args = parser.parse_args()

SQLALCHEMY_DATABASE_URI = f"postgresql://{args.user}:{args.jdbc_password}@{args.host}:{args.port}/{args.dbname}"
engine = create_engine(SQLALCHEMY_DATABASE_URI)
Base.metadata.create_all(bind=engine)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
session_local = SessionLocal()

class Record(Base):
    __tablename__ = 'usdtorub2'
    id = Column(Integer, nullable=False, unique=True, primary_key=True, autoincrement=True)
    r_value = Column(Float, nullable=False)
    r_date = Column(TIMESTAMP, nullable=False, index=True)

def get_dollar_rub_rate():
    url = "https://www.cbr-xml-daily.ru/daily_json.js"
    response = requests.get(url)
    if response.status_code != 200:
        raise Exception("Request Error")
    data = response.json()
    return data["Valute"]["USD"]["Value"]

def new_record(val):
    record = Record(r_value=val, r_date=datetime.datetime.utcnow())
    session_local.add(record)
    session_local.commit()

value = get_dollar_rub_rate()
new_record(value)
```

**Key Points**:
- Fetches USD/RUB rate from CBR API.
- Uses SQLAlchemy to store data in `usdtorub2` table.
- `BaseHook.get_connection` retrieves database credentials securely.
- Command-line arguments pass connection details dynamically.

### 3. Sensor-Based DAG
This DAG uses a custom sensor to check for data in a PostgreSQL table before executing subsequent tasks.

**Sensor File** (`check_table_sensor.py`):
```python
from airflow.sensors.base import BaseSensorOperator
import psycopg2 as pg
import pandas.io.sql as psql

class CheckTableSensor(BaseSensorOperator):
    poke_context_fields = ['conn', 'table_name']
    
    def __init__(self, conn, table_name, *args, **kwargs):
        self.conn = conn
        self.table_name = table_name
        super(CheckTableSensor, self).__init__(*args, **kwargs)
    
    def poke(self, context):
        connection = pg.connect(
            f"host={self.conn.host} dbname={self.conn.schema} user={self.conn.login} password={self.conn.password}")
        df_currency = psql.read_sql(f'SELECT * FROM {self.table_name} LIMIT 1', connection)
        return len(df_currency) > 0
```

**DAG File** (`sensor_dag.py`):
```python
from datetime import datetime
from airflow.models import DAG
from airflow.hooks.base_hook import BaseHook
from airflow.operators.bash import BashOperator
from utils.check_table_sensor import CheckTableSensor

connection = BaseHook.get_connection("postgres_connection_main")

default_args = {
    "owner": "etl_user",
    "depends_on_past": False,
    "start_date": datetime(2024, 9, 28),
}

dag = DAG('sensor_dag', default_args=default_args, schedule_interval='0 * * * *', catchup=True,
          max_active_tasks=3, max_active_runs=1, tags=["Test"])

task1 = BashOperator(
    task_id='task1',
    bash_command='python3 /airflow/scripts/dag5/task1.py',
    dag=dag)

task_check_table_sensor = CheckTableSensor(
    task_id='task_check_table_sensor',
    timeout=1000,
    mode='reschedule',
    poke_interval=10,
    conn=connection,
    table_name='op_table',
    dag=dag)

task3 = BashOperator(
    task_id='task3',
    bash_command='python3 /airflow/scripts/dag_sensor/task3.py',
    dag=dag)

for i in [1, 2, 3, 4, 5]:
    some_task = BashOperator(
        task_id=f'task4_{i}',
        bash_command='python3 /airflow/scripts/dag_sensor/task1.py',
        dag=dag)
    task3 >> some_task

task1 >> task_check_table_sensor >> task3
```

**Key Points**:
- `CheckTableSensor`: Checks if `op_table` has data using `psycopg2` and `pandas`.
- `mode='reschedule'`: Optimizes resource usage between checks.
- Dependencies ensure tasks wait for table data.

### 4. Data Mart DAG
This DAG manages a product data mart by clearing tables, inserting data, and performing SQL joins.

**DAG File** (`data_mart_dag.py`):
```python
from datetime import datetime
from airflow.models import DAG
from airflow.providers.postgres.operators.postgres import PostgresOperator

default_args = {
    "owner": "etl_user",
    "depends_on_past": False,
    "start_date": datetime(2024, 9, 29),
}

dag = DAG('data_mart_dag', default_args=default_args, schedule_interval='0 * * * *', catchup=True,
          max_active_tasks=3, max_active_runs=1, tags=["data marts", "dm_orders"])

clear_day1 = PostgresOperator(
    task_id='clear_day1',
    postgres_conn_id='postgres_connection_main',
    sql="TRUNCATE TABLE total_money;",
    dag=dag)

clear_day2 = PostgresOperator(
    task_id='clear_day2',
    postgres_conn_id='postgres_connection_main',
    sql="TRUNCATE TABLE products2;",
    dag=dag)

clear_day3 = PostgresOperator(
    task_id='clear_day3',
    postgres_conn_id='postgres_connection_main',
    sql="TRUNCATE TABLE c_order2;",
    dag=dag)

insert_products = PostgresOperator(
    task_id='insert_products',
    postgres_conn_id='postgres_connection_main',
    sql="""INSERT INTO products2(product_name, month_of_use, price, total_sales)
           SELECT 'Milk' as product_name, 1 as month_of_use, 1 as price, 324 as total_sales
           UNION ALL
           SELECT 'Dark Chocolate' as product_name, 2 as month_of_use, 2 as price, 943 as total_sales
           UNION ALL
           SELECT 'Sugar' as product_name, 24 as month_of_use, 1 as price, 412 as total_sales
           UNION ALL
           SELECT 'Oats' as product_name, 12 as month_of_use, 1 as price, 945 as total_sales
           UNION ALL
           SELECT 'Rice' as product_name, 12 as month_of_use, 1 as price, 324 as total_sales
           UNION ALL
           SELECT 'Salt' as product_name, 99999 as month_of_use, 1 as price, 1233 as total_sales
           UNION ALL
           SELECT 'Apples' as product_name, 1 as month_of_use, 1 as price, 733 as total_sales;""",
    dag=dag)

select_join_tables = PostgresOperator(
    task_id='select_join_tables',
    postgres_conn_id='postgres_connection_main',
    sql="""SELECT C.h_name, C.date_of_order, P.product_name, P.month_of_use, P.price, P.total_sales, T.total_money 
           FROM c_order2 C
           LEFT JOIN products2 P ON C.code_of_product = P.code
           LEFT JOIN total_money T ON T.product_code = P.code;""",
    dag=dag)

clear_day1 >> clear_day2 >> clear_day3 >> insert_products >> select_join_tables
```

**Key Points**:
- `PostgresOperator`: Executes SQL queries for table management and joins.
- Clears tables (`TRUNCATE`) and inserts sample product data.
- Note: `insert_c_order2` and `insert_total_money` tasks were truncated but follow a similar SQL-based structure.

### 5. Variable-Driven DAG
This DAG uses Airflow Variables to dynamically execute tasks based on stored values.

**DAG File** (`variable_dag.py`):
```python
from datetime import datetime
from airflow.models import DAG
from airflow.operators.bash import BashOperator
from airflow.models import Variable

default_args = {
    "owner": "etl_user",
    "depends_on_past": False,
    "start_date": datetime(2024, 3, 12),
}

dag = DAG('variable_dag', default_args=default_args, schedule_interval=None, catchup=True,
          max_active_tasks=3, max_active_runs=1, tags=["Test", "Variables"])

v_password = Variable.get("my_password")
d_values = Variable.get("json_variable", deserialize_json=True)

task1 = BashOperator(
    task_id='task1',
    bash_command='python3 /airflow/scripts/lastdag/main_script.py --variable ' + v_password,
    dag=dag)

for one_value in d_values.get("text"):
    some_task = BashOperator(
        task_id=one_value,
        bash_command='python3 /airflow/scripts/lastdag/main_script.py --variable ' + one_value,
        dag=dag)
    task1 >> some_task
```

**Script File** (`main_script.py`):
```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--variable", dest="variable")
args = parser.parse_args()

print('variable = ' + str(args.variable))
```

**Key Points**:
- Uses `Variable.get` to fetch `my_password` and `json_variable` (JSON data).
- Dynamically creates tasks based on `d_values["text"]`.
- `main_script.py` processes command-line arguments for flexibility.

### 6. Custom Operator Example
This example demonstrates a custom operator for inserting data into PostgreSQL.

**Plugin File** (`example_operator.py`):
```python
from airflow.models.baseoperator import BaseOperator
from sqlalchemy import create_engine, Column, Integer, Float, TIMESTAMP
from sqlalchemy.orm import sessionmaker, declarative_base

Base = declarative_base()

class ForAirflowPlugin(Base):
    __tablename__ = 'op_table'
    id = Column(Integer, nullable=False, unique=True, primary_key=True, autoincrement=True)
    r_value = Column(Float, nullable=False)
    r_date = Column(TIMESTAMP, nullable=False, index=True)

class ExampleOperator(BaseOperator):
    def __init__(self, postgre_conn, r_value, r_date, **kwargs):
        super().__init__(**kwargs)
        self.postgre_conn = postgre_conn
        self.r_value = r_value
        self.r_date = r_date
        self.SQLALCHEMY_DATABASE_URI = f"postgresql://{postgre_conn.login}:{postgre_conn.password}@{postgre_conn.host}:{str(postgre_conn.port)}/{postgre_conn.schema}"

    def execute(self, context):
        engine = create_engine(self.SQLALCHEMY_DATABASE_URI)
        Base.metadata.create_all(bind=engine)
        SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
        session_local = SessionLocal()
        new_record = ForAirflowPlugin(r_value=self.r_value, r_date=self.r_date)
        session_local.add(new_record)
        session_local.commit()
```

**Key Points**:
- Extends `BaseOperator` for custom database operations.
- Uses SQLAlchemy to insert data into `op_table`.
- Integrates with Airflow’s connection system for secure credential handling.

## 🧪 Testing and Running

1. **Place Files**:
   - DAGs: `/airflow/dags/` (e.g., `basic_dag.py`, `exchange_rate_dag.py`).
   - Scripts: `/airflow/scripts/` (e.g., `task1.py`, `main_script.py`).
   - Plugins: `/airflow/plugins/` (e.g., `check_table_sensor.py`, `example_operator.py`).

2. **Configure Variables** (for `variable_dag`):
   - In Airflow UI, set `my_password` (string) and `json_variable` (JSON, e.g., `{"text": ["task_a", "task_b"]}`).

3. **Start Airflow**:
   ```bash
   airflow scheduler &
   airflow webserver &
   ```

4. **Trigger DAGs**:
   - Access `http://localhost:8080`.
   - Enable and trigger DAGs via the UI.

5. **Verify**:
   - Check PostgreSQL tables (`usdtorub2`, `products2`, `op_table`) for data.
   - Monitor logs in `/airflow/logs`.

## 📝 Portfolio Tips

- **For UCAS**: Highlight this course in your personal statement: “I developed a comprehensive mini course on Apache Airflow DAGs (airflow_dags_course.markdown), demonstrating my ability to design, implement, and document ETL pipelines, a core skill in data engineering.”
- **For IELTS**: Use examples for Speaking Part 2 (e.g., “Describe a technical project” – discuss the exchange rate or data mart DAG) or Writing Task 2 (e.g., “Explain the importance of automation in data processing”). Practice terms like “DAG orchestration,” “custom sensor,” and “ETL pipeline.”
- **GitHub**: Add this to your `DeSql` repo alongside `airflow_installing.markdown` to showcase both setup and usage skills. Consider adding a Jupyter Notebook (`.ipynb`) with data analysis to complement this course.

## 🔮 Further Learning

- Add retry logic and error handling to DAGs.
- Explore CeleryExecutor for distributed task execution.
- Integrate with visualization tools like Power BI.
- Expand sensor functionality for more complex conditions.

**Author**: Mark Tsyrul
