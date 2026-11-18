---
name: bloodwork
description: Order cash-pay blood tests at Quest or Labcorp for a person in the US through bloodwork.sh's MCP server — find the test, book a draw near their ZIP, pay, and read the results. Use when someone wants a lab test (ApoB, HbA1c, testosterone, a panel), asks what one costs, or asks about results from a bloodwork.sh order.
---

# bloodwork.sh

> **Sandbox mode.** bloodwork.sh is in testing: orders go to Junction's sandbox lab, so no draw is booked and no
> money moves. Card payments use Stripe's test mode (card 4242 4242 4242 4242), and wallets pay with testnet funds
> (Tempo testnet for MPP, Base Sepolia for x402). This notice goes away when we go live.

Tell your agent "test my ApoB" and it does the rest. It finds the test at Quest or Labcorp, books a draw at a center
near you, and pays from its wallet or hands you a card checkout. You walk in, give blood, and the results come to you
by email. Let your agent read them too, then track your numbers over time with whichever model you like.

Lab testing at the API level: one MCP server, about 1,800 orderable tests, and every price shown before anyone gives a
name. You never make an account, and we keep no results.

## For the agent

An MCP server at `https://mcp.bloodwork.sh` (remote, streamable HTTP, at the root). Add it if its tools aren't loaded;
in Claude and ChatGPT the person adds it as a custom connector with that URL. bloodwork.sh publishes this skill
(github.com/bot-boyz/bloodwork.sh) and runs that server.

While the sandbox notice above stands, tell the person their order is a test: nothing is drawn or charged.

1. `search_tests` with every test you want in one call (names or lab codes, e.g. all the markers to recheck) and their
   ZIP. Each test's price is what it adds; each lab's `order_fees` are paid once per order, so put everything in one
   order. Then `find_slots` near their ZIP if you need more times than `draws` gives.
2. `quote` with their details and tests, and `referral_code` if they mentioned one (e.g. from a bloodwork.sh/r/ link),
   on their first quote only; bloodwork.sh remembers it after that, so never ask for one. Tell them the total (card,
   and wallet if you'd pay); nothing is charged yet.
   They get an email linking to the quote's page, which shows that price; their consent there approves the order.
3. Once `get_order` shows consent, ask how they want to pay. Pay from your wallet with `order` (MPP or x402, 3% less)
   only if they choose that and confirm the amount, and only the quoted total; otherwise send them `handoff`'s link
   to pay by card themselves. If you change the quote after consent, tell them the new total and wait for their OK.
4. They walk in at the booked time with the code from `get_order`. `reschedule` or `cancel_order` until the draw.
5. `get_results` works only if they let you see results; they always get them by email.
   If it's refused, tell them they can share their results with you at bloodwork.sh/orders (it emails them a link).

- Only for people 18 or older, outside New York, New Jersey and Rhode Island.
- Never give consent for them: it comes from their email link.
- Spend only on an order they asked for, at the total they agreed to; never pay without their go-ahead.
- Results aren't a diagnosis. A critical result is phoned to them by the ordering physician; for meaning, their doctor.
