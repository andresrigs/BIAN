# BIAN Model — Cards and Deposits (use case)

Source request (chat, verbatim): "Create a new Composite SML Model and call it a BIAN Model. Graph all BIAN Service domains from the BIAN website. SML model should include cards and deposits only. Enrich model with all the relationships and attributes."

## Domain grounding
* BIAN Service Landscape (Business Area > Business Domain > Service Domain) — v11 value-chain landscape, 326 service domains (see `bian_service_domains.csv`), plus 21 service domains first published as semantic APIs in releases 12–14.
* BIAN Semantic APIs release 14.0.0 (github.com/bian-official/public) — control records (CR) and behavior qualifiers (BQ) of the in-scope service domains (see `bian_semantic_api_extract.json`). Every table/column in `ddl.sql` maps to a CR attribute, a BQ, or a `*Reference` to a collaborating service domain.

## In scope
**Cards** — Credit Card (CreditCardFacility; CreditPlan, Billing, Interest, Charge, Repayment, CardPayment, IssuedDevice BQs), Credit Card Position Keeping, Card Authorization (Authorization, FraudCheck BQs), Card Transaction Capture, Card Case (Chargeback, Arbitration, Resolution BQs), Card Collections, Issued Device Administration, Merchant Relations / Merchant Acquiring Facility, Card Network Participant Facility, Reward Points Account.
**Deposits** — Current Account, Savings Account, Term Deposit (incl. Rollover BQ), Corporate Current Account (AmountBlock, Booking, Charge, DebitandCredit, Deposit, Interest, Payment, Sweep BQs), Position Keeping (FinancialPositionLog).
**Conformed reference** — Party Reference Data Directory, Product Directory, Branch Location Management, currency, channel, calendar.

## Personas
Head of Cards, Retail Deposits product manager, Credit risk / collections analyst, Fraud & disputes ops lead, Customer-relationship / finance analyst (composite).

## Natural-language questions (NLQs)
1. What is card credit utilization by card network and customer segment this quarter?
2. Which merchant categories drive card purchase volume and interchange yield?
3. What is the authorization approval rate and fraud rate by entry mode?
4. How has card transaction amount grown year over year by month?
5. What is the 30+ DPD delinquency rate by product and branch region?
6. What are the dispute rate, chargeback amount and average days to resolve card cases by case type?
7. What is the deposit ledger balance by BIAN service domain (Current, Savings, Term Deposit, Corporate Current)?
8. What is the weighted average deposit rate and YoY balance growth by product?
9. What is the net deposit flow year to date by channel?
10. What is overdraft utilization and dormancy rate by branch?
11. (Composite) For each customer segment, what are total balances, net liquidity (deposits minus card balances) and total fee income?
12. (Composite) What is the net interest contribution (card interest billed less deposit interest accrued) by month?
