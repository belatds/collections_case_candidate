# Data dictionary / Dicionário de dados

## `whatsapp_collections_history.csv` — one row per send attempt (Jun 1 – Aug 31, 2026)

| Column | Type | Description (EN) | Descrição (PT-BR) |
|---|---|---|---|
| `message_id` | id | Unique message id | Identificador da mensagem |
| `customer_id` | id | Customer id (joins to the queue file) | Identificador do cliente |
| `sent_at` | datetime | Send timestamp, local time, `YYYY-MM-DD HH:MM` | Data e hora do envio |
| `template` | cat | `friendly_reminder`, `urgent_reminder`, `discount_offer`, `pix_link` | Texto da mensagem |
| `n_msgs_last_14d` | int | Attempts sent to this customer in the previous 14 days (this one excluded) | Tentativas nos 14 dias anteriores |
| `days_past_due` | int | Days since the customer entered collections (1 = first day) | Dias de atraso |
| `outstanding_balance_brl` | float | Balance owed at send time (R$). Entry balances range 250–2,000; partial payments reduce it | Saldo devedor no momento do envio |
| `monthly_salary_brl` | float | Declared monthly salary (R$) | Salário mensal declarado |
| `payday_day_of_month` | int | Day of the month the customer is paid (1, 5, 10, 15, 20, 25 or 30) | Dia do pagamento do salário |
| `n_prior_transactions` | int | Number of credit transactions the customer had with us before this delinquency (loyalty) | Nº de transações anteriores (fidelidade) |
| `account_age_months` | int | Months since account opening | Idade da conta em meses |
| `days_since_last_app_login` | int | Days since the customer last opened the app, at send time | Dias desde o último login no app |
| `state_uf` | cat | Brazilian state | UF |
| `delivery_status` | cat | `delivered`, `failed_invalid_number`, `failed_blocked`, `failed_unreachable` | Status de entrega |
| `interaction` | cat | `none`, `read`, `replied`, `clicked_link` (always `none` when not delivered) | Interação do cliente |
| `paid_within_72h` | 0/1 | A payment (full or partial) happened within 72 h of this message | Houve pagamento em 72h |
| `amount_paid_brl` | float | Amount paid within 72 h (R$); 0 when no payment | Valor pago em 72h |

Notes
- Failed attempts are charged R$ 1.00 exactly like delivered ones.
- `discount_offer` settles the debt at 85% of the balance; a "full" payment under that template is therefore 0.85 × balance.
- A customer can pay part of the balance and remain in collections; a full payment ends the episode (no more messages).
- Episodes that were still open on Aug 31 are censored in this file and continue in the queue file.

## `collections_queue_sep2026.csv` — one row per customer eligible in September 2026

| Column | Type | Description |
|---|---|---|
| `customer_id` | id | Customer id (some appear in the history file, some are new) |
| `in_collections_since` | date | First eligible day. Dates in August = still open on Sep 1; dates in September = expected entry (due date known in advance) |
| `days_past_due_on_2026-09-01` | int | Days past due on Sep 1 (0 if the customer only enters during September) |
| `outstanding_balance_brl` | float | Balance owed on Sep 1 (or at entry) |
| `monthly_salary_brl`, `payday_day_of_month`, `n_prior_transactions`, `account_age_months`, `state_uf` | | Same definitions as above |
| `days_since_last_app_login` | int | As of Sep 1 (or at entry) |

## `plan.csv` — what you hand back

| Column | Constraint |
|---|---|
| `customer_id` | must exist in the queue file |
| `send_date` | `YYYY-MM-DD`, between 2026-09-01 and 2026-09-30, on or after `in_collections_since`, at most 60 days past due |
| `send_hour` | integer 9–20 |
| `template` | one of the four templates |
| rows | ≤ 10,000|
