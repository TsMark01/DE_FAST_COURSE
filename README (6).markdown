# 🚀 DE Fast Course: Data Engineering Essentials

[![Python 3.9+](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://www.python.org/downloads/)
[![Airflow](https://img.shields.io/badge/Apache%20Airflow-2.7%2B-orange.svg)](https://airflow.apache.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter%20Notebook-green.svg)](https://jupyter.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-12%2B-purple.svg)](https://www.postgresql.org/)
[![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-2.0%2B-red.svg)](https://www.sqlalchemy.org/)

## 📖 Welcome to DE Fast Course! 🎓

This repository is your **one-stop guide** to **Data Engineering fundamentals**, created by **Mark Tsyrul** as a graduation project. Whether you're a beginner exploring ETL pipelines or an intermediate learner building workflows, this course covers essential tools like **PostgreSQL**, **SQLAlchemy**, **Apache Airflow**, and **Jupyter Notebook** through hands-on mini-courses and examples.

Perfect for **CS students** looking to master data engineering skills. Dive in and start building like a pro! 💻

### 🎯 What You'll Learn
- **ETL Pipeline Basics**: Extract, transform, and load data efficiently.
- **Database Management**: Store and query data with PostgreSQL and SQLAlchemy.
- **Workflow Orchestration**: Schedule and monitor tasks with Airflow DAGs.
- **Interactive Analysis**: Explore and visualize data with Jupyter Notebook.
- **Best Practices**: Secure setups, error handling, and automation.

## 🗺️ Quick Navigation

| Section | Description | Link |
|---------|-------------|------|
| 📚 **Mini Courses** | Structured learning paths with examples | [Jump to Courses](#mini-courses) |
| 🛠️ **Tech Stack** | Tools and dependencies | [Tech Stack](#tech-stack) |
| 📁 **File Structure** | What's inside the repo | [Structure](#file-structure) |
| 💡 **Exercises** | Hands-on challenges | [Exercises](#exercises) |
| 🔗 **Related Projects** | Connect to my other work | [Projects](#related-projects) |

---

## 📚 Mini Courses

### 1. PostgreSQL Setup Mini Course 🗄️
**Level**: Beginner | **Duration**: 30 minutes

Learn to install and configure **PostgreSQL 12+** on Ubuntu for data engineering projects.

- **Key Topics**: Installation, database creation, remote access.
- **Examples**: Setting up a `data_engineering` database.

**[Full Course Guide](https://github.com/TsMark01/DE_FAST_COURSE/blob/main/POSTGRESS_INSTALLING.markdown)**

### 2. SQLAlchemy Mini Course 🛠️
**Level**: Beginner-Intermediate | **Duration**: 2-3 hours

Master **SQLAlchemy** for database interactions in Python. Learn CRUD operations, joins, and bulk inserts.

- **Key Topics**: ORM setup, CRUD, joins, error handling.
- **Examples**: Product inventory, exchange rate storage.

**[Full Course Guide](https://github.com/TsMark01/DE_FAST_COURSE/blob/main/SQLALCHEMY_COURSE.markdown)**

### 3. Airflow DAGs Mini Course 🎯
**Level**: Beginner-Intermediate | **Duration**: 2-3 hours

Learn to build and orchestrate data pipelines using **Apache Airflow DAGs**. From basic task execution to custom sensors and database integration.

- **Key Topics**: BashOperator, PostgresOperator, Custom Sensors, Variables.
- **Examples**: Exchange rate fetching, data marts, conditional workflows.

**[Full Course Guide](https://github.com/TsMark01/DE_FAST_COURSE/blob/main/AIRFLOW_DAGS_COURSE.markdown)**

### 4. Jupyter Notebook Mini Course 📝
**Level**: Beginner | **Duration**: 1-2 hours

Master interactive data analysis with **Jupyter Notebook**. Prototype ETL steps, visualize data, and automate tasks.

- **Key Topics**: Pandas cleaning, API integration, SQL queries, Matplotlib plots.
- **Examples**: Data cleaning, exchange rate trends, automated pipelines.

**[Full Course Guide](https://github.com/TsMark01/DE_FAST_COURSE/blob/main/JUPYTER_NOTEBOOK_COURSE.markdown)**

### 5. ETL Pipeline Project 🛠️
**Level**: Intermediate | **Duration**: 4-6 hours

Build a complete **ETL pipeline** fetching from a medical API, processing with Airflow, storing in PostgreSQL, and visualizing in Power BI.

- **Key Topics**: API extraction, data transformation, scheduling, dashboards.
- **Demo**: End-to-end medical data workflow.

**[Project Repo](https://github.com/TsMark01/DE_api_airflow_project_pbi)**

---

## 🛠️ Tech Stack

| Category | Tools | Why? |
|----------|-------|------|
| **Orchestration** | Apache Airflow 2.7+ | Schedule & monitor workflows |
| **Analysis** | Jupyter Notebook | Interactive prototyping |
| **Database** | PostgreSQL 12+ | Reliable data storage |
| **ORM** | SQLAlchemy 2.0+ | Database interactions |
| **Data Processing** | Pandas, SQLAlchemy | Cleaning & querying |
| **API** | Requests | Fetch external data |
| **Visualization** | Matplotlib, Power BI | Insights & dashboards |
| **Deployment** | Docker, Systemd | Scalable environments |

**Requirements File**: See [requirements.txt](requirements.txt) for full list.

---

## 📁 File Structure

```
DE_FAST_COURSE/
├── README.md
├── requirements.txt
├── POSTGRESS_INSTALLING.markdown
├── SQLALCHEMY_COURSE.markdown
├── AIRFLOW_INSTALLING.markdown
├── AIRFLOW_DAGS_COURSE.markdown
├── JUPYTER_NOTEBOOK_INSTALLING.markdown
├── JUPYTER_NOTEBOOK_COURSE.markdown
```

**Note**: Example DAGs, scripts, plugins, and notebooks are referenced in the course guides and can be created as needed.

---

## 💡 Exercises & Challenges

### Beginner Challenges 🎯
1. **DAG Builder**: Create a simple DAG that runs a Jupyter notebook as a Bash task.
2. **Data Cleaner**: In Jupyter, load a CSV, remove duplicates, and plot trends.

### Intermediate Challenges 🚀
1. **API Pipeline**: Build a notebook that fetches weather data and stores it in PostgreSQL.
2. **Sensor Test**: Modify the sensor DAG to check for specific data values (e.g., sales > 1000).

### Advanced Challenge 🏆
- **Full ETL**: Combine Airflow, Jupyter, and Power BI to build an end-to-end pipeline for stock data analysis.

**Submit Your Solutions**: Fork this repo, add your work, and create a PR! I'll review and merge.

---

## 🔗 Related Projects

Explore my other data engineering work:

- **[DE API Airflow + Power BI](https://github.com/TsMark01/DE_api_airflow_project_pbi)**: Full ETL pipeline with medical API integration. 🏥
- **[DE_FAST_COURSE Repo](https://github.com/TsMark01/DE_FAST_COURSE)**: Advanced guides on Airflow and Jupyter setups. 📚
- **[Travel Bot](https://github.com/TsMark01/travel_bot)**: Telegram bot with API and database features. 🌍
- **[Scriptwriter Bot](https://github.com/TsMark01/Bot_Scriptwriter)**: AI-powered story generator with Yandex GPT. 🎬

---

## 🤝 Contributing

1. Fork the repo.
2. Create a feature branch (`git checkout -b feature/amazing-dag`).
3. Commit changes (`git commit -m 'Add cool DAG example'`).
4. Push to branch (`git push origin feature/amazing-dag`).
5. Open a Pull Request!

**Issues?** Open a GitHub issue—I'm here to help! 📞

## 📝 Author

**Mark Tsyrul**  
17-year-old enthusiast from Russia | GitHub: [TsMark01](https://github.com/TsMark01) | Building the future of data engineering and IT! 🚀

*Last Updated: September 27, 2025*