# LLM-Powered SQL Data Retrieval Assistant

This project lets users ask questions in **plain English** and automatically translates them into **SQL queries** using an LLM, runs them on a database, and shows the results in a clean UI.

## Project Overview

A system where users can:

1. Type (or speak) a question in natural language
2. Have an **LLM (Euri)** convert it into a valid SQL query
3. Execute the SQL against a **PostgreSQL database (Neon.tech)**
4. View the results directly in the app

This makes database querying accessible to **non-technical users** without needing to know SQL.

## Technologies used

- **Streamlit** - For the interactive web UI
- **Euri AI** - LLM platform to:
  - Understand natural language queries
  - Generate SQL statements
- **SQLAlchemy** - ORM/database toolkit to:
  - Connect to the SQL database
  - Inspect schemas
  - Execute SQL queries
- **Neon.tech** - Cloud-hosted PostgreSQL database
- **SpeechRecognition** - For speech-to-text input
- **PyAudio** - For capturing audio from mic
- **Plotly** - For interactive data visualizations

## System Architecture

High-level flow:

1. **User Input (Streamlit UI)**
   - User provides a query in natural language (text or speech).

2. **LLM Processing (Euri)**
   - The query plus database schema/context is sent to Euri.
   - Euri generates a **SQL query**.

3. **Database Execution (SQLAlchemy + Neon.tech)**
   - Generated SQL is executed on the Neon.tech PostgreSQL database.
   - Results are fetched.

4. **UI Output (Streamlit)**
   - Display:
     - The generated SQL query
     - Results in a **table**
     - Optional **visualizations** via Plotly

Pipeline:

`Streamlit UI → Euri LLM → SQL generation → SQLAlchemy + Neon.tech → Results back to UI`

## Database Setup (Neon.tech)

The example setup uses a typical e-commerce-style schema:

- `customer`
- `product`
- `order`
- `order_item`

### Steps (conceptual)

1. Create a PostgreSQL database on **Neon.tech**.
2. Run SQL commands to:
   - Create the four tables.
   - Insert sample data (customers, products, orders, order items).

> **Note:** Check the repo’s SQL/init scripts or documentation for the exact DDL and seed data.

## `.env` Configuration

The project uses a `.env` file to store sensitive configuration:

- `EURI_API_KEY` – API key for accessing **Euri API's**
- `DATABASE_URI` – SQLAlchemy connection string for the **Neon.tech** PostgreSQL database
  (e.g., `postgresql+psycopg2://user:password@host/dbname`)

Example `.env`:

```env
EURI_API_KEY=your_euri_api_key_here

DATABASE_URI=postgresql+psycopg2://user:password@host/dbname
```

## Code Structure

The core Python files are:

### `config.py`

- Handles configuration and environment variables
- Loads:
  - `EURI_API_KEY`
  - `DATABASE_URI`
- May also set:
  - Model names
  - Timeouts
  - Other app-level configs

### `utils.py`

Contains helper functions such as:

- `get_db_schema()`
  - Uses **SQLAlchemy** to inspect the database
  - Retrieves table names, columns, and types
  - Provides schema context for the LLM

- **Euri LLM call**
  - Sends:
    - Natural language query
    - Database schema/context
  - Receives:
    - Generated SQL query

- `execute_sql(query: str)`
  - Uses SQLAlchemy to:
    - Connect to the database
    - Execute the SQL
    - Return results (e.g., as a DataFrame or list of rows)

### `main.py`

- Implements the **Streamlit UI**:
  - Text input for natural language queries
  - Optional voice input using **SpeechRecognition** and **PyAudio**
  - Buttons to trigger query execution
  - Displays:
    - Generated SQL
    - Query results in a **table**
    - Optional Plotly charts for visualization
- Orchestrates calls to:
  - `get_db_schema()`
  - Euri LLM for SQL generation
  - `execute_sql()` for data retrieval

## Key Functions & Components

- `get_db_schema()`
  - Inspects the database via SQLAlchemy
  - Helps Euri understand what tables/columns are available

- **Euri LLM Call** (name may vary in code)
  - Input: natural language query + schema
  - Output: generated SQL query string

- `execute_sql(sql: str)`
  - Executes the query on Neon.tech database
  - Returns:
    - Rows and column names for display
    - Data for Plotly visualizations

## UI Implementation (Streamlit)

Key UI elements:

- **Text Input**
  - Users type questions like:
    - “Show me the total sales by product for the last month”
    - “List all customers who placed more than 3 orders”

- **(Optional) Voice Input**
  - Using SpeechRecognition + PyAudio:
    - User speaks a query
    - It’s transcribed to text
    - Then processed like any other query

- **SQL Preview**
  - Display the **generated SQL** so users can:
    - Understand what’s being executed
    - Validate or learn from it

- **Results Table**
  - Display query results in a neat table
  - Potential filters or pagination (depending on implementation)

- **Plotly Visualizations**
  - Convert query results into:
    - Bar charts
    - Line plots
    - Pie charts
  - Makes numeric insights easier to interpret

- **Ngrok Integration (Global Access)**
  - App can be exposed to the internet using **ngrok**
  - Makes the local Streamlit app accessible globally for demos

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/Anurag0798/LLM-Powered-SQL-Data-Retrieval-Assistant.git

cd LLM-Powered-SQL-Data-Retrieval-Assistant
```

### 2. Create and Activate Virtual Environment (Optional but Recommended)

```bash
python -m venv .venv

# Linux / macOS
source .venv/bin/activate

# Windows
.venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

For audio features, ensure:

- **PyAudio** is installed and supported by your OS
- Microphone access is available

### 4. Configure `.env`

Create a `.env` file:

```env
EURI_API_KEY=your_euri_api_key_here
DATABASE_URI=postgresql+psycopg2://user:password@host/dbname
```

### 5. Prepare the Database (Neon.tech)

- Create a PostgreSQL instance on Neon.tech.
- Run the SQL script or commands to:
  - Create `customer`, `product`, `order`, `order_item` tables.
  - Insert sample data.

### 6. Run the App

```bash
streamlit run main.py
```

Open the local URL that Streamlit prints in your terminal.

## Key Takeaways

- **Natural Language → SQL**
  Euri LLM can translate user-friendly queries into accurate SQL statements.

- **Accessibility for Non-Technical Users**
  Users do not need to know SQL or database internals to retrieve data.

- **Powerful Stack**
  Combining **Streamlit**, **Euri AI**, and **SQLAlchemy** offers a flexible, modern stack for building data apps.

- **Extendable & Creative**
  You’re encouraged to:
  - Add more complex visualizations
  - Enhance schema reasoning
  - Support multiple databases
  - Add user authentication or logging

- **AI + Problem Solving**
  The project highlights how **creativity and problem-solving** remain crucial in the age of AI—tools like Euri and Streamlit amplify what you can build.

## Contributing

Contributions are welcome!

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Open a Pull Request with a clear description

## License

This project is open-source. See the `LICENSE` file in the repository for details.