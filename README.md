# Integrating Runtime Security

Notebooks for integrating **HiddenLayer AI Runtime Security (v2 pass-through
API)** into an agentic system, using the
[HiddenLayer Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python).
Wrap your model calls: `client.runtime.evaluate_request()` scans the payload
before the LLM, `client.runtime.evaluate_response()` scans the answer after.
The body is the raw provider payload, returned in the same format, and the
enforcement action arrives on the `HL-Runtime-Action` response header.

One notebook per supported provider payload format:

| Notebook | Payload format |
|----------|----------------|
| [`runtime_v2_openai_chat_completions.ipynb`](./runtime_v2_openai_chat_completions.ipynb) | [OpenAI Chat Completions](https://platform.openai.com/docs/api-reference/chat) |
| [`runtime_v2_openai_responses.ipynb`](./runtime_v2_openai_responses.ipynb) | [OpenAI Responses](https://platform.openai.com/docs/api-reference/responses) |
| [`runtime_v2_anthropic_messages.ipynb`](./runtime_v2_anthropic_messages.ipynb) | [Anthropic Messages](https://docs.anthropic.com/en/api/messages) |

Each notebook runs the same scenarios (safe request, prompt injection, PII in
the request, PII in the response, and a linked request/response roundtrip), so
the only difference between them is the payload shape. The provider format is
auto-detected from the body. On **detect** or **redact**, the payload
(potentially modified) comes back in the provider's format; on **block**, a
canned block message comes back in the provider's *response* format, ready to
return to the caller as if the model had said it.

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

> **Requires a tenant with Runtime v2 enabled.** Without the feature flag the
> calls succeed but return the payload unchanged, with no action header and no
> analysis. The endpoint is beta; the SDK emits a `BetaWarning`.

## Resources

- [HiddenLayer Documentation](https://docs.hiddenlayer.ai/)
- [Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python)
- [SDK API Reference](https://github.com/hiddenlayerai/hiddenlayer-sdk-python/blob/main/api.md)
