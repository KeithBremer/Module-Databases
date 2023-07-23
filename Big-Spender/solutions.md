# Big Spender

## Notes on Solutions

The solutions listed here are probably the most appropriate ones but please be aware that most problems can be solved in a number of valid ways using SQL.  Some solutions list alternatives that are equally or almost equally valid but there may be others.  The best way to determine if a solution is correct is to run it against the data and check the results are the same - if they are then that's a fairly good indication that it's a correct answer.  Always remember, however, that a valid solution should be generically correct, not dependent on the specifics of the sample data.

The following includes the text from the original coursework with the solutions provided between pairs of horizontal rule lines (to differentiate them from the coursework text) as follows:

---
---
```
The solution would be here...
```
---
---

Notes:

In the code solutions the lines that would be entered into `psql` are preceded by a '>' character to indicate the command prompt. The results of the commands are shown without any prompt character.

If you wish to run the solution commands you'll need to remove the '>' prompt characters from the text.

Any command text preceded by -- is a comment and not required.

` `  
` `  

## Objectives

- Use SQL queries to retrieve specific data from a database
- Interpret natural language user questions
- Write SQL queries to answer user questions

## Requirements

Run the following commands:

```
createdb big-spender
psql -d big-spender -f big-spender.sql
```

This will create and populate a database called big-spender from the file big-spender.sql.

It is based on real data from the government found at
https://data.gov.uk/dataset/72eaec8e-0d32-4041-a553-87b852abee64/spend-over-25-000-in-worcestershire-acute-hospitals-nhs-trust

You can work with it by running

```
psql big-spender
```

## User Stories:

- As a data analyst, I want to retrieve specific transaction records that meet a certain criteria, so that I can generate insights and reports based on the data.
- As a finance manager, I want to know the total amount spent on a specific month, so that I can compare it with our budget and plan accordingly.
- As a data analyst, I want to filter transactions by a specific keyword, so that I can retrieve records that are relevant to my analysis.
- As a finance manager, I want to add a missing transaction to the database, so that it correctly reflects our latest expenses for our report.

## Briefing

You are a data analyst working with the finance team at Worcestershire Acute Hospital Trust. The finance team has asked you to help them analyse their spending data. They have provided you with a file containing all of their big-ticket spending data for 2021.

You are working with Claire and Farnoosh, who are trying to complete a missing report for their boss. They don't just want the answers, they want the queries that will give them the answers. They want to be able to run the queries themselves, so they can do this year's report without your help.

**Claire:** Hey, can you help us out with something? We need to analyze our spending data for 2021 because apparently the report is missing.

**You:** I can try. What kind of data are you looking for exactly?

**Farnoosh:** We need to find out the transactions between £30,000 and £31,000. Could you help us write a query for that?

**You:** Absolutely. Here's the SQL query you need:

---
---
```sql
>SELECT date, transaction_no, description, amount
>  FROM spends
>  WHERE amount between 30000 AND 31000;

    date    | transaction_no |                           description                            | amount 
------------+----------------+------------------------------------------------------------------+--------
 2021-03-01 |       37755444 | Flexible endoscope Technical Service                             |  30238
 2021-03-01 |       37632601 | Support and Maintenance for Clinisys Winpath Pathology IT system |  30242
 2021-03-01 |       37675451 | Drugs for Spasticity clinics                                     |  30591
 2021-04-01 |       37854035 | CALL OFF PO - REMOTE ACCESS                                      |  30000
 2021-04-01 |       38014043 | Advisor Projects                                                 |  30040
 2021-04-01 |       37823809 | CALL OFF ORDER - NETWORKING                                      |  30942
 2021-04-01 |       37829728 | Linen services                                                   |  30990
(7 rows)
```

Alternatively, the following is exactly equivalent:

```sql
>SELECT date, transaction_no, description, amount
>  FROM spends
>  WHERE amount >= 30000 
>    AND amount <= 31000;
```
No results shown as they are the same as above.

---
---

**Claire:** That's great, thanks. Hey, what about transactions that include the word 'fee' in their description?

**You:** Does case matter?

**Claire:** I don't know. What do you mean?

**You:** Does it matter if fee is written like "Fee" or "fee" or "FEE"? Are those all the same to you?

**Claire:** I think that's all the same to me.

**You:** Then here's the query for that:

---
---
```sql
>SELECT date, transaction_no, description, amount
>  FROM spends
>  WHERE description ILIKE '% fee%';

    date    | transaction_no |                          description                           | amount 
------------+----------------+----------------------------------------------------------------+--------
 2021-03-01 |       37600517 | Overseas Nurses Fees                                           |  34800
 2021-03-01 |       37574010 | Agreement fee as set out in the Report for the Settlement Deed |  51113
 2021-03-01 |       37750117 | Consultant Fees                                                |  74996
 2021-03-01 |       37726776 | Consultant Fees                                                | 461861
 2021-03-01 |       37669018 | Consultant Fees                                                | 646944
 2021-03-01 |       37788777 | Consultant Fees                                                | 423270
 2021-04-01 |       37788824 | Recruitment fee for international nurses                       |  34800
 2021-04-01 |       37828209 | DAF - Professional fees                                        | 300000
 2021-04-01 |       38014054 | Annual Fee                                                     | 319646
(9 rows)
```

Because ILIKE is a non-standard PostgreSQL extension you could alternatively use `lower(description) LIKE '% fee%'` instead.

You could also use `description ILIKE '% fee %'` but this doesn't return any with plural fees.  Note that the match string should probably be `% fee%` (with a leading space before 'fee'). This ensures we don't select descriptions that might contain words like 'coffee', although none exist in the sample data but it does also exclude any with, for example. '(fees' or '/fees' (again, none exist in the sample data).

---
---
**Farnoosh:** Hi, it's me again. It turns out we also need the transactions that have the expense area of 'Better Hospital Food'. Can you help us with that one?

**You:** No worries. Here's the query for that:

---
---
Most experienced SQL developers would use a subquery, as follows:
```sql
>SELECT date, transaction_no, description, amount
>  FROM spends
>  WHERE expense_area_id = (
>         SELECT id
>            FROM expense_areas
>            WHERE expense_area = 'Better Hospital Food'
>         );

    date    | transaction_no |         description         | amount 
------------+----------------+-----------------------------+--------
 2021-03-01 |       37588988 | Meals Volume                | 105000
 2021-03-01 |       37588992 | Meals Additional Variations |  32558
 2021-03-01 |       37716258 | Meals Volume                | 100000
(3 rows)

```

If, however, you are not familiar with subqueries yet then a join can get the same results:
```sql
>SELECT s.date, s.transaction_no, s.description, s.amount
>  FROM spends s JOIN
>       expense_areas a ON (s.expense_area_id = a.id)
>  WHERE a.expense_area = 'Better Hospital Food';

```
If you (the trainee) also haven't yet encountered joins then this request will need two queries, as follows:

```sql
>SELECT id FROM expense_areas WHERE expense_area = 'Better Hospital Food';
 id 
----
  2
(1 row)

>SELECT date, transaction_no, description, amount
>  FROM spends
>  WHERE expense_area_id = 2;
```
which results in the same three rows as the join solution. Note that you must copy the result of the first query into the where clause of the second.

---
---

**Claire:** Great, that's very helpful. How about the total amount spent for each month?

**You:** You can get that by using the GROUP BY clause. Here's the query:

---
---
```sql
>SELECT date, sum(amount) as total_amount
>  FROM spends
>  GROUP BY date;

    date    | total_amount 
------------+--------------
 2021-03-01 |     28674452
 2021-04-01 |     22895194
(2 rows)

```
Note: this is not strictly correct but works because the only dates are the first of each month.  A more resilient query would be:

```sql
>SELECT date_trunc('month', date) as month, sum(amount) as total_amount
>  FROM spends
>  GROUP BY month;
```

but note that the date format in the result appears differently in this case (including a time component set to midnight). This can be fixed by explicitly formatting the result of date_trunc using a function (see if you can figure out how?)

---
---

**Farnoosh:** Thanks, that's really useful. We also need to know the total amount spent on each supplier. Can you help us with that?

**You:** Sure thing. Here's the query for that:

---
---
```sql
>SELECT su.supplier, sum(sp.amount)
>  FROM spends sp JOIN
>       suppliers su ON (sp.supplier_id = su.id)
>  GROUP BY su.supplier
>  ORDER BY su.supplier;

                         supplier                         |   sum    
----------------------------------------------------------+----------
 3D ALUMINIUM PLAS LTD                                    |   349528
 ADEPT TELECOM                                            |   215168
 ALLIANCE HEALTHCARE DISTRIBUTION LTD                     |   211469
 ALLOGA UK LTD                                            |    27000
 ALLSCRIPTS HEALTHCARE (IT) UK LTD                        |   967296
 ALTIATECH LTD                                            |    71780
 ARJO UK LTD                                              |    26387
 BATH ASU                                                 |   292630
 BAYER PLC                                                |   419730
 BIOTEST UK LTD                                           |   109200
 BIRMINGHAM & SOLIHULL MENTAL HEALTH NHS FOUNDATION TRUST |   324200
 BOXXE LTD                                                |   122820
 CARE QUALITY COMMISSION                                  |   319646
 . . .
```
The elipsis (. . .) above indicates that there are more results not shown here.

Again, if you aren't yet able to use joins then the query could be put as:

```sql
>SELECT supplier_id, sum(amount)
>  FROM spends
>  GROUP BY supplier_id
>  ORDER BY supplier_id;
```

This doesn't show the supplier name but the numeric supplier id.

Note that in both versions of the solution the ORDER BY clause is not required to meet the user's needs but might help 

---
---

**Claire:** Thanks, that's really helpful. I can't quite figure out...what is the total amount spent on each of these two dates (1st March 2021 and 1st April 2021)?

**You:** I think you can use the BETWEEN clause to get the total amount spent on a range of dates, just like we used earlier.

**Claire:** But I think I _only_ want those two dates, not a range, or all the days in between.

**You:** Then you need an extra clause. Here's the query:

---
---
```sql
>SELECT date, sum(amount) as total_amount
>  FROM spends
>  WHERE date IN ('2021-03-01', '2021-04-01')
>  GROUP BY date;

    date    | total_amount 
------------+--------------
 2021-03-01 |     28674452
 2021-04-01 |     22895194
(2 rows)
```

or alternatively the following is the same:

```sql
>SELECT date, sum(amount) as total_amount
>  FROM spends
>  WHERE date = '2021-03-01'
>     OR date = '2021-04-01'
>  GROUP BY date;
```
---
---

**Farnoosh:** Fantastic. One last thing, looks like we missed something. Can we add a new transaction to the spends table with a description of 'Computer Hardware Dell' and an amount of £32,000?

**You:** When was this?

**Farnoosh:** The receipt says August 19, 2021

**You:** Sure thing. To confirm, the date is August 19, 2021, the transaction number is 38104091, the supplier invoice number is 3780119655, the supplier is 'Dell', the expense type is 'Hardware' and the expense area is 'IT'. Here's the query for that:

---
---

Note:
You must first discover whether the supplier, expense type and expense areas exist and, if not, then create them, hence the initial SELECTs and following INSERTs.  The INSERT commands can use the `RETURNING` clause to display the generated id values of each.

```sql
>SELECT * FROM suppliers WHERE supplier = 'Dell';
>SELECT * FROM expense_types WHERE expense_type = 'Hardware';
>SELECT * FROM expense_areas WHERE expense_area = 'IT';
-- The above should each yield (0 rows)
>INSERT INTO suppliers (supplier) VALUES ('Dell') RETURNING id;
 id 
----
 66
(1 row)

INSERT 0 1

>INSERT INTO expense_types (expense_type) VALUES ('Hardware') RETURNING id;
 id 
----
 42
(1 row)

INSERT 0 1

>INSERT INTO expense_areas (expense_area) VALUES ('IT') RETURNING id;
 id 
----
 46
(1 row)

INSERT 0 1
```

The above sets up the required rows for the new `spends` entry, which can now be added as follows:

```sql
>INSERT INTO spends (
>     expense_type_id,
>     expense_area_id,
>     supplier_id,
>     date,
>     transaction_no,
>     supplier_inv_no,
>     description,
>     amount)
>  VALUES (
>     42,            -- taken from the result of "INSERT INTO expense_types" above
>     46,            -- taken from the result of "INSERT INTO expense_areas" above
>     66,            -- taken from the result of "INSERT INTO suppliers" above
>     '2021-08-19',
>     38104091,
>     3780119655,
>     'Computer Hardware Dell',
>     32000);

```

Note that the order of the items in the last insert can be arbitrary but the values must match the corresponding columns.  The values of expense_area_id, expense_type_id and supplier_id are those that you would have obtained normally, but make allowances for different values in each case.

---
---

**Claire:** Great, that's everything we need. Thanks for your help.

**You:** No problem, glad I could help you out.

## Acceptance Criteria

- [ ] All user stories are satisfied
- [ ] All queries are written in SQL
- [ ] All queries are correct and I have tested them in the database
- [ ] I have opened a pull request with my answers written directly into this README.md file
