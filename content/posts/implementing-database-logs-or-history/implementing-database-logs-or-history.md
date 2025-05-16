+++
date = '2025-05-16T21:22:57+02:00'
draft = false
title = 'Implementing Database Logs or History'
+++

![Banner](/images/blog/implementing-database-logs-or-history/banner.png)

## Introduction

In the realm of database management, maintaining an accurate record of changes made to the database structure is paramount. This is where the concept of a database changelog comes into play. A changelog, also known as a record history or audit trail, serves as a chronological log of modifications made to the database schema or data. It plays a crucial role in ensuring data integrity, transparency, and accountability within an organization's data management processes.

## Importance of Changelog

The importance of a changelog cannot be overstated. Here are some key reasons why implementing a changelog is essential:

1. **Traceability**: A changelog provides a clear trail of changes made to the database over time, enabling administrators to trace back and understand the evolution of the database structure or data.
2. **Troubleshooting and Debugging**: In the event of errors or inconsistencies in the database, having a changelog facilitates the process of troubleshooting and debugging. It helps in identifying when and where the problem occurred, thereby expediting the resolution process.
3. **Compliance and Auditing**: Many industries are subject to regulatory compliance requirements that mandate the maintenance of detailed records of data changes. A changelog serves as evidence of compliance with such regulations and facilitates auditing processes.
4. **Risk Management**: By maintaining a comprehensive record of changes, organizations can mitigate the risks associated with unauthorized or erroneous modifications to the database. It promotes accountability and transparency in data management practices.

## Understanding Audit Tables

As part of implementing a database changelog, one common practice is the use of audit tables. An audit table is a specialized database table designed to store metadata about changes made to other tables in the database. It typically captures information such as the user who initiated the change, the timestamp of the modification, the type of operation (e.g., insert, update, delete), and the affected data.

To implement we can consider some options available:

- Live history
- Shadow history
- Generic history

### Live history

In the live history method, the original table, such as the product table, is modified to accommodate the recording of changes in real-time. Rather than updating existing records, a new record is inserted into the original table for every modification.

**Implementation Process:**

1. **Database Table Modification:** The original table structure is augmented to include additional fields such as `updated_by`, `timestamp`, `version`, and `is_active`.
2. **Insertion of New Records:** Whenever a modification operation (e.g., insert, update, delete) is performed on the data, instead of directly updating the existing record, a new record is inserted into the original table with the updated information. `is_active` field here is used to mark the record as deleted. So whenever a record is deleted, we will create new record in the database for the deleted record and mark is as `is_active=False`

3. **Tracking Changes:** Each new record includes metadata such as the user who initiated the change (`updated_by`), the timestamp of the modification (`timestamp`), and a version identifier (`version`). These fields allow for tracking and auditing of changes over time.

**Example:**

Suppose we have a product table with the following structure:

| product_id | name      | price |
| ---------- | --------- | ----- |
| 1          | Product A | 10.99 |
| 2          | Product B | 15.99 |

After implementing the live history method, the table structure is modified to include additional fields:

| product_id | name      | price | updated_by | timestamp           | version | is_active |
| ---------- | --------- | ----- | ---------- | ------------------- | ------- | --------- |
| 1          | Product A | 10.99 | User1      | 2024-02-23 10:30:00 | 1       | 1         |
| 2          | Product B | 15.99 | User2      | 2024-02-23 11:15:00 | 1       | 1         |
| 1          | Product A | 12.99 | User3      | 2024-02-23 12:00:00 | 2       | 1         |

In this example, when the price of "Product A" is updated from $10.99 to $12.99 by "User3", instead of modifying the existing record, a new record is inserted with the updated price and corresponding metadata.

**Advantages:**

- **No Additional Tables Needed:** The live history method utilizes the existing table structure, eliminating the need for additional tables and simplifying the database schema.

**Disadvantages:**

- **Complex Queries:** Retrieving the current state of data may require querying and filtering through multiple records with different versions and excluding inactive (deleted) records, which can add complexity to queries.
- **Usage of Complex Primary Keys:** Managing versioning and active status may require the usage of complex primary keys or surrogate primary keys, potentially complicating database design.
- **No Foreign Keys:** The absence of foreign keys in a live history implementation can impact data integrity and referential integrity constraints.

### Shadow Method

In the shadow method, a separate table, often named with a suffix like "\_audit" or "\_history", is created to store historical records of changes to the original table. When modifications are made to the original table, such as inserts, updates, or deletes, corresponding actions are taken on the shadow table to maintain a historical record.

**Implementation Process:**

1. **Creation of Audit Table:** A new table, typically named with a suffix indicating its purpose (e.g., `product_audit`), is created to store historical records of changes to the original table (e.g., `product`).

2. **Insertion of New Records:** When a new record is inserted into the original table, the record is only added to the original table (product table in our case). In this case audit table is not affected.

3. **Update Operations:** When an existing record in the original table is updated, a new record reflecting the updated information is inserted into the audit table, in this operation, no changes made to the product table.

4. **Deletion Operations:** Upon deletion of a record from the original table, the record is moved to the audit table to retain a historical record of the deletion.

**Visual Representation:**

Original `product` Table:

| product_id | name      | price | username | timestamp           |
| ---------- | --------- | ----- | -------- | ------------------- |
| 1          | Product A | 10.99 | User1    | 2024-02-23 10:00:00 |
| 2          | Product B | 15.99 | User2    | 2024-02-23 10:00:00 |

`product_audit` Table:

| product_id | name      | price | username | timestamp           |
| ---------- | --------- | ----- | -------- | ------------------- |
| 1          | Product A | 12.99 | User3    | 2024-02-23 12:00:00 |
| 2          | Product B | 17.99 | User1    | 2024-02-23 11:15:00 |

**Advantages:**

- **Performance:** Separating historical records into a dedicated table helps maintain performance as it avoids bloating the original table with historical data.
- **Easy Management:** Historical records are easily managed and can be queried separately from the main table, simplifying data management tasks.

**Disadvantages:**

- **Data Retrieval Complexity:** To view the full history of a record, it's necessary to combine data from both the original and audit tables, potentially adding complexity to queries.
- **Schema Changes:** Any structural changes to the original table may require corresponding changes to the audit table, increasing maintenance overhead.

### Generic History Method

In the generic history method, a single table, typically named something like `general_audit`, is used to store historical records of changes to multiple tables in the database. This centralizes the recording of changes and simplifies the management of historical data.

**Implementation Process:**

1. **Creation of Audit Table:** A single table, `general_audit`, is created with fields to store information about changes made to various tables in the database. The structure of this table typically includes fields such as `table_name`, `field_name`, `old_value`, `new_value`, `username`, and `timestamp`.

2. **Insert Operations:** When a new record is inserted into a table, such as the `product` table, no record is added to the `general_audit` table. The insertion is solely recorded within the respective table.

3. **Update Operations:** When an existing record in a table is updated, such as changing the price of a product, we update it in original table and also a new record is inserted into the `general_audit` table to capture the old and new values of the modified fields, along with metadata such as the username and timestamp.

4. **Delete Operations:** Upon deletion of a record from a table, the record is removed from the table (product table) itself. However, a new record is added to the `general_audit` table with `is_active=false` to indicate the deletion, and the `old_value` and `new_value` fields are set to null.

**Visual Representation:**

`general_audit` Table:

| table_name | field_name | row_key | old_value | new_value | username | timestamp           | is_active |
| ---------- | ---------- | ------- | --------- | --------- | -------- | ------------------- | --------- |
| product    | price      | 1       | 10.99     | 12.99     | User3    | 2024-02-23 12:00:00 | 1         |
| product    | price      | 1       | null      | null      | User4    | 2024-02-23 13:00:00 | 0         |

**Advantages:**

- **Single Audit Table:** Using only one table for audit purposes simplifies the management of historical records and reduces the complexity of the database schema.
- **Scalability:** The generic history approach can accommodate changes to any table in the database without the need for additional audit tables.

**Disadvantages:**

- **Large Field Sizes:** The `old_value` and `new_value` fields may need to be large character fields to accommodate changes for all tables with different field types, potentially increasing storage requirements.
- **Limited Record Visibility:** It may be challenging to view the complete history of a specific record from a single table, as the audit records are centralized and not directly associated with the original table.

### Conclusion:

I personally think that option 2 (Shadow history) seems to be better than the other options as it make it easy to read the history by having separate tables and also it is not affecting the performance in case we expect high number of data in our original table
