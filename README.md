# Purchasing Payment Pipeline

A SQL pipeline that brings together five data sources owned by different teams
into one clean model, so the purchasing payment flow can be followed end to end
in a single Power BI dashboard.

> 🚧 **Work in progress.** See [Status](#status) for what is done and what is next.

> The data here is synthetic. It keeps the structure and the typical defects of
> the real sources (different formats, inconsistent names, duplicates), but every
> supplier, person, value and document number was generated.

<!-- TODO: once the dashboard is ready, add the screenshot here:
![Payment dashboard](docs/payment_dashboard.png)
-->

---

## The problem

Paying a supplier for a tooling or service investment is the last step of a long
chain, and each link of that chain lives with a different team, in a different
file and a different format.

<!-- TODO: replace with the real five sources (generic names, no company data) -->
| # | Source | Owned by | What it holds |
|---|---|---|---|
| 1 | Budget approvals | Finance / controlling | approved investments and amounts |
| 2 | Purchase requisitions | Purchasing admin | requisitions created in the ERP |
| 3 | Purchase orders | Buyers | orders, suppliers and values |
| 4 | Goods / service receipts | Requesting areas | what was delivered and accepted |
| 5 | Invoices and payments | Accounts payable | invoices received and payment dates |

None of these sources share a clean key, supplier names are written in different
ways in each one, and dates and amounts arrive in mixed formats. Answering simple
questions meant opening five files and matching them by hand:

- **Where is each payment stuck**, and with which team?
- **How long does it take** from approved budget to paid invoice?
- **Which suppliers and steps cause most of the delay?**
- **How much is due to be paid** this month, and how much is overdue?

---

## What the pipeline does

```
5 source files (one per team)
    │
    │  load          land each file as it is, change nothing
    ▼
stg_* tables         one staging table per source
    │
    │  clean (SQL)   standardize dates, amounts and supplier names,
    │                remove duplicates, build the keys that link the sources
    ▼
clean_* tables       trustworthy rows
    │
    │  model (SQL)   one row per payment flow, from budget to payment
    ▼
fact_payment_flow    + dimension tables (supplier, team, step, calendar)
    │
    │  views (SQL)   business rules: current step, lead time per step,
    │                on time / delayed, aging of open payments
    ▼
Power BI             reads the views
```

### Why SQL

The hard part of this project is not the volume, it is the **integration**:
five sources that were never meant to be joined. SQL keeps every cleaning rule
and every business rule readable in one place. Changing what counts as
"delayed" is a change to one `CASE` statement, not to a hidden spreadsheet
formula.

---

## The dashboard

<!-- TODO: add 2–3 screenshots in docs/ and describe each page in one line -->

| Page | Question it answers |
|---|---|
| Overview | How many payment flows are open, where they are, how many are late |
| Lead time by step | Which step takes longest, and how that changes month to month |
| Suppliers and teams | Who concentrates the delays and the open amounts |
| Payment aging | What is due this month and what is already overdue |

The report file itself is not published; the screenshots use synthetic data.

---

## Insights

<!-- TODO: after running the pipeline on the synthetic data, write the 3–4
     findings the dashboard shows, one line each. Example format:
- **X% of the lead time** is spent between goods receipt and invoice entry.
- **3 suppliers** concentrate most of the overdue amount.
-->

---

## Layout

<!-- TODO: update when the files are in the repository -->
```
data/raw/            synthetic source files (one per team)
sql/01_staging.sql   staging tables
sql/02_clean.sql     cleaning and standardization rules
sql/03_model.sql     fact and dimension tables
sql/04_views.sql     business rules and reporting views
docs/                dashboard screenshots
```

---

## Status

- [x] Problem and data model defined
- [ ] Synthetic source files
- [ ] Staging and cleaning in SQL
- [ ] Integrated model and reporting views
- [ ] Power BI dashboard and screenshots
- [ ] Insights written up

---

**Author:** Lucas Fernandes Nunes · [LinkedIn](https://www.linkedin.com/in/lucasfernandesnunes) · [Portfolio](https://lucasnunesf.notion.site/Lucas-Fernandes-Nunes-3de917c89c178092885cf639b619767b?pvs=143)
