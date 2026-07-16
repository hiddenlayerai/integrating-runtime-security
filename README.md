# Integrating Runtime Security

Notebooks for integrating **HiddenLayer AI Runtime Security** into an agentic
system with a single endpoint, `client.runtime.evaluate_interaction()`
(`POST /detection/v2/interaction-evaluations`), using the
[HiddenLayer Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python).

`evaluate_interaction` takes the **whole interaction so far** and returns the
full evaluation: the canonicalized messages with per-message signals, plus a
policy `outcome` (action, threat level, detections, and the
`effective_interaction` to forward). You call it every time the model's context
window grows, and act on `outcome.action` however your system decides.

Each notebook builds one agent turn up **boundary by boundary** (every point
where new content enters the context window) for one provider payload format:

| Notebook | Payload format |
|----------|----------------|
| [`evaluate_interaction_openai_chat_completions.ipynb`](./evaluate_interaction_openai_chat_completions.ipynb) | [OpenAI Chat Completions](https://platform.openai.com/docs/api-reference/chat) |
| [`evaluate_interaction_openai_responses.ipynb`](./evaluate_interaction_openai_responses.ipynb) | [OpenAI Responses](https://platform.openai.com/docs/api-reference/responses) |
| [`evaluate_interaction_anthropic_messages.ipynb`](./evaluate_interaction_anthropic_messages.ipynb) | [Anthropic Messages](https://docs.anthropic.com/en/api/messages) |

The four boundaries, evaluated in order:

1. **User prompt** enters the context
2. **Assistant tool call** is emitted
3. **Tool result** returns (untrusted third-party input, the indirect
   prompt-injection channel; the notebooks smuggle an injection in here)
4. **Assistant final answer** goes out

The interaction is passed in each provider's **native payload format**;
`evaluate_interaction` canonicalizes it internally so detection rules run
against a uniform structure, and `outcome.effective_interaction` comes back in
the same format you sent.

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

`.env` is read by each notebook via `python-dotenv`. Never commit it.

> **Requires a tenant with Runtime v2 enabled and detection policies
> configured.** Without them the calls succeed and return a well-formed
> response, but `action` is `NONE` and `detections` is empty. The endpoint is
> beta; the SDK emits a `BetaWarning`.

## Resources

- [HiddenLayer Documentation](https://docs.hiddenlayer.ai/)
- [Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python)
- [SDK API Reference](https://github.com/hiddenlayerai/hiddenlayer-sdk-python/blob/main/api.md#runtime)
