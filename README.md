
# Database Admin 101 - Lab

## Introduction 

In this lab, you'll go through the process of designing and creating a database. From there, you'll begin to populate this table with mock data provided to you.

## Objectives

You will be able to:

* Use knowledge of the structure of databases to create a database and populate it

## The Scenario

You are looking to design a database for a school that will house various information from student grades to contact information, class roster lists and attendance. First, think of how you would design such a database. What tables would you include? What columns would each table have? What would be the primary means to join said tables?

## Creating the Database

Now that you've put a little thought into how you might design your database, it's time to go ahead and create it! Start by import the necessary packages. Then, create a database called **school.sqlite**.


```python
# Import necessary packages
import sqlite3
import pandas as pd
```


```python
# Create the database school.sqlite 
conn = sqlite3.connect("school.sqlite")

```

## Create a Table for Contact Information

Create a table called contactInfo to house contact information for both students and staff. Be sure to include columns for first name, last name, role (student/staff), telephone number, street, city, state, and zipcode. Be sure to also create a primary key for the table. 


```python
cur = conn.cursor()
cur.execute("""CREATE TABLE contactInfo(
                                        userid INTEGER PRIMARY KEY,
                                        firstName TEXT,
                                        lastName TEXT,
                                        role TEXT,
                                        telephone INTEGER,
                                        street INTEGER,
                                        city TEXT,
                                        state TEXT,
                                        zipcode INTEGER
                                        );
""")
```

## Populate the Table

Below, code is provided for you in order to load a list of dictionaries. Briefly examine the list. Each dictionary in the list will serve as an entry for your contact info table. Once you've briefly investigated the structure of this data, write a for loop to iterate through the list and create an entry in your table for each person's contact info.


```python
# Load the list of dictionaries; just run this cell
import pickle

with open('contact_list.pickle', 'rb') as f:
    contacts = pickle.load(f)
```


```python
# Iterate over the contact list and populate the contactInfo table here
for contact in contacts:
    firstName = contact['firstName']
    lastName = contact['lastName']
    role = contact['role']
    telephone = contact['telephone ']
    street = contact['street']
    city = contact['city']
    state = contact['state']
    zipcode = contact['zipcode ']
    cur.execute("""INSERT INTO contactInfo(firstName, lastName, role, telephone, street, city, state,zipcode)
            VALUES('{}', '{}', '{}', '{}', '{}', '{}', '{}', '{}');
    """.format(firstName, lastName, role, telephone, street, city, state,zipcode) )

```

**Query the Table to Ensure it is populated**


```python
cur.execute("""SELECT * 
FROM contactInfo;""")
df = pd.DataFrame(cur.fetchall())
df.columns = [x[0] for x in cur.description]
df

```

## Commit Your Changes to the Database

Persist your changes by committing them to the database.


```python
conn.commit()
```

## Create a Table for Student Grades

Create a new table in the database called "grades". In the table, include the following fields: userId, courseId, grade.

** This problem is a bit more tricky and will require a dual key. (A nuance you have yet to see.)
Here's how to do that:

```SQL
CREATE TABLE table_name(
   column_1 INTEGER NOT NULL,
   column_2 INTEGER NOT NULL,
   ...
   PRIMARY KEY(column_1,column_2,...)
);
```


```python
# Create the grades table
cur.execute("""CREATE TABLE grades (
                        userId INTEGER NOT NULL,
                        courseId INTEGER NOT NULL,
                        grades INTEGER,
                        PRIMARY KEY(userId, courseId)
                        );
            """)

```

## Remove Duplicate Entries

An analyst just realized that there is a duplicate entry in the contactInfo table! Find and remove it.


```python
# Find the duplicate entry
cur.execute("""SELECT firstName, lastName, telephone, COUNT(*)
FROM contactInfo
GROUP BY firstName, lastName, telephone
HAVING COUNT(*) > 1;
""").fetchall()
```


```python
# Delete the duplicate entry
cur.execute("""DELETE FROM contactInfo
            WHERE telephone = 3259909290;
            """)

```


```python
# Check that the duplicate entry was removed
cur.execute("""SELECT firstName, lastName, telephone, COUNT(*)
FROM contactInfo
GROUP BY firstName, lastName, telephone
HAVING COUNT(*) > 1;
""").fetchall()

```

## Updating an Address

Ed Lyman just moved to `2910 Simpson Avenue York, PA 17403`. Update his address accordingly.


```python
# Update Ed's address
cur.execute(""" UPDATE contactInfo
            SET street = '2910 Simpson Avenue',
                city = 'York',
                state = 'PA',
                zipcode = '17403'
            WHERE firstName = 'Ed' AND lastName = 'Lyman';""")

```


```python
# Query the database to ensure the change was made
cur.execute("""SELECT *
            FROM contactInfo;""")
df = pd.DataFrame(cur.fetchall())
df.columns = [x[0] for x in cur.description]
df

```

## Commit Your Changes to the Database

Once again, persist your changes by committing them to the database.


```python
conn.commit()
```

## Summary

While there's certainly more to do with setting up and managing this database, you got a taste for creating, populating, and maintaining databases! Feel free to continue fleshing out this exercise for more practice. 
