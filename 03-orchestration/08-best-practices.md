---
prev_url: 07-multi-agent.md
next_url: 09-next-steps.md
---
# Best Practices

## When to Use What

| Scenario | Use This | Why |
|----------|----------|-----|
| Creating/editing flows | AI Copilot | Fastest way to generate YAML flow code |
| Answering questions about your data | RAG | Grounds responses in real data |
| Fixed, repeatable ETL pipelines | Traditional workflows | Deterministic, predictable, compliant |
| Research and analysis tasks | AI Agents | Can adapt to findings and make decisions |
| Complex, multi-step objectives | Multi-agent systems | Specialized agents working together |

## Cost Considerations

AI features use LLM APIs, which have costs based on token usage.

Pricing per 1M tokens ([full pricing page](https://ai.google.dev/gemini-api/docs/pricing)):

| Model | Tier | Input | Output |
|-------|------|-------|--------|
| Gemini 3.8 Flash | Free tier | $0.00 | $0.00 |
| Gemini 3.8 Flash | Standard (through Dec 31, 2026) | $0.75 | $3.75 |
| Gemini 3.8 Flash | Standard (from Jan 1, 2027) | $1.50 | $7.50 |
| Gemini 3.5 Flash-Lite | Standard | $0.30 | $2.50 |

Use Gemini 3.8 Flash for most agentic workflows. Google currently limits access to Gemini 2.5 models to users who have actively used them; for new projects, prefer Gemini 3.8 Flash or a lower-cost current model such as Gemini 3.5 Flash-Lite. Check the pricing page before running cost-sensitive workflows.

Cost-saving tips:

1. Start with the free tier for learning and development
2. Use smaller/cheaper models for simple tasks — check the [pricing page](https://ai.google.dev/gemini-api/docs/pricing)
3. Set `maxToken` to limit response size
4. Monitor token usage in execution outputs
5. Use traditional workflows when determinism is needed

## Security

Never commit API keys to Git. Always use secrets:

```yaml
# ❌ Wrong
apiKey: "sk-abc123def456"

# ✅ Correct
apiKey: "{{ secret('GEMINI_API_KEY') }}"
```

Export base64-encoded keys as `SECRET_`-prefixed environment variables before starting Kestra. Rotate keys regularly (e.g., every 90 days) and monitor usage. Read more about secrets in the [Kestra documentation](https://kestra.io/docs/concepts/secret).

## Observability and Debugging

Enable detailed logging when troubleshooting:

```yaml
- id: my_agent_task
  type: io.kestra.plugin.ai.agent.AIAgent
  provider:
    # ...
    # provider settings
    # ...
  configuration:
    logRequests: true
    logResponses: true
```

Monitor token usage per execution, agent tool calls and decisions, execution time and costs, and output quality.

Debugging tips:

1. Start with simple prompts and iterate
2. Check logs for LLM reasoning
3. Verify tool execution outputs

## Production Readiness

Before deploying AI workflows to production:

1. Test thoroughly — run multiple times with different inputs, verify outputs are consistent and accurate
2. Add fallbacks — handle API failures with retries and configure alerts on failure
3. Set limits — cap `maxToken` to control costs
4. Document behavior — explain what the agent does in your flow and task descriptions
