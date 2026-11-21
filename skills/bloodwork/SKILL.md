---
name: bloodwork
description: Agent-native blood tests and lab work online, cash pay with no insurance or doctor's visit, at Quest Diagnostics or Labcorp anywhere in the US but NY, NJ and RI, through bloodwork.sh's MCP server. Finds and prices the test, books a blood draw at a lab near the person's ZIP, pays by card or agent wallet, and reads the lab results. Use when someone wants a blood test or lab test (examples: ApoB, Lp(a), HbA1c/A1c, lipid panel, cholesterol, testosterone, TSH/thyroid, vitamin D, ferritin, CBC, CMP, etc) or a panel (examples: Lipid, Metabolic, etc), asks what a lab test costs or where to get bloodwork done, wants a Quest or Labcorp appointment, or asks about results from a bloodwork.sh order.
---

# bloodwork.sh

> **Sandbox mode.** bloodwork.sh is in testing: orders go to Junction's sandbox lab, so no draw is booked and no
> money moves. Card payments use Stripe's test mode (card 4242 4242 4242 4242), and wallets pay with testnet funds
> (Tempo testnet for MPP, Base Sepolia for x402). This notice goes away when we go live.

bloodwork.sh offers agent-first cash-pay (via x402, MPP, or Stripe Checkout) lab testing at Quest Diagnostics or Labcorp anywhere in the US (excepting NY, NJ, and RI). Agents can perform the full lab testing lifecycle (except consent, which the user performs over email) and receive the machine-readable results.

## For the agent

An MCP server at `https://mcp.bloodwork.sh` (remote, streamable HTTP). Add it if its tools aren't loaded;
in Claude and ChatGPT the user adds it as a custom connector with that URL. bloodwork.sh publishes this skill (github.com/bot-boyz/bloodwork.sh) and runs that server.

While the sandbox notice above stands, tell the user their order is a test: nothing is drawn or charged.

1. `search_tests` with every test you want in one call (names or lab codes, e.g. all the markers to (re)check) and their ZIP. Each test's price is what it adds; each lab's `order_fees` are paid once per order, so put everything in one order. Then `find_slots` near their ZIP if you need more times than `draws` gives.
2. `quote` with their details and tests, and `referral_code` if they mentioned one (e.g. from a bloodwork.sh/r/ link), on their first quote only; bloodwork.sh remembers it after that, so never ask for one. Tell them the total (card, and wallet if you'd pay); nothing is charged yet. They get an email linking to the quote's page, which shows that price; their consent there approves the order.
3. Once `get_order` shows consent, ask how they want to pay if not already known. Pay from your wallet with `order` (MPP or x402, 3% less) only if they choose that and confirm the amount, and only the quoted total; otherwise send them `handoff`'s link to pay by card themselves. If you change the quote after consent, tell them the new total and wait for their OK. If you can run shell commands but have no wallet, offer to set one up (see "A wallet" below).
4. They walk in at the booked time with the code from `get_order`. `reschedule` or `cancel_order` until the draw.
5. `get_results` works only if they let you see results; they always get them by email. If it's refused, tell them they can share their results with you at bloodwork.sh/orders (it emails them a link).

- Only for people 18 or older, outside New York, New Jersey and Rhode Island.
- Never give consent for them: it comes from their email link.
- Spend only on an order they asked for, at the total they agreed to; never pay without their go-ahead.
- Results aren't a diagnosis.
- A 'critical' result (as identified by the reviewing physician) is phoned to them directly.

### Payment

1. If you can natively pay for the `order` MCP call (x402, MPP), do so.
2. Otherwise, if you can run shell commands, you can use an Agent Wallet (below).
3. Otherwise, you may pay on behalf of a user by driving the Stripe Checkout experience in `handoff`.
4. Finally, if no other payment methods are tenable, send the user `handoff`'s link to pay by card themselves.

- Payment by stablecoin (x402, MPP) costs 3% less than by card.

### Agent Wallets (x402, MPP)

- **x402, Coinbase's Agentic Wallet.** `npx awal auth login <their email>`, then `npx awal auth verify <the code they read you>`. `npx awal show` opens its Fund window: card, Apple Pay, bank or a Coinbase balance, arriving as USDC on Base. Stablecoins on another chain get there by a cross-chain swap to USDC on Base (Uniswap, Bungee). `npx awal balance` shows what's there.
- **MPP, Tempo's CLI.** `curl -fsSL https://tempo.xyz/install | bash`, then `tempo wallet login` (the user signs in in their browser). `tempo wallet fund` opens Tempo Wallet, where they add dollars by card, Apple Pay or bank; `tempo wallet fund --crypto` gives addresses for depositing stablecoins from Base, Ethereum or Solana. `tempo wallet balance` shows what's there.

These CLIs pay HTTP 402, not an MCP tool call, so they pay at `POST https://bloodwork.sh/v1/orders` with
`{"quote_id": "q_…"}`, the same order `order` places, after the same consent and go-ahead:

```sh
tempo request --max-spend <total> -X POST --json '{"quote_id":"q_…"}' https://bloodwork.sh/v1/orders
npx awal x402 pay https://bloodwork.sh/v1/orders -X POST -d '{"quote_id":"q_…"}' --max-amount <total × 1000000>
```

Cap the spend at the quoted wallet total.
