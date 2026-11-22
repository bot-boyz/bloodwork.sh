# bloodwork.sh

[![skills.sh](https://skills.sh/b/bot-boyz/bloodwork.sh)](https://skills.sh/bot-boyz/bloodwork.sh)

Tell your agent "test my ApoB" and it does the rest. It finds the test at Quest or Labcorp, books a draw at a center
near you, and pays from its wallet or hands you a card checkout. You walk in, give blood, and the results come to you
by email. Let your agent read them too, then track your numbers over time with whichever model you like.

[bloodwork.sh](https://bloodwork.sh) is lab testing at the API level, built for people who already run their health
through an agent and would rather have the raw numbers than one more health app.

- **Priced before you commit.** One `search_tests` call takes your whole list ("everything from last time") and
  returns each test's price, each lab's once-per-order fees, and the nearest open draw times. Nobody gives a name until
  they want a quote.
- **Your data stays with you.** Results go from the lab to you, and to your agent only if you say so. We keep no
  results and delete contact details on a schedule.
- **Works with your agent.** Claude, ChatGPT, Codex, Cursor or anything else that speaks MCP. Agents pay over MPP or
  x402 (3% less than card), or send you a checkout link.

```sh
npx skills add bot-boyz/bloodwork.sh -g -y
```

| Skill                                    | What it does                                                                                                       |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| [`bloodwork`](skills/bloodwork/SKILL.md) | Finds the test, books a draw near the person, takes payment from the agent's wallet or by card, and reads results. |

The MCP server is `https://mcp.bloodwork.sh`. You always give consent yourself, from a link in your email. For people
18 and over, in every US state except New York, New Jersey and Rhode Island.
