# BIAN Model — composite SML repository (Cards + Deposits)

Composite model `bian_model` (label **BIAN Model**) built from two component models — `bian_cards` and `bian_deposits` — that share the conformed Party, Product, Branch, Currency, Channel and Date dimensions. Product Dimension carries the BIAN Service Landscape classification (Business Area > Business Domain > Service Domain > Product).

## Inputs
- `context/use_case.md` — the chat request (verbatim), scope, personas and 12 NLQs (NLQs are embedded in the use case).
- `context/ddl.sql` — Snowflake DDL for 15 tables, derived from BIAN Semantic API r14.0.0 control records and behavior qualifiers.
- `context/erd.mmd` — Mermaid ERD with every FK relationship.
- `context/bian_service_domains.csv` — all 347 BIAN service domains: 326 from the v11 value-chain landscape (with Business Area / Business Domain) plus 21 published as semantic APIs in r12–r14.
- `context/bian_semantic_api_extract.json` — control record, BQ and attribute extract for the in-scope service domains (github.com/bian-official/public, release14.0.0).
- `context/build.yaml` — build parameters stated in the request plus defaults.
- Data profile — (not provided). No live data was profiled.
- Target warehouse — Snowflake (default; see assumptions).

## Build parameters
- `unrelated_dimension_handling`: repeat
- `warehouse`: Snowflake
- `model_unique_name`: bian_model (composite) — components `bian_cards`, `bian_deposits`
- `catalog_unique_name`: bian_model_catalog
- `currency`: USD
- `time_window`: calendar (Gregorian Year > Quarter > Month > Day)
- `use_cases_covered`: Cards, Deposits
- `use_cases_excluded`: Loans, Mortgages, Leasing, Trade Banking, Investments, Market Operations and all non-product business areas
- `semi_additive_default`: `last` over the Snapshot Date relationship for every balance metric on a snapshot fact

## Assumptions and decisions
- No DDL was supplied. The physical schema was derived from BIAN r14 semantic APIs — every column maps to a control-record attribute, a behavior qualifier, or a `*Reference` to a collaborating service domain (see `context/bian_semantic_api_extract.json`).
- Warehouse defaulted to Snowflake with `database: BIAN_DEMO`, `schema: BIAN`, `as_connection: Snowflake` (Rule 17: concrete identifiers). Override to match the target AtScale data warehouse connection id.
- Identifiers are UPPERCASE (Snowflake storage case, Rule 1).
- `is_unique_key` omitted on every level because no data profile was supplied (Rule 16).
- Non-leaf hierarchy levels use composite keys that include their parent column(s) so a member cannot belong to two parents.
- BIAN landscape classification denormalized onto DIM_PRODUCT instead of a snowflaked service-domain table — one hierarchy, no bridge levels (Rules 5, 9, 11 not triggered).
- Fact-resident attributes (transaction type, authorization decision / decline reason, entry mode, delinquency bucket, card case type/status/outcome, debit/credit indicator) modelled as degenerate dimensions with `is_degenerate: true`, listed in each model's `dimensions:` block (Rule 12).
- Card account, issued device and deposit account join only from facts (no embedded Party/Product relationships) to avoid a second, ambiguous path to the conformed Party and Product dimensions.
- Prior-year calcs use `ParallelPeriod` with a `PRIOR_YEAR_NUM` calculated column and `parallel_periods` on Quarter and Month (Rule 13). Ratios use `Divide` (MDX whitelist).
- Role-play prefixes: Snapshot, Transaction, Posting, Value, Case Opened, Case Closed. Cards and Deposits both use `Snapshot {0}` and `Transaction {0}`, so composite calcs line up on a common time axis.
- Composite-only calcs (6) live in `calculations/` and are referenced only by `models/bian_model.yml`.
- Service domains first published after v11 have no Business Area / Business Domain in the source landscape; they are labelled "(post-v11)" rather than guessed.

## Generation summary
- Datasets: 15 (5 facts, 10 dimension tables)
- Dimensions: 16 (10 standard incl. time, 6 degenerate)
- Base metrics: 49 — Cards 30, Deposits 19
- Calculations: 28 — Cards 13, Deposits 9, Composite 6
- Model relationships: Cards 21, Deposits 14
- Calculated columns: PRIOR_YEAR_NUM, DELINQUENT_BALANCE_AMT, REVOLVING_ACCOUNT_FLAG, APPROVED_FLAG, PURCHASE_AMT, RATE_WEIGHTED_BALANCE_AMT, DORMANT_BALANCE_AMT, CREDIT_AMT, DEBIT_AMT
- Caveat: the tables do not exist yet. Load data matching `context/ddl.sql` (or adjust datasets to an existing schema) before publishing.

## Reproducing this build
`context/` holds verbatim copies of every input used (use case with NLQs, DDL, ERD, BIAN landscape extract, BIAN semantic API extract and build.yaml). Re-running the AtScale SML model generator against those inputs and build parameters should produce an equivalent model.
