# Personal Finance Tracker (From Scratch) — Python

A personal finance engine that indexes the same transaction data in **three different data structures simultaneously**, demonstrating when and why to reach for each one.

## Features
-Transaction record: ID, date, amount, category, merchant, note
-HashMap indexed by category → O(1) average category-total lookup and per-category listing
-AVL Tree indexed by date → O(log n + k) date-range queries (handles duplicate dates)
-MaxHeap indexed by amount → O(log n) insert/delete, O(n log n) top-N surfacing
-All three structures stay synchronized on every add and delete
-Commands: add-transaction, delete, range, category-summary, top-spending, monthly-report
-Load historical data from CSV (≥ 300 records); export filtered results back to CSV
-Edge cases: same-date transactions, deleting a transaction in the heap's top-N, category with zero transactions
-Full complexity analysis: docs/complexity.md
-Hand-traced correctness verification: docs/manual_trace.md
-BONUS — Budget-limit feature: set a monthly cap per category with ≥ 80% usage warnings
-BONUS — Visual monthly breakdown (bar chart rendered in the terminal)

## Data Structures

```
HashMap   key=category  →  O(1) avg  total + listing
AVL Tree  key=date       →  O(log n)  range queries
MaxHeap   key=amount     →  O(log n)  insert/delete, O(n log n) top-N
```

Full justification: see `docs/complexity.md`.

## Project Structure

```
src/finance_tracker/
  __init__.py
  hashmap.py              Custom HashMap (string keys, chaining, auto-resize)
  avl_tree.py             Custom AVL Tree (date keys, duplicate-date support)
  max_heap.py             Custom MaxHeap (lazy deletion for O(1) delete)
  finance_tracker.py      Core engine — keeps all three in sync
  simulator.py            CLI interface

tests/
  test_hashmap.py
  test_avl_tree.py
  test_max_heap.py
  test_finance_tracker.py

data/
  transactions.csv        320 generated transactions (2024, 8 categories)

docs/
  complexity.md           Complexity analysis for every operation
  manual_trace.md         Step-by-step hand trace verifying correctness

pyproject.toml
pytest.ini
```

## Setup

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -U pip
pip install pytest
```

## Run Tests

```bash
PYTHONPATH=src pytest -q
```

## Run Simulator — Interactive Mode

```bash
PYTHONPATH=src python -m finance_tracker.simulator --csv data/transactions.csv --interactive
```

Interactive commands:

```
add <date> <amount> <category> <merchant> [note]
    e.g.  add 2024-03-15 42.50 food Starbucks coffee

delete <id>

range <date1> <date2> [min_amount]
    e.g.  range 2024-03-01 2024-03-31
          range 2024-03-01 2024-03-31 50

category-summary

top-spending <n>

monthly-report <YYYY-MM>
    e.g.  monthly-report 2024-03

set-budget <category> <limit>
    e.g.  set-budget food 200

export <filename.csv>
```

## Run Simulator — Report Mode (non-interactive)

```bash
PYTHONPATH=src python -m finance_tracker.simulator --csv data/transactions.csv --report 2024-03
```

## Example Session

```
finance> add 2024-03-15 42.50 food Starbucks coffee
  ✓ Added transaction #321: $42.50 at Starbucks [food] on 2024-03-15

finance> set-budget food 200
  ✓ Budget for 'food' set to $200.00/month

finance> monthly-report 2024-03
  ══════════════════════════════
  MONTHLY REPORT — 2024-03
  ══════════════════════════════
  Total spent : $1,842.17
  Transactions: 28
  ...
  ⚠  BUDGET WARNINGS:
    food: $213.40 / $200.00  (107%)

finance> top-spending 3
  ID    DATE        AMOUNT  CATEGORY  MERCHANT
  ...

finance> range 2024-03-01 2024-03-31 100
  (transactions in March over $100)
```

## Required Write-ups

- Manual trace (by hand): `docs/manual_trace.md`
- Complexity analysis: `docs/complexity.md`
