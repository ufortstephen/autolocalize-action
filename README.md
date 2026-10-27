# Localize.dev GitHub Action

Finds hardcoded UI strings in your React code, gives them semantic keys, translates them, and
opens a pull request that swaps the strings for `t()` calls and adds the locale files.

Built from [`localize-dev`](https://www.npmjs.com/package/localize-dev) v0.5.0.

## Setup

1. Create an API key in the [Localize.dev dashboard](https://localize-dev.vercel.app/dashboard/keys).
2. Add it to your repository as the secret `LOCALIZEDEV_API_KEY`
   (Settings → Secrets and variables → Actions).
3. Allow Actions to open pull requests: Settings → Actions → General →
   "Allow GitHub Actions to create and approve pull requests".
4. Save [`example-workflow.yml`](example-workflow.yml) as `.github/workflows/localize.yml`
   and set your default branch and languages.

```yaml
- uses: actions/checkout@v5
- uses: ufortstephen/autolocalize-action@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    api_key: ${{ secrets.LOCALIZEDEV_API_KEY }}
    target_locales: 'es,fr,de'
```

## What the pull request contains

- Components rewritten to `t()` calls, plus nested locale files (`locales/{locale}.json`).
- If the app doesn't use i18next yet: `i18next` and `react-i18next` in `package.json` and
  the lockfile, a `src/i18n.ts` that loads the locale files, and an import of it in your entry
  file (`src/main.tsx` etc.), plus a `<LanguageSwitcher />` component to place in your UI.
  Existing i18next setups are left as they are.

## Inputs

| Input | Default | |
| --- | --- | --- |
| `github_token` | required | Token that pushes the branch and opens the PR (`contents` and `pull-requests: write`). |
| `api_key` | | Localize.dev API key. Required unless `offline` is `true`. |
| `target_locales` | `es,fr` | Comma-separated languages to translate into. |
| `src_dir` | `src` | Directory to scan. |
| `locales_path` | `locales/{locale}.json` | Where locale files go; `{locale}` is replaced. |
| `base_branch` | the triggering branch | Branch the PR targets. |
| `api_url` | `https://localize-dev.vercel.app` | API base URL, for staging deployments. |
| `offline` | `false` | Generate keys without translating; no API key needed. |

## Outputs

- `pr_url`: the opened or updated pull request.
- `strings_count`: how many strings were replaced.
