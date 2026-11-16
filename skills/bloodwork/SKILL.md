---
name: bloodwork
description: Order cash-pay blood tests at Quest or Labcorp for a person in the US through bloodwork.sh's MCP server — find the test, book a draw near their ZIP, pay, and read the results. Use when someone wants a lab test (ApoB, HbA1c, testosterone, a panel), asks what one costs, or asks about results from a bloodwork.sh order.
---

# bloodwork.sh

An MCP server at `https://mcp.bloodwork.sh` (remote, streamable HTTP, at the root). Add it if its tools aren't loaded;
in Claude and ChatGPT the person adds it as a custom connector with that URL.

1. `search_tests` for each lab's code, then `find_slots` near the person's ZIP.
2. `quote` with their details and tests. Tell them the total; they get an email to give consent.
3. Once `get_order` shows consent, pay with `order` (MPP or x402, 3% less) or send them `handoff`'s link to pay by card.
4. They walk in at the booked time with the code from `get_order`. `reschedule` or `cancel_order` until the draw.
5. `get_results` works only if they let you see results; they always get them by email.

- Only for people 18 or older, outside New York, New Jersey and Rhode Island.
- Never give consent for them: it comes from their email link.
- Results aren't a diagnosis. A critical result is phoned to them by the ordering physician; for meaning, their doctor.
