# SQLAlchemy: A Mini Course for Database Management

## 📚 Introduction

This concise mini course, created by **Mark Tsyrul**, introduces **SQLAlchemy**, a Python ORM for efficient database management in data engineering. Through three Jupyter Notebook examples, you’ll learn to set up databases, perform CRUD operations, and execute joins. This course is ideal for beginners and complements Airflow and Jupyter workflows in your CS portfolio.

### 🎯 Learning Objectives
- Configure SQLAlchemy with PostgreSQL.
- Perform CRUD (Create, Read, Update, Delete) operations.
- Execute SQL joins for data aggregation.
- Apply SQLAlchemy in ETL pipelines.

### 🛠️ Tech Stack
| Category          | Tools/Technologies       | Purpose |
|-------------------|--------------------------|---------|
| **ORM**           | SQLAlchemy 2.0+         | Database interaction |
| **Notebook**      | Jupyter Notebook        | Interactive coding |
| **Database**      | PostgreSQL 12+          | Data storage |
| **Scripting**     | Python 3.9+             | Core logic |
| **Dependencies**  | psycopg2-binary, pandas | Drivers and data display |

### 📋 Prerequisites
- **SQLAlchemy**: Version 2.0+.
- **Python**: 3.9+.
- **PostgreSQL**: 12+ with a database (e.g., `data_engineering`).
- **Dependencies**: `sqlalchemy`, `psycopg2-binary`, `pandas`, `notebook`.

For Jupyter setup, see [JUPYTERNOTEBOOK_INSTALLING.markdown](https://github.com/TsMark01/DeSql/blob/main/JUPYTERNOTEBOOK_INSTALLING.markdown). For PostgreSQL setup, see [postgres_installing.markdown](https://github.com/TsMark01/DeSql/blob/main/postgres_installing.markdown).

Install dependencies:
```bash
pip install sqlalchemy psycopg2-binary pandas notebook
```

## 🏗️ Course Outline

This course includes three modules with Jupyter Notebook examples, saved in `/jupyter_notebook_files/`.

### Module 1: Database Setup and CRUD
**Objective**: Set up a database and perform CRUD operations.

**Example Notebook** (`sqlalchemy_crud.ipynb`):
```python
from sqlalchemy import create_engine, Column, Integer, String, Float
from sqlalchemy.orm import declarative_base, sessionmaker
import pandas as pd

Base = declarative_base()

# Define Product model
class Product(Base):
    __tablename__ = 'products'
    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String, nullable=False)
    price = Column(Float, nullable=False)

# Connect and create session
engine = create_engine("postgresql://myuser:mypassword@localhost:5432/data_engineering")
Base.metadata.create_all(engine)
session = sessionmaker(bind=engine)()

# Create
session.add(Product(name="Milk", price=1.5))
session.commit()

# Read
products = session.query(Product).all()
df = pd.DataFrame([(p.id, p.name, p.price) for p in products], columns=['id', 'name', 'price'])
print("Products:", df)

# Update
product = session.query(Product).filter_by(name="Milk").first()
product.price = 1.8
session.commit()

# Delete
session.query(Product).filter_by(name="Milk").delete()
session.commit()
```

**Use Case**: Managing product inventories in ETL pipelines.
**Explanation**: Creates a `products` table, adds a record, queries it, updates the price, and deletes it, using `pandas` for display.

**Exercise**:
1. Create `sqlalchemy_crud.ipynb` and run it.
2. Verify changes in `products` with `psql`.
3. Add a `quantity` column to the `Product` model.
4. **Question**: How would you query products with `price > 1.5`?

### Module 2: SQL Joins for Data Analysis
**Objective**: Perform SQL joins to aggregate data.

**Example Notebook** (`sqlalchemy_joins.ipynb`):
```python
from sqlalchemy import create_engine, Column, Integer, String, Float, ForeignKey
from sqlalchemy.orm import declarative_base, sessionmaker, relationship
import pandas as pd

Base = declarative_base()

# Define models
class Order(Base):
    __tablename__ = 'orders'
    id = Column(Integer, primary_key=True)
    product_id = Column(Integer, ForeignKey('products.id'))
    quantity = Column(Integer)
    product = relationship("Product")

class Product(Base):
    __tablename__ = 'products'
    id = Column(Integer, primary_key=True)
    name = Column(String)
    price = Column(Float)

# Connect and create session
engine = create_engine("postgresql://myuser:mypassword@localhost:5432/data_engineering")
Base.metadata.create_all(engine)
session = sessionmaker(bind=engine)()

# Insert sample data
session.add_all([Product(name="Sugar", price=1.0), Product(name="Rice", price=1.2)])
session.add(Order(product_id=1, quantity=10))
session.commit()

# Join query
query = session.query(Product.name, Product.price, Order.quantity).join(Order)
df = pd.read_sql(query.statement, engine)
print("Joined Data:", df)
```

**Use Case**: Aggregating sales data for reporting.
**Explanation**: Defines `products` and `orders` tables, inserts data, and performs a join to combine product and order details.

**Exercise**:
1. Create `sqlalchemy_joins.ipynb` and run it.
2. Verify the join results in `psql`.
3. Add a `date` column to `Order` and filter by recent orders.
4. **Question**: How would you perform a LEFT JOIN?

### Module 3: Bulk Inserts and Error Handling
**Objective**: Implement bulk inserts and handle database errors.

**Example Notebook** (`sqlalchemy_bulk_error.ipynb`):
```python
from sqlalchemy import create_engine, Column, Integer, String, Float
from sqlalchemy.orm import declarative_base, sessionmaker
import pandas as pd

Base = declarative_base()

class ExchangeRate(Base):
    __tablename__ = 'exchange_rates'
    id = Column(Integer, primary_key=True)
    currency = Column(String)
    rate = Column(Float)

# Connect and create session
engine = create_engine("postgresql://myuser:mypassword@localhost:5432/data_engineering")
Base.metadata.create_all(engine)
session = sessionmaker(bind=engine)()

# Bulk insert
try:
    rates = [
        ExchangeRate(currency="USD/RUB", rate=92.5),
        ExchangeRate(currency="EUR/RUB", rate=100.0)
    ]
    session.add_all(rates)
    session.commit()
except Exception as e:
    session.rollback()
    print(f"Error: {e}")

# Query and display
df = pd.read_sql("SELECT * FROM exchange_rates", engine)
print("Exchange Rates:", df)
```

**Use Case**: Storing large datasets from APIs in ETL pipelines.
**Explanation**: Performs a bulk insert of exchange rates and includes error handling with rollback.

**Exercise**:
1. Create `sqlalchemy_bulk_error.ipynb` and run it.
2. Verify data in `exchange_rates` with `psql`.
3. Add error logging to a file.
4. **Question**: How would you handle duplicate entries?

## 🧪 Testing and Running
1. **Setup**:
   - Follow [postgres_installing.markdown](https://github.com/TsMark01/DeSql/blob/main/postgres_installing.markdown) for PostgreSQL.
   - Follow [JUPYTERNOTEBOOK_INSTALLING.markdown](https://github.com/TsMark01/DeSql/blob/main/JUPYTERNOTEBOOK_INSTALLING.markdown) for Jupyter.
2. **Run Jupyter**:
   ```bash
   jupyter notebook
   ```
   Access `http://localhost:8888`.
3. **Test Notebooks**:
   - Execute each notebook’s cells.
   - Verify database tables and outputs with `psql`.

## 📝 Portfolio Tips
- **For UCAS**: Highlight this course: “I developed a SQLAlchemy mini course (sqlalchemy_course.markdown) for my graduation project, showcasing database management skills critical for data engineering.”
- **For IELTS**: Use examples for Speaking Part 2 (e.g., “Describe a technical project” – discuss the joins notebook) or Writing Task 1 (e.g., describe the CRUD process). Practice terms like “ORM,” “database joins,” and “bulk inserts.”
- **GitHub**: Add to your `DeSql` repo to complement your Airflow and Jupyter courses.

## 🔮 Further Learning
- Integrate SQLAlchemy with Airflow for ETL pipelines.
- Explore advanced querying with SQLAlchemy’s `text()` for raw SQL.
- Combine with Power BI for visualizations.

**Related Courses**:
- [Airflow DAGs Mini Course](https://github.com/TsMark01/DeSql/blob/main/AIRFLOW_DAGS_COURSE.markdown)
- [Jupyter Notebook Mini Course](https://github.com/TsMark01/DeSql/blob/main/jupyter_notebook_course.markdown)

**Author**: Mark Tsyrul
