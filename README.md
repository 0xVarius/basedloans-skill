# Based Loans agent skill

**Borrow USDC against crypto on Base without liquidations, or lend USDC for fixed-term returns, from any AI agent.**

This is an [Agent Skill](https://agentskills.io) for [Based Loans](https://based.loans), a fixed-term lending protocol on Base. It teaches an agent how to quote collateral-backed USDC loans, check a token's risk before lending against it, read loan and lender positions, and prepare transactions for borrowing, full or partial buybacks, lending, withdrawals and claims. The user's own wallet signs every transaction.

It works with Claude Code, Claude Desktop, Cursor, Codex, Gemini CLI and other agents that support skills. Agents get live data through the Based Loans MCP server or HTTP API.

## How Based Loans works

- **Borrow:** deposit a token such as WETH, cbBTC, VIRTUAL or AERO as collateral and receive USDC right away. Buy it back at a fixed price any time before the due date.
- **No liquidations:** price moves during the loan never sell the collateral, and there are no margin calls. If you don't buy back by the due date, you keep the USDC and the lenders receive the collateral. Nothing else is owed.
- **Lend:** deposit USDC and choose which tokens you'll lend against, each with its own limit. Earn the fixed buyback cost when borrowers repay, or receive the collateral when they don't.

| | Current value (read live with `get_protocol_info`) |
|---|---|
| Network | Base mainnet |
| Max loan-to-value | 50% |
| Loan fee | 2.49%, taken out of the loan |
| Terms and buyback cost | 15 days +2.5%, 30 days +5%, 45 days +7.5% |
| Minimum loan / lender deposit | 50 USDC / 200 USDC |

## Install

### Claude Code (plugin: skill plus MCP server)

```
/plugin marketplace add 0xVarius/basedloans-skill
/plugin install based-loans@based-loans
```

This adds the skill and connects the hosted MCP server at `https://mcp.based.loans/mcp`.

### Any skills-compatible agent

```bash
npx skills add 0xVarius/basedloans-skill
```

The [skills CLI](https://skills.sh) installs into Claude Code, Cursor, Codex, Gemini CLI, OpenCode and others. Then connect the MCP server so the agent has live data:

```bash
# Claude Code
claude mcp add --transport http basedloans https://mcp.based.loans/mcp
```

```json
// Claude Desktop, Cursor and most MCP clients
{ "mcpServers": { "basedloans": { "url": "https://mcp.based.loans/mcp" } } }
```

Prefer running it locally? `npx -y @basedloans/mcp`. No MCP support at all? The skill includes an [HTTP API reference](skills/based-loans/references/http-api.md).

### Manual

Copy `skills/based-loans` into your agent's skills folder (for Claude Code, `~/.claude/skills/` or `.claude/skills/` in a project).

## Requirements

- **No API key, account or login.** The MCP server and HTTP API are public and read straight from Base.
- **A Base wallet** for transactions. The agent prepares unsigned transactions and the wallet signs them. A little ETH on Base pays for gas.
- **Never** give an agent a seed phrase or private key. This skill never asks for one.

## Example prompts

**Borrowers**

- "How much USDC can I borrow against 0.5 WETH for 30 days on Based Loans?"
- "What would it cost to buy back that loan, and when is it due?"
- "Which tokens can I borrow against on Based Loans right now?"
- "Show my Based Loans loans for 0x…"
- "Pay 40 USDC toward loan 199 and tell me how much collateral I get back."
- "Can I borrow against my HYDX? If not, how do I request it?"

**Lenders**

- "How risky is VIRTUAL to lend against on Based Loans? Explain the flags."
- "Deposit 500 USDC and lend up to 250 against WETH and 250 against cbBTC."
- "What's my lending position, and is anything ready to claim?"
- "Claim repaid USDC from my funded loans and put it back into lending."
- "Withdraw everything that isn't in active loans."

## What the skill covers

| Read-only (nothing is signed) | Transactions (the user's wallet approves each one) |
|---|---|
| Protocol fees, terms and minimums | Open a loan |
| Accepted collateral, prices and USDC available | Buy back in full or in part |
| Exact loan quotes, including short-funding warnings | Deposit USDC with per-token limits |
| Token risk reports with plain-language flags | Change lending limits |
| Borrower loans, lender positions, single loans | Withdraw |
| Token listing requests (returns a link) | Claim repaid USDC or defaulted collateral |
| Transaction simulation | Turn automatic re-lending on or off |

## Discoverable, installable, available

To be clear about what installing this does:

- **Discoverable:** listed in directories, so people and agents can find it.
- **Installable:** anyone can add it to their agent with one of the commands above.
- **Available:** only once installed. Publishing a skill does not add it to every AI agent automatically.

## Related

- Website: https://based.loans
- Docs for AI agents: https://docs.based.loans/ai-agents
- Developer toolkit (TypeScript SDK, MCP server, Coinbase AgentKit provider): https://github.com/0xVarius/basedloans-agents
- npm: [`@basedloans/sdk`](https://www.npmjs.com/package/@basedloans/sdk), [`@basedloans/mcp`](https://www.npmjs.com/package/@basedloans/mcp), [`@basedloans/agentkit`](https://www.npmjs.com/package/@basedloans/agentkit)
- MCP Registry: `io.github.0xVarius/basedloans`

Risk reports describe data and are not financial advice. Loans and lending carry risk: borrowers lose their collateral if they don't buy back in time, and lenders may receive collateral worth less than they lent.

## License

MIT
