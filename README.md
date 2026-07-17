# Integrating Runtime Security

Notebooks for **integrating HiddenLayer runtime security into agentic systems**,
using `client.runtime.evaluate_interaction()` from the
[HiddenLayer Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python).

Integration is one SDK method, `client.runtime.evaluate_interaction()`, called at
each boundary where content enters the model's context window (user prompt, tool
call, tool result, final answer). The arguments are recommended for proper
functionality:

- `interaction`: the native provider payload you send to or receive from the model
- `metadata`: `model`, `provider`, `requester_id`, `external_session_id`
- `hl_project_id`: the project whose policy evaluates the interaction
- `HL-Runtime-Session-Id` header: the same value across the run, so HiddenLayer
  strings the turns into one session

Every returned message carries **`analysis.signals`**: what the analyzers
detected (`prompt_injection`, `personally_identifiable_information`, `code`,
`denial_of_service`, `guardrails`, `url`, `language`). Use the signals to decide
what your agent does. The notebooks show the raw SDK call first, then wrap it in
an optional `scan()` helper; wrapping it is your choice, the SDK call is the same
either way.

The agent framework is your choice. HiddenLayer works at the payload level, so
integrate at whichever boundaries your loop exposes.

Each notebook ends with a self-correction pattern: when a signal fires on
untrusted input (a poisoned tool result), the agent withholds the flagged
content and forwards a short note built from the signals instead, so the model
knows something was detected and can self-correct without ever seeing the
malicious content. The agent keeps running.

Beyond using the signals directly, you can craft HiddenLayer policy rules
against these same signals; when a rule matches, the decision comes back on
`outcome` so enforcement happens in the platform rather than your agent code.

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

> Beta endpoint; the SDK emits a `BetaWarning`.

## Resources

- [HiddenLayer Documentation](https://docs.hiddenlayer.ai/)
- [Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python)
- [SDK API Reference](https://github.com/hiddenlayerai/hiddenlayer-sdk-python/blob/main/api.md#runtime)
