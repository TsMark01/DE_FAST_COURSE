# 🚀 DE Fast Course: Data Engineering Essentials

[![Python 3.9+](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://www.python.org/downloads/)
[![Airflow](https://img.shields.io/badge/Apache%20Airflow-2.7%2B-orange.svg)](https://airflow.apache.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter%20Notebook-green.svg)](https://jupyter.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-13%2B-purple.svg)](https://www.postgresql.org/)

## 📖 Welcome to DE Fast Course! 🎓

This repository is your **one-stop guide** to **Data Engineering fundamentals**, created by **Mark Tsyrul**. Whether you're a beginner exploring ETL pipelines or an intermediate learner building workflows, this course covers essential tools like **Apache Airflow**, **Jupyter Notebook**, and **PostgreSQL** through hands-on examples and mini-courses.

Perfect for **CS students** aiming to build a portfolio for university applications or entry-level roles. Dive in and start engineering data like a pro! 💻

### 🎯 What You'll Learn
- **ETL Pipeline Basics**: Extract, transform, and load data efficiently.
- **Workflow Orchestration**: Schedule and monitor tasks with Airflow DAGs.
- **Interactive Analysis**: Use Jupyter for data exploration and visualization.
- **Database Management**: Query and store data with PostgreSQL.
- **Best Practices**: Secure setups, error handling, and deployment.

## 🗺️ Quick Navigation

| Section | Description | Link |
|---------|-------------|------|
| 📚 **Mini Courses** | Structured learning paths with examples | [Jump to Courses](#mini-courses) |
| 🚀 **Quick Start** | Setup in under 5 minutes | [Setup Guide](#quick-start) |
| 🛠️ **Tech Stack** | Tools and dependencies | [Tech Stack](#tech-stack) |
| 📁 **File Structure** | What's inside the repo | [Structure](#file-structure) |
| 💡 **Exercises** | Hands-on challenges | [Exercises](#exercises) |
| 🔗 **Related Projects** | Connect to my other work | [Projects](#related-projects) |

---

## 📚 Mini Courses

### 1. Airflow DAGs Mini Course 🎯
**Level**: Beginner-Intermediate | **Duration**: 2-3 hours

Learn to build and orchestrate data pipelines using **Apache Airflow DAGs**. From basic task execution to custom sensors and database integration.

- **Key Topics**: BashOperator, PostgresOperator, Custom Sensors, Variables.
- **Examples**: Exchange rate fetching, data marts, conditional workflows.

**[Full Course Guide](https://github.com/TsMark01/DE_FAST_COURSE/blob/main/AIRFLOW_DAGS_COURSE.markdown)**

### 2. Jupyter Notebook Mini Course 📝
**Level**: Beginner | **Duration**: 1-2 hours

Master interactive data analysis with **Jupyter Notebook**. Prototype ETL steps, visualize data, and automate tasks.

- **Key Topics**: Pandas cleaning, API integration, SQL queries, Matplotlib plots.
- **Examples**: Data cleaning, exchange rate trends, automated pipelines.

**[Full Course Guide](https://github.com/TsMark01/DE_FAST_COURSE/blob/main/JUPYTER_NOTEBOOK_COURSE.markdown)**

### 3. ETL Pipeline Project 🛠️
**Level**: Intermediate | **Duration**: 4-6 hours

Build a complete **ETL pipeline** fetching from a medical API, processing with Airflow, storing in PostgreSQL, and visualizing in Power BI.

- **Key Topics**: API extraction, data transformation, scheduling, dashboards.
- **Demo**: End-to-end medical data workflow.

**[Project Repo](https://github.com/TsMark01/DE_api_airflow_project_pbi)**

---

## 🚀 Quick Start

### Prerequisites
- Python 3.9+
- Git
- Docker (optional for isolated environments)

### Setup in 5 Minutes
1. **Clone the Repo**:
   ```bash
   git clone https://github.com/TsMark01/DE_FAST_COURSE.git
   cd DE_FAST_COURSE
   ```

2. **Install Dependencies**:
   ```bash
   python -m venv de_env
   source de_env/bin/activate  # Windows: de_env\Scripts\activate
   pip install -r requirements.txt
   ```

3. **Run Jupyter for Hands-On**:
   ```bash
   jupyter notebook
   ```
   Open `http://localhost:8888` and explore the notebooks.

4. **Start Airflow** (if needed):
   Follow the [Airflow Installation Guide]().

**Pro Tip**: Use the [Jupyter Setup Guide](https://github.com/TsMark01/DE_FAST_COURSE/blob/main/JUPYTERNOTEBOOK_INSTALLING.markdown) for daemon mode.

---

## 🛠️ Tech Stack

| Category | Tools | Why? |
|----------|-------|------|
| **Orchestration** | Apache Airflow 2.7+ | Schedule & monitor workflows |
| **Analysis** | Jupyter Notebook | Interactive prototyping |
| **Database** | PostgreSQL 13+ | Reliable data storage |
| **Data Processing** | Pandas, SQLAlchemy | Cleaning & querying |
| **API** | Requests | Fetch external data |
| **Visualization** | Matplotlib, Power BI | Insights & dashboards |
| **Deployment** | Docker, Systemd | Scalable environments |

**Requirements File**: See [requirements.txt](requirements.txt) for full list.

---

## 📁 File Structure

```
DE_FAST_COURSE/
├── README.md                  # 📄 This file
├── requirements.txt           # 📦 Dependencies
├── dags/                      # 📊 Airflow DAG examples
│   ├── basic_dag.py
│   ├── exchange_rate_dag.py
│   └── data_mart_dag.py
├── scripts/                   # 🐍 Python scripts
│   ├── task1.py
│   └── main_script.py
├── notebooks/                 # 📓 Jupyter examples
│   ├── data_cleaning.ipynb
│   └── automated_pipeline.ipynb
├── plugins/                   # 🔌 Custom Airflow plugins
│   └── check_table_sensor.py
└── guides/                    # 📖 Linked guides
    ├── airflow_installing.md
    └── jupyter_installing.md
```

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
