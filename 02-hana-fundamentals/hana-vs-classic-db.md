# SAP HANA vs Classic Databases (DB2 / Oracle)

What are fundamental differences between SAP HANA
and classic relational databases traditionally used with SAP ECC,
(DB2, Oracle)?

By understanding this we can understand why changes in architecture, custom code, and data models are required.


## Classic SAP Database Model (Pre-HANA)

Traditional SAP ECC systems typically run on relational databases such as:
- IBM DB2: layer that stores SAP business data (tables like BKPF, MSEG, MARA, etc.).
   Responds to SQL queries coming from the SAP application server.
   Main differences to HANA: it is disk‑based and row‑oriented (vs. in‑memory, column‑based)
- Oracle: stores SAP ECC business data and executes SQL statements sent by the SAP application server.
  Also disk-based, row-oriented.
- Microsoft SQL Server:

### Key Characteristics

- **Row-based storage**
  - Data is stored row by row
  - Optimized for transactional processing (OLTP)
- **Disk-based processing**
  - Data is read from disk into memory when needed
- **Heavy use of indexes**

 - Performance depends on secondary indexes
- **Database-agnostic SQL**
  - ABAP Open SQL designed to work across databases
- **Aggregates and redundant tables**
  - Pre-calculated totals (e.g. totals tables in FI/MM)

### Typical Consequences

- Performance tuning focuses on indexes and buffering
- Complex reporting logic handled in application server (ABAP)
- Data duplication to compensate for slower read performance

---

## SAP HANA Database Model

SAP HANA is an **in-memory**, **column-oriented** database
designed to handle both transactional (OLTP) and analytical (OLAP)
workloads in a single system.

### Key Characteristics

- **In-memory processing**
  - Data primarily resides in RAM
- **Column-based storage**
  - Optimized for reads, aggregations, and analytics
- **Massive parallelization**
  - Calculations executed in parallel at database level
- **Reduced reliance on indexes**
  - Many indexes become unnecessary or harmful
- **Push-down principle**
  - Logic is shifted from ABAP layer to the database

---

## Column Store vs Row Store (Simplified)

| Aspect | Classic DB | SAP HANA |
| Storage model | Row-based | Column-based |
| Read performance | Moderate | Very fast |
| Aggregations | Expensive | Native & fast |
| Index dependency | High | Low |
| Data compression | Limited | Very strong |

---

## Architectural Impact on SAP Systems

The move to HANA fundamentally changes how SAP systems are designed.

### 1. Push-Down Logic

Operations that were traditionally executed in ABAP are now executed
directly in the database:
- Aggregations
- Calculations
- Joins
- Filtering

Technologies enabling this:
- CDS Views
- AMDP
- Open SQL enhancements

---

### 2. Simplified Data Model

Because HANA handles aggregation efficiently:
- Totals tables are removed
- Redundant data is eliminated
- Real-time calculations replace stored totals

This leads directly to S/4HANA data model simplifications.

---

### 3. Performance Mindset Shift

Old mindset:
> “Add an index to improve performance”

HANA mindset:
> “Optimize the query and push logic to the database”

---

## Why SAP S/4HANA Requires Code Changes

Custom code written for classic databases often:
- Relies on SELECT *
- Accesses physical tables directly
- Uses database-specific SQL behavior
- Performs business logic in ABAP loops

On HANA:
- SELECT * is inefficient
- Certain SQL patterns are forbidden
- Direct access to replaced tables causes errors
- Poorly written code can negate HANA benefits

---

## Summary

SAP HANA is not just a faster database — it represents
a **fundamental architectural shift**.

Key takeaways:
- From disk → memory
- From row → column
- From ABAP-heavy → database-driven logic
- From redundancy → simplification

This shift is the technical foundation that makes SAP S/4HANA possible
and explains why system conversion is more than a database migration.

---

## Notes

This document is part of a learning and documentation project
focused on understanding SAP ECC to SAP S/4HANA transition concepts.
No proprietary SAP content is included.
