🔍 SQL Murder Mystery Project

Welcome to the SQL Murder Mystery — an engaging data exploration challenge designed to test and showcase your SQL and Python skills. This project investigates a fictional crime using real database querying, and serves as an interactive detective story using structured data.

--Project Overview--

In this project, I used SQLite and pandas in Python to explore a database filled with clues, suspects, and witness statements. The database contains multiple tables such as crime_scene_report, interview, person, and more. The goal is to solve a murder mystery by writing smart queries that reveal the killer's identity, the method, and the motive.
<img width="1470" height="956" alt="Screenshot 2025-07-14 at 12 10 07" src="https://github.com/user-attachments/assets/76bea831-2b34-46c5-bc68-f067124f3e5c" />

The project is also a fun way to practice:

SQL joins, filters, and aggregations
Working with real relational databases
Data analysis with pandas

--Tools Used--

SQLite3
pandas
Jupyter Notebook
📦 Getting Started

Make sure to have:

Python 3.8+
Jupyter Notebook
SQLite
pandas

--📂 Example Snippets--

1. Connect to the SQLite database using Python
import sqlite3
import pandas as pd

con = sqlite3.connect('sql-murder-mystery.db')
pd.set_option('display.max_colwidth', None)

2. List all tables in the database
q = """
SELECT name 
FROM sqlite_master
WHERE type = 'table'
"""
pd.read_sql(q, con)

3. Check the structure of a table
q = """
SELECT sql
FROM sqlite_master
WHERE name = 'crime_scene_report'
"""
pd.read_sql(q, con)


4. Begin clue search from the crime scene
q = """
SELECT *
FROM crime_scene_report
WHERE city = 'SQL City' AND date = '20180115' AND type = 'murder'
"""
pd.read_sql(q, con)

5. Identify potential suspects using address-based filters
q = """
SELECT *
FROM
(
  SELECT *
  FROM person
  WHERE address_street_name = 'Northwestern Dr'
  ORDER BY address_number DESC
  LIMIT 1
)
UNION ALL
SELECT *
FROM person 
WHERE address_street_name = "Franklin Ave" AND name LIKE 'Annabel%'
"""
pd.read_sql(q, con)


6. Investigate all interviews
q = """
SELECT *
FROM interview 
"""
pd.read_sql(q, con)


7. Join person and interview info for suspect profiling
q = """
SELECT person.*, interview.*
FROM
(
  SELECT *
  FROM person
  WHERE address_street_name = 'Northwestern Dr'
  ORDER BY address_number DESC
  LIMIT 1
) AS nw_person
JOIN interview ON nw_person.id = interview.person_id

UNION ALL

SELECT person.*, interview.*
FROM person 
JOIN interview ON person.id = interview.person_id
WHERE address_street_name = "Franklin Ave" AND name LIKE 'Annabel%'
"""
pd.read_sql(q, con)


8. Find the suspect using a license plate fragment
q = """
SELECT * 
FROM drivers_license
WHERE plate_number LIKE '%H42W%' AND gender = 'male'
"""
pd.read_sql(q, con)


9. Narrow down gym members using membership and check-in data
q = """
WITH members AS (
  SELECT * 
  FROM get_fit_now_check_in
  WHERE check_in_date = '20180109' AND membership_id LIKE '48Z%'
)
SELECT *
FROM get_fit_now_member
JOIN members ON get_fit_now_member.id = members.membership_id
"""
pd.read_sql(q, con)


10. Combine multiple filters to confirm suspects
q = """
SELECT person.*, drivers_license.*
FROM person
JOIN drivers_license ON person.license_id = drivers_license.id
WHERE plate_number LIKE '%H42W%' AND gender = 'male'
  AND height BETWEEN 65 AND 70
  AND hair_color = 'red'
  AND car_make = 'Tesla'
  AND car_model = 'Model S'
"""
pd.read_sql(q, con)


11. Final clue: Interviews of key suspects
q = """
SELECT p1.*, i1.*
FROM (
    SELECT *
    FROM person
    WHERE name = 'Jeremy Bowers'
) AS p1
JOIN interview AS i1 ON p1.id = i1.person_id
"""
pd.read_sql(q, con)


12. Solve the mystery and catch the killer
q = """
SELECT *
FROM solution
WHERE description LIKE '%Jeremy Bowers%'
"""
pd.read_sql(q, con)
