
# PostgreSQL Partitioning – Complete Interview Kit
> (c) Venkata Bhattaram / TINITIATE

This document is a comprehensive interview-ready reference covering PostgreSQL internals and operational topics, including:
- Updates and MVCC
- Partitions and partition maintenance
- Leaf nodes and B-tree internals
- ANALYZE and VACUUM (types, tuning, best practices)
- XID (Transaction IDs) and wraparound
- Partition indexes, pruning, bloat & vacuum
- Performance tuning and advanced features
- Diagrams and practical commands

---

## Table of Contents

1. Updates & MVCC  
2. Partitions (overview)  
3. Leaf nodes (B-tree index internals)  
4. ANALYZE, VACUUM – types and usage  
5. VACUUM deep dive, best practices (OLTP & heavy tables)  
6. XID (Transaction ID) explanation  
7. Partition maintenance guide  
8. Partition Indexes — Q&A  
9. Partition Pruning — Q&A  
10. Partition Bloat & Vacuum — Q&A  
11. Diagram: Partition Index Architecture (ASCII)  
12. Complete Interview Q&A (condensed)  
13. Useful commands & monitoring queries  
14. References & further reading

---

# 1. Updates & MVCC

## Q: How does PostgreSQL perform UPDATE internally?
- PostgreSQL implements **MVCC (Multi-Version Concurrency Control)**.
- An UPDATE does **not** modify the original tuple in-place. Instead:
  1. A new tuple (row version) is written with `xmin = <updating_txid>`.
  2. The old tuple's `xmax` is set to the updating transaction ID (logical deletion).
- Old tuples remain until VACUUM cleans them.
- Indexes may be updated: if the update changes indexed columns, the index entries are updated to point to the new tuple. If possible, HOT (Heap-Only Tuple) updates avoid index writes.

## Why updates cause bloat
- Dead tuples accumulate.
- Autovacuum must remove them; if autovacuum lags, table and index bloat occur.

## HOT updates
- HOT allows certain updates to be performed without updating indexes if the new tuple fits on the same page and no indexed column changed.
- Configure `fillfactor` to leave free space for HOT updates.

---

# 2. Partitions (overview)

## Partition types
- **RANGE**: continuous ranges (eg. date ranges).
- **LIST**: list of discrete values.
- **HASH**: distributes data evenly via hash.
- **Composite**: combinations of the above.

## Benefits
- Query performance via pruning.
- Easier maintenance (drop, detach, archive partitions).
- Reduced VACUUM scope (per-partition).
- Better indexing and manageability for time-series / large datasets.

## Leaf partitions
- Leaf partitions are the actual child tables storing rows.
- Only leaf partitions hold data; parent is logical.

---

# 3. Leaf nodes (B-tree index internals)

## B-tree structure
- Levels: root → internal nodes → leaf nodes.
- Leaf nodes contain actual index entries (key + TID pointing to heap tuple).
- When leaf nodes fill, they split, potentially increasing tree depth.

## Index maintenance and updates
- Updates that change indexed columns add new index entries pointing at new tuple; old index pointers remain until index cleanup.
- High update volumes cause index bloat; reindex required periodically.

---

# 4. ANALYZE, VACUUM — types and usage

## ANALYZE
- Collects column statistics into `pg_statistic`.
- Helps the planner choose efficient plans.
- Run after bulk loads, big updates/deletes, schema changes.

## VACUUM types
1. **VACUUM (standard)**:
   - Removes dead tuples, marks space as reusable.
   - Non-blocking to regular operations.
   - Does not shrink file size.
2. **VACUUM ANALYZE**:
   - VACUUM + ANALYZE; recommended after bulk changes.
3. **VACUUM FULL**:
   - Rewrites the entire table and compacts it.
   - Requires `ACCESS EXCLUSIVE` lock — **downtime** for the table.
   - Reclaims disk space physically.
4. **VACUUM FREEZE**:
   - Freezes tuple XIDs to avoid wraparound.
   - Used for very old rows / preventing XID wraparound.
5. **Autovacuum**:
   - Background worker to trigger VACUUM and ANALYZE automatically.

## When to use which
- Regular maintenance: autovacuum (tuned).
- After bulk deletes/inserts: VACUUM ANALYZE.
- Reclaim disk space urgently: VACUUM FULL (during maintenance window).
- Prevent XID wraparound: VACUUM FREEZE (or autovacuum will do freeze).

---

# 5. VACUUM deep dive, best practices (OLTP & heavy tables)

## How VACUUM works
- Removes dead tuples that are no longer visible to any active transaction snapshot.
- Marks space as reusable for future inserts.
- Updates visibility map and statistics.

## VACUUM impact on hot systems
- Standard VACUUM is non-blocking and safe in production.
- Autovacuum can consume IO; tune cost_limit and cost_delay as needed.

## Best practices for heavy OLTP tables
1. Tune autovacuum parameters globally and per-table (scale_factor, thresholds).
2. Reduce `autovacuum_vacuum_scale_factor` on hot tables (e.g., 0.01–0.02).
3. Increase `autovacuum_max_workers` and `autovacuum_work_mem` if you have many partitions / big tables.
4. Use `fillfactor` (e.g., 70–90%) to leave room for HOT updates.
5. Keep transactions short to allow MVCC cleanup.
6. Use `pg_repack` for online table rewrites (no exclusive lock) to reclaim space.
7. Prefer partition drop/detach over mass delete for historical data.
8. Monitor `pg_stat_user_tables` and `pg_stat_progress_vacuum`.

## How VACUUM affects high-transaction tables
- Prevents uncontrolled bloat, keeps queries performant.
- Aggressive autovacuum may contend with user IO; tune autovacuum cost-based throttling.
- Long-running transactions block tuple removal — avoid them.

---

# 6. XID (Transaction ID) explanation

## What is XID?
- A 32-bit identifier given to transactions.
- Stored in tuple headers as `xmin` and `xmax`.

## Visibility rules recap
- When transaction T starts, it builds a snapshot.
- A tuple is visible to T if:
  - `xmin` is committed and is older than T's snapshot cutoff.
  - `xmax` is null or is > snapshot cutoff or belongs to an aborted/ongoing transaction in a way that makes the tuple visible.

## XID wraparound risk
- 32-bit XIDs wrap after ~4.29 billion transactions.
- If tuples are not frozen (via VACUUM FREEZE), visibility becomes ambiguous after wraparound.
- Autovacuum freeze and VACUUM FREEZE are essential to prevent wraparound.

## Monitoring XID age
```sql
SELECT datname, age(datfrozenxid) FROM pg_database ORDER BY 2 DESC;
SELECT relname, age(relfrozenxid) FROM pg_class ORDER BY 2 DESC;
```

---

# 7. Partition maintenance guide

## 1. Create future partitions proactively
- For range partitions (e.g., monthly), create partitions for upcoming months.
- Avoid inserts into DEFAULT partition by ensuring partitions exist.

## 2. Detach and archive old partitions
- `ALTER TABLE ... DETACH PARTITION ...;`
- Then `DROP TABLE` or move the detached table to cold storage.
- Dropping partition is fast (metadata only).

## 3. Vacuum and analyze per partition
- Each partition is a table; autovacuum settings apply per partition.
- Consider lowering `autovacuum_vacuum_scale_factor` on hot partitions.

## 4. Indexing per partition
- Indexes exist per partition. Reindex and maintain them independently.

## 5. Pruning and query design
- Use queries that enable pruning (filter on partition key with simple ops).
- Avoid expressions on partition key that prevent pruning.

## 6. Repartitioning / data movement
- Use `ATTACH`/`DETACH` and `INSERT INTO ... SELECT` or `CREATE TABLE ... AS` for reorganization.
- For changing partition key, a rewrite/migration is typically required.

## 7. Bulk loads and deletes
- Bulk load directly into target partitions using `COPY`.
- For bulk purge, drop partitions rather than deleting millions of rows.

## 8. Partition size guidance
- Aim for 5M–50M rows per partition. Balance between too many partitions and too-large partitions.
- For time-series, daily or weekly partitions may be better than monthly for high-throughput systems.

---

# 8. Partition Indexes — Q&A (Condensed)

- Indexes are per-partition (local) by default — parent index definition creates child indexes.
- Global indexes historically were not supported; recent PG versions introduce experimental global options.
- Reindexing must be done per-partition (or use `REINDEX TABLE` which touches children).
- Unique constraints are enforced per-partition unless global unique index or other mechanism used.
- Missing index on a partition causes poor query performance; ensure consistent indexing across partitions or targeted indexes on hot partitions.

---

# 9. Partition Pruning — Q&A (Condensed)

## What is partition pruning?
- Eliminates partitions that cannot contain qualifying rows, reducing scans.

## When does pruning occur?
- Planning-time pruning and runtime pruning (runtime helps with bind parameters).

## When pruning fails
- Expressions on partition key, mismatched types, complex functions, use of DEFAULT partition.

## How to ensure pruning
- Filter on partition key using simple operators; avoid casts and functions on key column.

---

# 10. Partition Bloat & Vacuum — Q&A (Condensed)

## Why partitions bloat
- The same reasons as tables: many updates/deletes; autovacuum lag; long transactions; index churn.

## Vacuuming partitions
- `VACUUM` runs per partition. Tune autovacuum per-partition if necessary.
- For heavy OLTP partitions: use lower `autovacuum_vacuum_scale_factor`, increase workers.

## Reducing bloat
- Use `pg_repack` for online reclaiming.
- Use `VACUUM FULL` only on offline partitions.
- Use `fillfactor` and HOT updates to reduce index churn.

---

# 11. Diagram: Partition Index Architecture (ASCII)

```
                ┌─────────────────────────────┐
                │   Parent Partitioned Table  │
                │       sales (RANGE)         │
                └───────────────┬─────────────┘
                                │
   ┌────────────────────────────┼─────────────────────────────┐
   │                            │                             │
┌──▼───────────────┐     ┌──────▼──────────────┐     ┌────────▼───────────────┐
│ sales_2024_10     │     │ sales_2024_11       │     │ sales_2024_12           │
└──┬───────────────┘     └──────┬──────────────┘     └────────┬───────────────┘
   │                             │                             │
   │                             │                             │
Local Indexes             Local Indexes                 Local Indexes
(per partition)           (per partition)               (per partition)

   idx_sales_2024_10      idx_sales_2024_11            idx_sales_2024_12

         ┌──────────────────── Global Index (PG 15+) ─────────────────────┐
         │                                                                │
         └──────────►   idx_sales_amount_global (covers all partitions) ◄─┘
```

Notes:
- Local indexes exist on each partition.
- Global index support is limited and version-dependent.

---

# 12. Complete Interview Q&A (condensed full set)

> The following Q&A are ready to use in interviews.

### Q: Explain MVCC and how updates work in PostgreSQL.
A: (See Section 1 — MVCC and HOT updates summary.)

### Q: What is a partition and why use it?
A: (See Section 2 — partition types and benefits.)

### Q: How does partition pruning improve performance?
A: Planner removes unneeded partitions at plan time or runtime; reduces IO.

### Q: How do you maintain partitions?
A: Create future partitions, vacuum/analyze per partition, reindex per partition, detach/archive old partitions.

### Q: When should you run VACUUM FULL?
A: Only during maintenance windows when you need to reclaim disk immediately and can tolerate exclusive lock.

### Q: How does XID wraparound work and how to prevent it?
A: XID is 32-bit; autovacuum freeze or manual VACUUM FREEZE to convert XIDs to FrozenXID; monitor `age(datfrozenxid)`.

### Q: How do indexes work with partitions?
A: Parent index definition creates child indexes; reindex per partition; global indexes are limited.

### Q: How to handle large deletes in partitioned tables?
A: Drop or detach partitions instead of deleting rows.

---

# 13. Useful commands & monitoring queries

## Check autovacuum progress
```sql
SELECT * FROM pg_stat_activity WHERE query LIKE '%autovacuum%';
SELECT * FROM pg_stat_progress_vacuum;
```

## Check table statistics and vacuum age
```sql
SELECT relname, n_live_tup, n_dead_tup, last_vacuum, last_autovacuum, last_analyze, last_autoanalyze
FROM pg_stat_user_tables WHERE relname LIKE 'sales%';

SELECT datname, age(datfrozenxid) FROM pg_database ORDER BY 2 DESC;
SELECT relname, age(relfrozenxid) FROM pg_class ORDER BY 2 DESC;
```

## Check partition list for a parent table
```sql
SELECT inhrelid::regclass AS partition, inhparent::regclass AS parent
FROM pg_inherits WHERE inhparent = 'public.sales'::regclass;
```

## Show indexes and sizes (per partition)
```sql
SELECT
  schemaname, tablename, indexname, pg_size_pretty(index_size) AS idx_size
FROM (
  SELECT
    schemaname, tablename, indexname, pg_relation_size(indexrelid) AS index_size
  FROM pg_indexes
  JOIN pg_class ON tablename = relname
) sub
ORDER BY idx_size DESC LIMIT 50;
```

## Reindex concurrently on a partition
```sql
REINDEX INDEX CONCURRENTLY sales_2024_10_idx_sales_customer;
```

## Example: Create partitioned table and child
```sql
CREATE TABLE sales (
  id bigserial primary key,
  sale_date date not null,
  customer_id bigint,
  amount numeric,
  status text
) PARTITION BY RANGE (sale_date);

CREATE TABLE sales_2024_10 PARTITION OF sales
FOR VALUES FROM ('2024-10-01') TO ('2024-11-01');
```

---

# 14. References & further reading

- PostgreSQL official docs: Partitioning, VACUUM, MVCC, Indexes  
- pg_repack project  
- PostgresWiki: Autovacuum tuning  
- Blog posts from major PostgreSQL contributors (e.g., Peter Eisentraut, Hackers)

---

## End of document
