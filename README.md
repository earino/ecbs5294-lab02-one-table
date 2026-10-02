# Lab 2 — The revenue the brief did not ask for

**ECBS5294 — Working with Data · Session 1, Block 2**

## Start here

| | |
|---|---|
| **The question** | What was the shop's revenue in 2010, and what did the average identified customer bring in, as finance's brief defines them? |
| **The file** | `data/raw/online_retail.parquet`: every invoice line of a UK online gift shop, 1 December 2009 to 9 December 2010. |
| **What is wrong** | The report runs without an error. Its numbers do not follow the brief, and one of them disagrees with the report's own table. |
| **What you hand in** | `DIAGNOSIS.md` on Moodle, before you leave. |
| **First thing to do** | Run the notebook top to bottom and write the report's three numbers down. Then hold each number against its sentence in the brief, below, clause by clause. |

## Get the project

If you cloned it during the stretch, you already have it. Otherwise, in your terminal (Git Bash on Windows,
Terminal on macOS), in the folder where you keep course work:

```bash
git clone https://github.com/earino/ecbs5294-lab02-one-table.git
cd ecbs5294-lab02-one-table
uv sync
```

Open **this folder** in VS Code (*File → Open Folder…*; trust the authors if asked), open `notebooks/report.ipynb`,
pick the `.venv` kernel, and run all cells. The first code cell prints the folder it is working in: it must be this
project's folder.

`notebooks/lecture.ipynb` holds the lecture's queries, as they were run in class, for review; it and
`data/raw/countries.csv` are not part of this lab. This lab is about `report.ipynb` and `online_retail.parquet`. One row of that file is one line of an invoice; `Price` is in pounds per unit.

## The brief

Finance wrote these definitions. They are not yours to change, and the data cannot tell you them: they are what the
numbers mean.

> **Sales invoices.** A sales invoice has a six-digit number. An invoice number that starts with a letter is not a
> sale: `C` marks a cancellation, and `A` an accounting adjustment (its description says *Adjust bad debt*).
>
> **Revenue** is the sum of `Quantity * Price` over the lines of sales invoices.
>
> **Identified revenue** is revenue from lines that carry a `Customer ID`. Lines with no `Customer ID` are
> *unidentified*: they are part of revenue, and part of no per-customer figure.
>
> **Average revenue per identified customer** is identified revenue divided by the number of distinct
> `Customer ID`s on those same lines.
>
> **2010** means every line whose `InvoiceDate` is in 2010.

## What is broken

Your colleague's report says that 2010 revenue was **£8,739,637.52**, that **4,258** identified customers bought, and
that the average identified customer brought in **£2,052.52**.

Two things disagree with it.

- **The report's own table.** Section 3 of the report lists every identified customer with their 2010 revenue, one
  row per customer. Take the average of its revenue column. It is not £2,052.52, and both numbers claim to be the
  average identified customer's revenue.
- **The brief.** Every number in the report has a sentence in the brief. The method: check each number against its
  sentence, clause by clause. Which clauses does the query do? Which one did it skip? A number is right only when its
  query does all of them, and nothing more.

Nothing errors. Find out why, before you change anything.

## What you must produce

Work in the notebook, under **Your work starts here**, top to bottom:

1. **Inspect** (section A). Run the three supplied queries. Paste what they showed you into `DIAGNOSIS.md`, part 3,
   **before you change anything**. Then name, for each number in the report, the clause of its sentence that the
   query does not do.
2. **The lines that count** (section B). Write the brief's **whole** filter once, as a view `sales_2010`: which
   invoices are sales, *and* which lines are 2010. Every later query reads from it, so it needs both. Run the census
   again on your view and read every row it kept.
3. **Revenue by month, 2010** (section C). Write this query yourself, from the empty cell. Say how many rows you
   expect **before** you run it, and order the rows by month.
4. **The five countries with the most sales invoices in 2010** (section D), each with its number of invoices and its
   revenue, most invoices first. From the empty cell; say how many rows you expect first. Then one sentence: what is one
   row of your result, and what is one row of the view it reads? The count is **invoices, not lines**.
5. **The check** (section E). Two checks, both to the penny — `abs(a - b) < 0.005`, never `==`. First,
   your view against the raw file: the revenue of all 2010 lines, minus the revenue of the lines whose invoice starts
   with a letter, equals your revenue. Second, your months against your view: the twelve months add up to your
   revenue. **When both differences print `£0.00`, your numbers are done.**
6. **`DIAGNOSIS.md`**, all five parts, short, **before section F**. Then the last ten minutes, below, and the Moodle
   checkpoint.
7. **After the note, if there is time: the per-customer figure** (section F). Its top and its bottom from the same
   lines, the unidentified share beside it, and the per-customer table that proves it. It is also Homework 1's
   question 7: if the lab ends before you reach it, you write it there.

## Rules

- **Never edit `data/raw/`.** The raw data is the evidence. Fix the queries.
- **The brief is the definition.** Do not write your own. If a number looks odd but follows its sentence, it is right.
- Fix the *cause*: which lines each sentence counts. A filter that makes the numbers look plausible is not the brief's
  filter.
- **Round when you display, never while you are adding up.** A `ROUND` inside a sum you then compare moves the
  difference off zero, and the check fails for a reason that is not in the data.
- Your view may carry a revenue column (`SELECT *, Quantity * Price AS revenue`), so later queries say `SUM(revenue)`.
  Either way is fine; the numbers are the same.
- You may use AI to explain an error or a function. You must be able to explain every line you hand in: your
  neighbour will ask, at minute 33, without notes.

## Hints, if stuck

Staff will say these over the room at minutes 5, 10, and 15. Read them earlier if you want. The syntax you need is on
one page: `REFERENCE-sql.md`. The full reference, with a task index in plain English, is on the course site:
https://earino.github.io/ecbs5294-2026/site/reference.html

1. Run inspection query 1 and read every row of it beside the brief's first sentence. Which of those rows does the
   report's revenue add up? That query's `left(Invoice, 1)` is the piece your view's filter is built from.
2. The average has a top and a bottom. Which lines does the `SUM` in report section 1 add up, and which lines can
   the `COUNT(DISTINCT "Customer ID")` in report section 2 see? Inspection query 3 counts both kinds of line. The
   report's own table (section 3) has the same invoice-number fault, so its average is not the answer either.
3. The report's revenue has no condition on the invoice number at all, so the cancellation and adjustment lines are
   netted into it. And its average divides revenue from every line, including lines with no customer, by a count of
   customers, who exist only on the lines that have one.

## Diagnosis note

In `DIAGNOSIS.md`: the template is there. Write it after section E, before section F. There are two failures; one
note with both causes in part 2 is fine, even if you have not repaired the second yet. Part 5 is section E's output:
paste it. If you reach section F, add its output to part 5.

## Stretch task

(a) Put your view's filter beside `WHERE Quantity > 0`. How many 2010 lines does each remove? List the lines that one
removes and the other keeps. (b) From your view: the ten products by units sold in 2010, beside the ten by number of lines. What is
a line, and what is a unit? (c) The brief names the two letters that exist today. Next year an invoice number starting
with `D` appears. Does your filter still follow the brief's first sentence? Write a filter that keeps only the
six-digit numbers, and count what each filter keeps today: the two counts must agree.

## Git thread

Commit after your view works, and again after the check closes. The message says what the cause was: "Count only
sales invoices in revenue: C cancellations and A adjustments were netted in" — not "fix".

## The last ten minutes

At minute 33, finished or not, turn to the person next to you (three if the row is odd). One of you explains, about a
minute: what was wrong, why, the query that proved it, what you changed, how you know it is right. Point at the
screen; do not read the note. The other asks:

1. **Show me the query that proves it.**
2. **Why was it wrong, not just where?**
3. **The what-if question on the slide.**

Then swap. If either of you is unsure, or you disagree, put a hand up: staff come to you first. Then the answer to
the what-if, for everyone. An unfinished repair is explained the same way: what you found so far.

Before you leave: the lab's **checkpoint on Moodle**. Upload `DIAGNOSIS.md` with its first line filled in. That is
what "complete" means; nobody signs you off.

## If you got lost: how to reset

Both of these **destroy work**. Read before running.

**Discard uncommitted changes (destructive)** — throw away edits and new files; keep your commits:

```bash
git restore --staged --worktree .    # every tracked file back to the last commit, staged or not
git clean -fd                        # and remove new, untracked files
```

> ⚠️ Permanently deletes uncommitted changes, staged or not, and any new untracked files.

**Full reset to the starter state (destructive)** — back to exactly what you cloned; throws away your commits too:

```bash
git reset --hard origin/main
git clean -fdx
```

> ⚠️ Discards your local commits and uncommitted changes. The `-x` also removes every ignored file — `data/silver/`, the
> `.venv/` environment, and anything else `.gitignore` lists, such as `.vscode/` and `.env` — so the folder matches a fresh clone. `uv sync` rebuilds the environment in a minute.
