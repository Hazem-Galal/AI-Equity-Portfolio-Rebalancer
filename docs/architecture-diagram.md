# n8n Architecture Diagram - AI Equity Portfolio Rebalancer

This diagram is generated from [AI Equity Portfolio Rebalancer.json](../AI%20Equity%20Portfolio%20Rebalancer.json).

```mermaid
flowchart TD
    subgraph Trigger
        A[On form submission\nForm Trigger]
    end

    subgraph Agent_Orchestration
        B[AI Agent\nLangChain Agent]
        M[0.OpenAI gpt4.1\nLanguage Model]
        M -.ai_languageModel.-> B
    end

    subgraph Tooling
        T1[1.Get many EoD data in Marketstack]
        T2[2.Get row(s) in Google Sheets]
        T3[3.Update Price\nGoogle Sheets Update]
        T4[4.Update New Quantity After Rebalancing\nGoogle Sheets Update]
        T5[5.Push a message in Pushover\nTool-based notification]
        T6[6.Send a message in Gmail\nDetailed report]
    end

    subgraph Post_Agent_Control
        C{If $json.output == OK}
        D[Push a message\n"Rebalancing successful"]
        E[Push a message1\n"Rebalancer Failed"]
    end

    A --> B

    B -.ai_tool.-> T1
    B -.ai_tool.-> T2
    B -.ai_tool.-> T3
    B -.ai_tool.-> T4
    B -.ai_tool.-> T5
    B -.ai_tool.-> T6

    B --> C
    C -->|True| D
    C -->|False| E
```

## Functional Stages

1. Trigger: user submits rebalance instruction via form.
2. Agent stage: AI Agent executes tools in strict left-to-right order.
3. Data + action stage: prices fetched, sheet read/updated, notifications sent.
4. Control stage: If node checks result and emits final success/failure push message.

## Execution Notes

- The tool order is explicitly constrained in the AI Agent system prompt.
- Gmail is required in the AI Agent prompt as part of final reporting.
- The If node provides operational status signaling after agent execution.
