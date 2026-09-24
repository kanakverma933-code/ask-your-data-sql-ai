Ask Your Data — Natural Language to SQL

A small tool that lets you ask questions about a database in plain English, instead of writing SQL yourself.

You type a question like "Which restaurant has the highest rating?" — the app uses the Gemini API to turn it into a MySQL query, runs it safely against the database, and prints the result.

How it works
Connects to a MySQL database and reads the table structure automatically.
Sends the question, along with the table's column names and types, to Gemini.
Gemini responds with one SQL SELECT query.
The query is checked for safety before running (see below).
The query runs against the database, and the result is printed.
Safety measures

Since an AI is generating the SQL automatically, two layers of protection are built in:

Read-only database user — the MySQL user this app connects with only has SELECT permission. Even if a bad query were generated, the database itself would refuse to run anything that modifies data.
Code-level validation — before any query runs, it's checked to confirm it starts with SELECT, contains no destructive keywords (DELETE, DROP, UPDATE, etc.), and isn't secretly multiple statements chained together.

This was tested by deliberately asking the app a destructive-sounding question and confirming the safety check caught it before anything ran.

Tech stack
Python
MySQL
Google Gemini API
mysql-connector-python, python-dotenv
Example

Question: "Which restaurant has the highest rating?"

Generated SQL:

sql
SELECT Restaurant_Name, Aggregate_rating
FROM zomato_restaurants
ORDER BY Aggregate_rating DESC
LIMIT 1;

Result: printed as rows in the terminal.

Setup
Clone this repository.
Create a .env file (not included, for security) with:
   GEMINI_API_KEY=your_key_here
   DB_HOST=localhost
   DB_USER=your_readonly_db_user
   DB_PASSWORD=your_db_password
   DB_NAME=your_database_name
Install dependencies:
   pip install mysql-connector-python google-generativeai python-dotenv
Run:
   python SQL_AI.py
Dataset

Uses a restaurant dataset (Zomato-style) with fields like restaurant name, city, cuisine, cost for two, ratings, and votes.

Next steps - 
Build a Streamlit interface so questions can be typed in a browser instead of edited in code.
Add a test set of known questions and correct answers to measure accuracy.
