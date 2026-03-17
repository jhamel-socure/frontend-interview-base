# Frontend Interview — Transaction Dashboard

**Exercise instructions:** The full prompt, requirements, and evaluation criteria are in **[PROMPT.md](PROMPT.md)**. Read that for your task; this README covers how to run the app and use the API.

**Node:** Use Node 20. If you use [nvm](https://github.com/nvm-sh/nvm), run `nvm use` in this repo. [Volta](https://volta.sh/) will pick up Node 20 automatically.

## Run the app

```bash
npm install
npm run dev
```

No separate API server. The app uses a mock API (MSW) that runs in the browser. Data resets on a full page refresh.

---

## API reference

Base path: `/api/transactions` (same origin as the app).

### GET /api/transactions

Returns the list for the overview.

**Response:**

```json
{
  "transactions": [
    {
      "id": "txn_1024",
      "createdAt": "2026-03-10T14:23:11Z",
      "customerName": "Jane Cooper",
      "email": "jane@example.com",
      "amount": 249.99,
      "currency": "USD",
      "merchant": "Acme Electronics",
      "status": "needs_review",
      "riskScore": 87,
      "riskLevel": "high",
      "paymentMethod": "visa",
      "last4": "4242",
      "location": "Austin, TX",
      "hasChargeback": false
    }
  ],
  "total": 20
}
```

`status` is one of: `approved`, `declined`, `needs_review`, `reviewed`.  
`riskLevel` is derived from `riskScore`: &lt; 50 = low, 50–70 = medium, &gt; 70 = high. Some fields (e.g. `location`) may be `null`.

### GET /api/transactions/:id

Returns full detail for one transaction (addresses, signals, notes, `availableActions`, etc.).

### PATCH /api/transactions/:id

Apply an action and get the updated transaction back.

**Request body:**

```json
{ "action": "approve" }
```

`action` must be one of: `approve`, `decline`, `mark_reviewed`. Only actions listed in the transaction’s `availableActions` (from GET detail) are allowed. Response is the updated full transaction object.

**Behavior:** `approve` → status `approved`; `decline` → `declined`; `mark_reviewed` → `reviewed`. After an action, `availableActions` updates (e.g. `needs_review` → all three actions; `approved`/`declined` → none).

---

## Seeing the data shape

- **List:** Open `/` — the page shows the raw list response JSON.
- **Detail:** Go to `/transactions/:id` (e.g. `/transactions/txn_1024`) to see the raw detail response. The detail page also notes how to call PATCH.

TypeScript types for these shapes live in `src/types/transactions.ts`.
