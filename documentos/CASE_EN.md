# Technical Case — Senior Data Scientist (Collections & CRM)

## Context

You have just joined the data team of a consumer-credit fintech. When a customer misses a payment
they enter the **collections queue** and start receiving WhatsApp reminders asking them to settle.
Every message costs **R$ 1.00**, **including the ones that fail** (invalid number, customer blocked
the sender, phone unreachable).

Over the last three months (June–August 2026) the operations team sent roughly
**25,000 messages per month**, with no clear rule about whom to contact, when, or with which text.
Your manager has just told you that from September the **monthly WhatsApp budget is R$ 10,000**
(i.e. at most 10,000 send attempts in the month).

Your mission: **decide how to spend those R$ 10,000 in September to maximise the amount
recovered while minimising failed attempts.**

## Rules of the game

| Rule | Value |
|---|---|
| Cost per send attempt (delivered or not) | R$ 1.00 |
| September 2026 budget | R$ 10,000 (≤ 10,000 attempts) |
| Sending window | Every day, **9 am to 9 pm** (send hour 9 through 20) |
| Cap per customer | 1 message per day |
| Eligibility | From the day the customer enters collections until 60 days past due |
| Templates available | `friendly_reminder`, `urgent_reminder`, `discount_offer` (settles at a 15% discount), `pix_link` |
| Revenue | Amount actually paid by the customer within 72 h of the message |

The sending system automatically drops customers who have already settled (those messages are
neither sent nor charged).

## Data

You receive two files (full dictionary in `data_dictionary.md`):

1. **`whatsapp_collections_history.csv`** — ~75k messages sent between 1 Jun and 31 Aug 2026 to
   ~11.7k customers. One row per send attempt, with the customer's state at that moment
   (outstanding balance, salary, payday, prior transaction count, app-login recency, …),
   the delivery status, the customer's interaction, and whether a payment happened within 72 h.

2. **`collections_queue_sep2026.csv`** — the ~10.7k customers who will be in collections in
   September: those still open on 1 Sep plus those whose due date falls during the month
   (`in_collections_since` is the date they become eligible). Same attributes, **no outcomes**:
   this is what you plan against.

The data is synthetic but built to behave like real collections data: there are clear signals,
there is noise, and there are traps. There is no hidden "right answer" — we want to see how you think.

## Deliverables

1. **Exploratory analysis and diagnosis** (notebook): what drives delivery, interaction and payment?
   Where was money wasted over the last three months?
2. **Model(s)**: whatever approach you find appropriate to estimate the expected value of a message
   (for a customer, on a day and hour, with a template). Justify choices, validation and metrics.
3. **September plan** — a `plan.csv` with at most 10,000 rows and the columns:

   ```
   customer_id,send_date,send_hour,template
   C000002,2026-09-02,19,pix_link
   ```
   (`send_date` as `YYYY-MM-DD`, `send_hour` an integer from 9 to 20.)
   We will **run your plan through a simulator** that reproduces customer behaviour and compare
   recovered revenue and failure rate with other plans.
4. **A deck of at most 10 slides** for the (non-technical) manager and the (technical) data team:
   how much you expect to recover, at what failure rate, what the levers are, and how you would
   prove in production that the plan works.

## How we evaluate

- Data manipulation and analytical rigour (joins, aggregations, leakage, confounding).
- Modelling quality and honest validation.
- Optimisation reasoning under a budget: whom to contact, when, with what, how many times.
- Business thinking: cost vs. failure vs. interaction vs. revenue; customer loyalty (does a long
  transaction history help or hurt?); what to measure afterwards.
- Clear communication to different audiences.

Suggested effort: 6–8 hours of work, 7-day deadline. The presentation is 45 minutes
(20 presenting + 25 Q&A). Any language or library is fine. If a rule is ambiguous, make a
reasonable assumption and state it.
