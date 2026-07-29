# Library Management System

A Java application to manage book inventory, member records, and
issue/return transactions for a library, backed by a relational SQL
database and reported on via Power BI.

## Tech Stack
- **Java** (JDBC) — application logic, book/member/transaction management
- **SQL** (MySQL) — relational schema for books, members, and transactions
- **Power BI** — circulation trends and overdue-book reporting

## Project Structure
```
library-management-system/
├── pom.xml
├── sql/
│   ├── schema.sql                # books / members / transactions tables
│   └── circulation_report.sql    # queries powering the Power BI report
├── powerbi/
│   └── README.md                 # how to connect Power BI to the DB
└── src/main/java/com/library/
    ├── model/
    │   ├── Book.java
    │   ├── Member.java
    │   └── Transaction.java
    ├── dao/
    │   ├── DatabaseConnection.java
    │   ├── BookDAO.java
    │   ├── MemberDAO.java
    │   └── TransactionDAO.java
    └── app/
        ├── LibraryService.java    # business rules (availability, loan period)
        └── Main.java              # console entry point
```

## How It Works
- **model/** — plain Java objects for `Book`, `Member`, and `Transaction`
  (with an `ISSUED / RETURNED / OVERDUE` status).
- **dao/** — JDBC data-access classes, one per table, using
  `PreparedStatement` for all queries.
- **app/LibraryService** — enforces business rules: a book can't be issued
  if it has zero available copies; issuing/returning a book keeps
  `books.available_copies` in sync with the `transactions` table.
- **app/Main** — a simple console menu to add books, register members,
  issue/return books, list inventory, and view overdue books.

## Setup
1. Create the database and tables:
   ```bash
   mysql -u root -p < sql/schema.sql
   ```
2. Set connection environment variables:
   ```bash
   export LIBRARY_DB_URL="jdbc:mysql://localhost:3306/library_db"
   export LIBRARY_DB_USER="root"
   export LIBRARY_DB_PASSWORD="your_password"
   ```
3. Build and run with Maven:
   ```bash
   mvn clean package
   java -jar target/library-management-system.jar
   ```

## Sample Session
```
=== Library Management System ===
1. Add book
2. Register member
3. Issue book
4. Return book
5. List all books
6. Show overdue books
0. Exit
Choose an option: 1
Title: Clean Code
Author: Robert C. Martin
ISBN: 9780132350884
Number of copies: 3
Added: [1] "Clean Code" by Robert C. Martin (ISBN: 9780132350884) - 3/3 available
```

## Power BI Reporting
See `powerbi/README.md` — `sql/circulation_report.sql` provides the
monthly-checkouts-per-book and currently-overdue queries used to build
the circulation and overdue dashboards.
