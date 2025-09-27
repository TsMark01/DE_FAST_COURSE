# Apache Airflow DAGs: A Mini Course for Beginners

## 📚 Introduction

Welcome to this mini course on **Apache Airflow DAGs** (Directed Acyclic Graphs), designed to teach you how to orchestrate data pipelines using Airflow. This course is perfect for beginners in data engineering who want to learn how to automate workflows, integrate APIs, manage databases, and create custom operators. Through five practical examples, you’ll build hands-on skills in creating robust ETL (Extract, Transform, Load) pipelines. This course is a portfolio piece by Mark Tsyrul, showcasing data engineering skills for academic and professional growth.

### 🎯 Learning Objectives
By the end of this course, you will:
- Understand the structure and purpose of Airflow DAGs.
- Use `BashOperator` and `PostgresOperator` to execute tasks.
- Create and apply custom sensors for conditional workflows.
- Integrate external APIs and PostgreSQL databases.
- Build dynamic pipelines using Airflow Variables.
- Develop portfolio-ready projects to demonstrate data engineering skills.

### 🛠️ Tech Stack
| Category          | Tools/Technologies       | Purpose |
|-------------------|--------------------------|---------|
| **Orchestration** | Apache Airflow          | Workflow management |
| **Operators**     | BashOperator, PostgresOperator | Task execution |
| **Custom Sensor** | BaseSensorOperator      | Conditional checks |
| **Database**      | PostgreSQL              | Data storage |
| **API**           | Requests                | External data fetching |
| **ORM**           | SQLAlchemy              | Database interaction |
| **Scripting**     | Python 3.9+             | Task logic |

### 📋 Prerequisites
- **Airflow**: Version 2.7.3+ with `[postgresql]` extra.
- **Python**: 3.9+.
- **PostgreSQL**: Configured with a database (e.g., `airflow_metadata`).
- **Dependencies**: `psycopg2-binary`, `requests`, `sqlalchemy`.
- **Airflow Connection**: `postgres_connection_main` (host, schema, login, password, port: 5432).

For setup, refer to the guide at [AIRFLOW INSTALLING](https://github.com/TsMark01/DeSql/blob/main/AIRFLOW_INSTALLING.markdown).

Install dependencies:
```bash
pip install apache-airflow[postgresql]==2.7.3 psycopg2-binary requests sqlalchemy
```

## 🏗️ Course Outline

This course consists of five modules, each with a practical DAG example, code, explanations, and exercises to reinforce learning. Place DAGs in `/airflow/dags/`, scripts in `/airflow/scripts/`, and plugins in `/airflow/plugins/`.

### Module 1: Basic DAG with BashOperator
**Objective**: Learn to create a simple DAG that executes Python scripts sequentially.

**What You’ll Learn**:
- Define a DAG with `BashOperator`.
- Set up task dependencies.
- Understand scheduling and execution.

**Example DAG** (`basic_dag.py`):
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

**Explanation**:
- **DAG Definition**: The DAG runs every minute (`schedule_interval='* * * * *'`).
- **Tasks**: `task1` and `task2` execute Python scripts in `/airflow/scripts/dag1/`.
- **Dependency**: `task1 >> task2` ensures `task2` runs after `task1`.

**Exercise**:
1. Create a dummy `task1.py` and `task2.py` in `/airflow/scripts/dag1/` (e.g., `print("Task 1")` and `print("Task 2")`).
2. Place the DAG in `/airflow/dags/` and trigger it via the Airflow UI (`http://localhost:8080`).
3. Check logs in `/airflow/logs` to verify execution order.
4. **Question**: How would you modify the schedule to run every hour?

### Module 2: Exchange Rate DAG with Arguments
**Objective**: Build a DAG that fetches USD/RUB exchange rates and stores them in PostgreSQL, using command-line arguments for flexibility.

**What You’ll Learn**:
- Integrate APIs with `requests`.
- Use SQLAlchemy for database operations.
- Pass dynamic parameters via `BashOperator`.

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

**Alternative Script** (`task1_alternative.py`):
```python
import datetime
import requests
from sqlalchemy import create_engine, Column, Integer, Float, TIMESTAMP
from sqlalchemy.orm import sessionmaker, declarative_base

Base = declarative_base()

SQLALCHEMY_DATABASE_URI = ""  # Must be set manually or via environment variables
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

**Explanation**:
- **API Integration**: Fetches USD/RUB rates from CBR API.
- **Database**: Stores rates in `usdtorub2` table using SQLAlchemy.
- **Arguments**: `task1.py` uses command-line arguments for flexibility, while `task1_alternative.py` requires manual URI configuration.
- **Security**: `BaseHook.get_connection` retrieves credentials securely.

**Exercise**:
1. Set up a PostgreSQL database with `usdtorub2` table.
2. Configure `postgres_connection_main` in Airflow UI.
3. Run the DAG and verify data in `usdtorub2` using `psql`.
4. **Question**: How would you modify `task1.py` to fetch EUR/RUB rates?

### Module 3: Sensor-Based DAG
**Objective**: Create a DAG with a custom sensor to wait for data in a PostgreSQL table before proceeding.

**What You’ll Learn**:
- Build a custom sensor with `BaseSensorOperator`.
- Use `psycopg2` and `pandas` for table checks.
- Implement conditional task execution.

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

**Explanation**:
- **Sensor**: `CheckTableSensor` checks if `op_table` has at least one row.
- **Configuration**: `mode='reschedule'` optimizes resource usage; `poke_interval=10` checks every 10 seconds.
- **Workflow**: `task1` populates data, sensor waits, then `task3` and subsequent tasks run.

**Exercise**:
1. Create `op_table` in PostgreSQL with sample data.
2. Place `check_table_sensor.py` in `/airflow/plugins/utils/`.
3. Run the DAG and observe the sensor’s behavior in the Airflow UI.
4. **Question**: How would you adjust `poke_interval` for a slower database?

### Module 4: Data Mart DAG
**Objective**: Build a DAG to manage a product data mart with SQL operations.

**What You’ll Learn**:
- Use `PostgresOperator` for database tasks.
- Perform table truncation and data insertion.
- Execute SQL joins for data aggregation.

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

**Explanation**:
- **Tasks**: Clears tables (`total_money`, `products2`, `c_order2`), inserts product data, and performs a join.
- **PostgresOperator**: Executes SQL directly, simplifying database operations.
- **Note**: Truncated tasks (`insert_c_order2`, `insert_total_money`) follow a similar SQL structure.

**Exercise**:
1. Create tables `products2`, `c_order2`, and `total_money` in PostgreSQL with appropriate schemas.
2. Run the DAG and query the joined results using `psql`.
3. **Question**: How would you add error handling for failed SQL queries?

### Module 5: Variable-Driven DAG
**Objective**: Create a DAG that uses Airflow Variables for dynamic task creation.

**What You’ll Learn**:
- Use `Variable.get` for dynamic configuration.
- Generate tasks based on JSON data.
- Process command-line arguments in scripts.

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

**Explanation**:
- **Variables**: `my_password` (string) and `json_variable` (e.g., `{"text": ["task_a", "task_b"]}`) drive task creation.
- **Dynamic Tasks**: Loops over `d_values["text"]` to create tasks.
- **Script**: `main_script.py` processes the `--variable` argument.

**Exercise**:
1. In Airflow UI, set `my_password` (e.g., `"secret"`) and `json_variable` (e.g., `{"text": ["task_a", "task_b"]}`).
2. Run the DAG and check logs for variable outputs.
3. **Question**: How would you modify the DAG to handle missing variables?

### Module 6: Custom Operator
**Objective**: Develop a custom operator for inserting data into PostgreSQL.

**What You’ll Learn**:
- Extend `BaseOperator` for custom logic.
- Use SQLAlchemy for database operations.
- Integrate with Airflow’s connection system.

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

**Explanation**:
- **Custom Operator**: Inserts data into `op_table` using SQLAlchemy.
- **Connection**: Uses `postgre_conn` for secure credential management.
- **Usage**: Can be integrated into a DAG for custom tasks.

**Exercise**:
1. Place `example_operator.py` in `/airflow/plugins/`.
2. Create a DAG using `ExampleOperator` to insert a sample record (e.g., `r_value=42.0`, `r_date=datetime.utcnow()`).
3. Verify data in `op_table` using `psql`.
4. **Question**: How would you extend `ExampleOperator` to update existing records?

## 🧪 Testing and Running

1. **Setup**:
   - Follow [airflow_installing.markdown](https://github.com/TsMark01/DeSql/blob/main/airflow_installing.markdown).
   - Place DAGs, scripts, and plugins in respective directories.

2. **Test DAGs**:
   - Access `http://localhost:8080`.
   - Enable and trigger DAGs.
   - Verify data in PostgreSQL tables (`usdtorub2`, `products2`, `op_table`).
   - Check logs in `/airflow/logs`.

## 🔮 Further Learning

- Add retry logic and error handling to DAGs.
- Explore CeleryExecutor for distributed tasks.
- Integrate with visualization tools like Power BI. ([EXAMPLE PROJECT](https://github.com/TsMark01/DE_api_airflow_project_pbi))
- Develop advanced sensors for complex conditions.

**Author**: Mark Tsyrul
