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
| `app_id` | GitHub App ID for posting as yagi-chan | (optional) |
| `app_private_key` | GitHub App private key | (optional) |
| `profiles_repo` | Yagi profiles repository | `yagi-agent/yagi-profiles` |
| `profiles_ref` | Git ref for profiles repository | `main` |

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

## Using yagi-chan Avatar (GitHub App Setup)

By default, comments are posted by `github-actions[bot]`. To use a custom avatar (yagi-chan), you need to create a GitHub App.

### Step 1: Create GitHub App

1. Go to **Settings** → **Developer settings** → **GitHub Apps** → **New GitHub App**
2. Fill in the basic information:
   - **GitHub App name**: `yagi-chan-reviewer` (or any name you prefer)
   - **Homepage URL**: `https://github.com/yagi-agent/yagi`
   - **Webhook**: Uncheck "Active"
3. Set **Repository permissions**:
   - **Pull requests**: Read and write
   - **Contents**: Read-only
4. Click **Create GitHub App**

### Step 2: Upload yagi-chan Avatar

1. On the app settings page, scroll to **Display information**
2. Upload the yagi-chan icon image (prepare an image file in advance)
3. Save changes

### Step 3: Generate Private Key

1. Scroll to **Private keys** section
2. Click **Generate a private key**
3. Download the `.pem` file (keep it secure!)

### Step 4: Install the App

1. Go to **Install App** tab
2. Install to your organization or personal repositories
3. Select repositories where you want to use the app

### Step 5: Set Repository Secrets

1. Go to repository **Settings** → **Secrets and variables** → **Actions**
2. Add two secrets:
   - `YAGI_APP_ID`: Your app ID (found on the app settings page)
   - `YAGI_APP_PRIVATE_KEY`: Contents of the `.pem` file

**For organization-wide use**: Set these as organization secrets to share across all repositories.

### Step 6: Update Workflow

```yaml
- uses: yagi-agent/actions-yagi@v1
  env:
    OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
  with:
    app_id: ${{ secrets.YAGI_APP_ID }}
    app_private_key: ${{ secrets.YAGI_APP_PRIVATE_KEY }}
```

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
