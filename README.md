# AI Equity Portfolio Rebalancer

![n8n](https://img.shields.io/badge/Built%20With-n8n-ef6d5a?style=for-the-badge&logo=n8n&logoColor=white)
![OpenAI](https://img.shields.io/badge/LLM-OpenAI-10a37f?style=for-the-badge&logo=openai&logoColor=white)
![Marketstack](https://img.shields.io/badge/Market%20Data-Marketstack-1e88e5?style=for-the-badge)
![Google Sheets](https://img.shields.io/badge/Data%20Store-Google%20Sheets-34a853?style=for-the-badge&logo=googlesheets&logoColor=white)
![Gmail](https://img.shields.io/badge/Reporting-Gmail-ea4335?style=for-the-badge&logo=gmail&logoColor=white)
![Pushover](https://img.shields.io/badge/Notifications-Pushover-1b75bc?style=for-the-badge)

Production-ready n8n agentic workflow that rebalances an equity portfolio to a 60/40 target, updates portfolio records, and sends reporting notifications.

- Main workflow: [AI Equity Portfolio Rebalancer.json](AI%20Equity%20Portfolio%20Rebalancer.json)
- Architecture: [docs/architecture-diagram.md](docs/architecture-diagram.md)
- Repository: [Hazem-Galal/AI-Equity-Portfolio-Rebalancer](https://github.com/Hazem-Galal/AI-Equity-Portfolio-Rebalancer)

The workflow is defined in [AI Equity Portfolio Rebalancer.json](AI%20Equity%20Portfolio%20Rebalancer.json).

## What This n8n Application Does

1. Starts from a form submission.
2. Runs an AI Agent with strict tool-order instructions.
3. Retrieves latest EOD prices from Marketstack.
4. Reads positions from Google Sheets.
5. Updates price values in Google Sheets.
6. Calculates and updates new quantities for rebalancing.
7. Sends push and email notifications.
8. Uses an If branch to send final success or failure push message.

## Workflow Architecture Diagram

See [docs/architecture-diagram.md](docs/architecture-diagram.md).

## n8n Canvas Screenshot

![n8n workflow canvas](docs/assets/screenshots/n8n-workflow-canvas.png)

If the image is not visible on GitHub yet, add your uploaded screenshot to `docs/assets/screenshots/n8n-workflow-canvas.png`.

Quick view:

```mermaid
flowchart LR
    A[On form submission] --> B[AI Agent]
    M[OpenAI gpt-4.1] -.ai_languageModel.-> B

    B -.ai_tool.-> T1[1.Get many EoD data in Marketstack]
    B -.ai_tool.-> T2[2.Get row(s) in Google Sheets]
    B -.ai_tool.-> T3[3.Update Price]
    B -.ai_tool.-> T4[4.Update New Quantity After Rebalancing]
    B -.ai_tool.-> T5[5.Push a message in Pushover]
    B -.ai_tool.-> T6[6.Send a message in Gmail]

    B --> C{If output == OK}
    C -->|True| D[Push a message\nRebalancing successful]
    C -->|False| E[Push a message1\nRebalancer Failed]
```

## Enforced Tool Order in AI Agent

The agent prompt enforces this execution order:

1. Get many EoD data in Marketstack
2. Get row(s) in Google Sheets
3. Update Price
4. Update New Quantity After Rebalancing
5. Push a message in Pushover
6. Send a message in Gmail

## Allocation Logic Configured in Prompt

- Target allocation: 60% Equity, 40% Fixed Income
- Acceptable band before rebalance:
  - Equity: 59% to 61%
  - Fixed Income: 39% to 41%

Key formulas used by the agent:

- Position Value = Quantity x Current Price
- Equity Exposure = Position Value x Equity Ratio
- Fixed Income Exposure = Position Value x Fixed Income Ratio
- Total Portfolio Value = sum of all Position Values

## Required Services/Credentials in n8n

- OpenAI API
- Marketstack API
- Google Sheets OAuth2 API
- Gmail OAuth2 API
- Pushover API

## Google Sheet Columns Expected

- Ticker
- Quantity
- Equity ratio
- Fixed income ratio
- Price
- Total Value
- New Quantity After Rebalancing
- New Total Value

## How to Use in n8n

1. Import [AI Equity Portfolio Rebalancer.json](AI%20Equity%20Portfolio%20Rebalancer.json).
2. Map all credentials in each node.
3. Open and submit the Form Trigger.
4. Review updates in Google Sheets.
5. Verify push notifications and Gmail report.

## Security Notes

This JSON currently contains real-looking IDs, webhook values, and email/user identifiers.

Before publishing publicly:

1. Replace sensitive values with placeholders.
2. Rotate exposed credentials/keys.
3. Use n8n credential vault for secrets instead of hardcoded values.
