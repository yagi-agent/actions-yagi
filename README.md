# actions-yagi

GitHub Action for AI-powered pull request review using [yagi](https://github.com/yagi-agent/yagi).

## Usage

```yaml
name: yagi-review

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
      - uses: yagi-agent/actions-yagi@v1
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
```

## Inputs

| Name | Description | Default |
|------|-------------|---------|
| `model` | Model to use | `openai/gpt-4o` |
| `prompt` | Custom review prompt | Built-in Japanese review prompt |
| `yagi_version` | Yagi version to install | `latest` |
| `github_token` | GitHub token for posting comments | `${{ github.token }}` |

## Example with custom model

```yaml
- uses: yagi-agent/actions-yagi@v1
  env:
    ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
  with:
    model: anthropic/claude-sonnet-4-20250514
    prompt: |
      Review this pull request and provide feedback in English.
      Focus on security issues and performance.
```

## Supported Providers

API key is passed via environment variables:

| Provider | Environment Variable |
|----------|---------------------|
| OpenAI | `OPENAI_API_KEY` |
| Anthropic | `ANTHROPIC_API_KEY` |
| Google | `GEMINI_API_KEY` |
| OpenRouter | `OPENROUTER_API_KEY` |

## Note

The yagi-profiles repository includes an English version of the identity file (`IDENTITY_EN.md`). You can switch to it by setting the `YAGI_IDENTITY_FILE` environment variable according to your preference.

You can also override the model by setting the `YAGI_MODEL` environment variable:

```yaml
- uses: yagi-agent/actions-yagi@v1
  env:
    OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
    YAGI_IDENTITY_FILE: IDENTITY_EN.md
    YAGI_MODEL: openai/gpt-4o-mini
```
