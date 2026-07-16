# Integrating Runtime Security

Notebooks for **integrating HiddenLayer runtime security into agentic systems**,
using `client.runtime.evaluate_interaction()` from the
[HiddenLayer Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python).

Wire the endpoint into your agent at each boundary where content enters the
model's context window (user prompt, tool call, tool result, final answer). Each
call re-evaluates the whole interaction and returns:

- **`evaluated_interaction[].analysis.signals`**: what the analyzers detected on
  each message. Always populated, independent of policy. The notebooks print
  these for every message, so you can watch the interaction build up boundary by
  boundary.
- **`outcome.action` / `outcome.detections`**: policy enforcement, populated
  once enterprise Runtime v2 policies are configured on the tenant.

The agent framework is your choice. HiddenLayer works at the payload level, so
integrate at whichever boundaries your loop exposes.

One notebook per provider payload format:

| Notebook | Payload format |
|----------|----------------|
| [`evaluate_interaction_openai_chat_completions.ipynb`](./evaluate_interaction_openai_chat_completions.ipynb) | [OpenAI Chat Completions](https://platform.openai.com/docs/api-reference/chat) |
| [`evaluate_interaction_openai_responses.ipynb`](./evaluate_interaction_openai_responses.ipynb) | [OpenAI Responses](https://platform.openai.com/docs/api-reference/responses) |
| [`evaluate_interaction_anthropic_messages.ipynb`](./evaluate_interaction_anthropic_messages.ipynb) | [Anthropic Messages](https://docs.anthropic.com/en/api/messages) |

## Quick start

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install hiddenlayer-sdk python-dotenv jupyter

cat > .env <<'ENV'
HIDDENLAYER_CLIENT_ID=your-client-id
HIDDENLAYER_CLIENT_SECRET=your-client-secret
ENV

jupyter lab
```

> Beta endpoint; the SDK emits a `BetaWarning`. `outcome` stays empty until
> Runtime v2 policies are configured on the tenant; `signals` do not.

## Resources

- [HiddenLayer Documentation](https://docs.hiddenlayer.ai/)
- [Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python)
- [SDK API Reference](https://github.com/hiddenlayerai/hiddenlayer-sdk-python/blob/main/api.md#runtime)
