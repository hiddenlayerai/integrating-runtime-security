# Integrating Runtime Security

Runnable notebooks showing how to integrate HiddenLayer AI Runtime Security into
an agentic system, one per supported payload format, using the
[HiddenLayer Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python).

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

Get credentials from the HiddenLayer console (Settings, API Keys). Each notebook
runs top to bottom with no LLM provider configured.

> Beta endpoint; the SDK emits a `BetaWarning`.

## How it works

Call `client.runtime.evaluate_interaction()` at each boundary where content
enters the model's context window (user prompt, tool call, tool result, final
answer). The arguments, all recommended for proper functionality:

- `interaction`: the native provider payload you send to or receive from the model
- `metadata`: `model`, `provider`, `requester_id`, `external_session_id`
- `hl_project_id`: the project whose policy evaluates the interaction
- `HL-Runtime-Session-Id` header: the same value across the run, so HiddenLayer
  strings the turns into one session

Each returned message carries **`analysis.signals`** (`prompt_injection`,
`personally_identifiable_information`, `code`, `denial_of_service`, `guardrails`,
`url`, `language`). Use the signals to decide what your agent does.

## Integrating into your agent

The notebook payloads are hardcoded so they run without an LLM provider. In your
agent you already build these payloads to call the model; to integrate, pass
those same payloads to `evaluate_interaction` at the matching boundaries. The SDK
call does not change, and the agent framework is your choice.

The notebooks show two ways to act on a detection:

- **Self-correction**: when a signal fires on untrusted input, withhold the
  flagged content and forward a short note built from the signals, so the model
  self-corrects without ever seeing it and the agent keeps running.
- **Policy enforcement**: craft HiddenLayer policy rules against these signals;
  when a rule matches, the decision comes back on `outcome`, so enforcement
  happens in the platform rather than your agent code.

## Resources

- [HiddenLayer Documentation](https://docs.hiddenlayer.ai/)
- [Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python)
- [SDK API Reference](https://github.com/hiddenlayerai/hiddenlayer-sdk-python/blob/main/api.md#runtime)
