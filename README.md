🔍 SQL Murder Mystery Project

Welcome to the SQL Murder Mystery — an engaging data exploration challenge designed to test and showcase your SQL and Python skills. This project investigates a fictional crime using real database querying, and serves as an interactive detective story using structured data.

--Project Overview--

In this project, I used SQLite and pandas in Python to explore a database filled with clues, suspects, and witness statements. The database contains multiple tables such as crime_scene_report, interview, person, and more. The goal is to solve a murder mystery by writing smart queries that reveal the killer's identity, the method, and the motive.

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

<img width="280" height="433" alt="2" src="https://github.com/user-attachments/assets/15e6db6d-3a9a-4d68-a602-f28b28fbc6ea" />



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
<img width="916" height="258" alt="4" src="https://github.com/user-attachments/assets/c24104f9-2452-41b1-8f8d-50c1e6d09ab5" />

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
<img width="670" height="430" alt="5" src="https://github.com/user-attachments/assets/ff94ad32-b841-42bd-b39c-5573a1edc267" />


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
<img width="919" height="699" alt="7" src="https://github.com/user-attachments/assets/ba860cf0-743f-4d69-8a4a-6bad73c48930" />


8. Find the suspect using a license plate fragment
q = """
SELECT * 
FROM drivers_license
WHERE plate_number LIKE '%H42W%' AND gender = 'male'
"""
pd.read_sql(q, con)
<img width="661" height="229" alt="8" src="https://github.com/user-attachments/assets/498832bf-36b3-43ed-882b-16dfedb9ccb7" />


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
<img width="901" height="382" alt="9" src="https://github.com/user-attachments/assets/2541c2c5-d4a1-4816-b61d-8ac8692dcb99" />


10. Final clue: Who attended the concert 3 times in december?
q = """
SELECT *
FROM facebook_event_checkin
WHERE event_name = 'SQL Symphony Concert' AND date LIKE '201712%'
GROUP BY person_id
HAVING COUNT(*) = 3
ORDER BY person_id
"""
pd.read_sql(q, con)
<img width="525" height="323" alt="10" src="https://github.com/user-attachments/assets/ca923c8e-6b8f-4c7e-90cc-83dc68786d3f" />


11. Solve the mystery and catch the killer
q = """
WITH event_checkins AS (
SELECT *
FROM facebook_event_checkin
WHERE event_name = 'SQL Symphony Concert' AND date LIKE '201712%'
GROUP BY person_id
HAVING COUNT(*) = 3
ORDER BY person_id
),

suspects AS (
SELECT id, name, license_id, ssn
FROM person 
JOIN event_checkins ON person.id = event_checkins.person_id
)

SELECT *
FROM suspects
JOIN income ON suspects.ssn = income.ssn
"""

pd.read_sql(q, con)

<img width="559" height="491" alt="11" src="https://github.com/user-attachments/assets/0be51697-e09a-44dd-bee3-1b9434fd441e" />

