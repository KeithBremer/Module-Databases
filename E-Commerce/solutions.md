# Project Brief: E-Commerce Database

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

## Learning Objectives

- Use SQL queries to retrieve specific data from a database
- Draw a database schema to visualize relationships between tables
- Label database relationships defined by the `REFERENCES` keyword in `CREATE TABLE` commands

## Requirements

### Setup

To prepare your environment, open a terminal and create a new database called `cyf_ecommerce`:

```sql
createdb cyf_ecommerce
```

Import the file [`cyf_ecommerce.sql`](./cyf_ecommerce.sql) in your newly created database:

```sql
psql -d cyf_ecommerce -f cyf_ecommerce.sql
```

### Understand the schema

Open the file `cyf_ecommerce.sql` in VSCode and examine the SQL code. Take a piece of paper and draw the database with the different relationships between tables (as defined by the REFERENCES keyword in the CREATE TABLE commands). Identify the foreign keys and make sure you understand the full database schema.

Don't skip this step. You may one day [be asked at interview](https://monzo.com/blog/2022/03/23/demystifying-the-backend-engineering-interview-process) to draw a database schema. Sketching systems is a valuable skill for back end developers and worth practising. If you're interested in systems design, you may also want to take a course on Udemy.

You can even [draw relationship diagrams](https://mermaid.js.org/syntax/entityRelationshipDiagram.html) on [GitHub](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-diagrams):

```mermaid
erDiagram
    customers {
        id INT PK
        name VARCHAR(50) NOT NULL
        address VARCHAR(120)
        city VARCHAR(30)
        country VARCHAR(20)
    }
    suppliers {
        id INT PK
        supplier_name VARCHAR(100) NOT NULL
        country VARCHAR(20) not null
    }
    products {
        id INT PK
        product_name VARCHAR(100) NOT NULL
    }
    product_availability {
        prod_id INT PK
        supp_id INT PK
        unit_price INT NOT NULL
    }
    orders {
        id INT PK
        order_date DATE NOT NULL
        order_reference VARCHAR(10) NOT NULL
        customer_id INT
    }
    order_items {
        id INT PK
        order_id INT NOT NULL
        product_id INT NOT NULL
        supplier_id INT NOT NULL
        quantity INT NOT NULL
    }
    customers ||--o{ orders : places
    orders ||--0{ order_items : contains
    products ||--0{ product_availability : of
    suppliers ||--0{ product_availability : from
    product_availability ||--0{ order_items : part-of

```

### Query Practice

Write SQL queries to complete the following tasks:

- [ ] List all the products whose name contains the word "socks"
- [ ] List all the products which cost more than 100 showing product id, name, unit price, and supplier id
- [ ] List the 5 most expensive products
- [ ] List all the products sold by suppliers based in the United Kingdom. The result should only contain the columns product_name and supplier_name
- [ ] List all orders, including order items, from customer named Hope Crosby
- [ ] List all the products in the order ORD006. The result should only contain the columns product_name, unit_price, and quantity
- [ ] List all the products with their supplier for all orders of all customers. The result should only contain the columns name (from customer), order_reference, order_date, product_name, supplier_name, and quantity

## Acceptance Criteria

- [ ] The `cyf_ecommerce` database is imported and set up correctly
- [ ] The database schema is drawn correctly to visualize relationships between tables
- [ ] The SQL queries retrieve the correct data according to the tasks listed above
- [ ] The pull request with the answers to the tasks is opened on the `main` branch of the `E-Commerce` repository
