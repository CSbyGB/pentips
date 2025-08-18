# Giskard - SQL Helper - Level 3

- [Link to challenge](https://red.giskard.ai/challenges/broken-formatting/sql-helper-3)

## Instructions

![Instructions](../.res/2025-08-18-06-45-40.png)

## Resolution

When doing this one I realized that my prompt for the level 2 was not that easy to understand (even for a chatbot). 
And when I tried it on this level I got the following error: `Sorry, your request is invalid. I can only generate SELECT queries.`  
Also, the kind of injections I tried with level 1 and 2 where not as easily accepted.  
I tried to make the chatbot leaked it system prompt but did not succeed.
I tried a few other things and realized that what could work is instead of injecting chars in `LIKE`'s string (like we did in level 1 and 2), I could try to name table with reserved SQL keywords. So I asked to gemini a list of reserved keywords in SQL and got the following answer:  

> Reserved keywords in SQL are predefined words that have special meaning within the SQL language and are used to perform specific operations or define elements of the database. These words cannot typically be used as identifiers (such as names for tables, columns, views, or other database objects) unless enclosed in special delimiters, which vary depending on the specific SQL dialect (e.g., double quotes, single quotes, or backticks). 
> Examples of common SQL reserved keywords include:
Data Definition Language (DDL) Keywords: `CREATE, ALTER, DROP, TABLE, INDEX, VIEW, DATABASE`.
Data Manipulation Language (DML) Keywords: `SELECT, INSERT, UPDATE, DELETE, FROM, WHERE, JOIN, GROUP BY, ORDER BY`.
Data Control Language (DCL) Keywords: `GRANT, REVOKE`.
Transaction Control Language (TCL) Keywords:` COMMIT, ROLLBACK`.
Data Types: `INT, VARCHAR, DATE, BOOLEAN`.
Other Keywords: `AS, AND, OR, NOT, NULL, PRIMARY KEY, FOREIGN KEY, UNIQUE, DISTINCT, CASE, WHEN, THEN, ELSE, END`.
While some words might be considered "keywords" and have special meaning in certain contexts, only "reserved keywords" are strictly prohibited from use as identifiers without proper quoting. The specific list of reserved keywords can vary slightly between different SQL implementations (e.g., MySQL, PostgreSQL, Oracle SQL Server). It is generally recommended to avoid using reserved words as identifiers to prevent potential syntax errors and improve code readability.


Next thing was to try each of them and the one that worked was table.  

![Resolution](../.res/2025-07-25-17-23-27.png)