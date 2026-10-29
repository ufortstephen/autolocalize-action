# localize.dev GitHub Action

Finds the hardcoded UI strings in your app, gives them keys named after what they do,
translates them with the context of your app, and opens one pull request that swaps the strings
for translation calls and adds the locale files. On every push, new copy gets the same.

Works with React, Next.js, React Native, Vue, Nuxt, Svelte, Angular, Astro, Solid, Qwik, Lit,
Ember, Flutter, Android, iOS, Rails, Django, Laravel and static HTML; each gets the i18n library
its framework expects. Built from [`localize-dev`](https://www.npmjs.com/package/localize-dev) v0.18.0.
Docs: [https://uselocalize.dev/docs](https://uselocalize.dev/docs).

## Setup

1. Create an API key in the [localize.dev dashboard](https://uselocalize.dev/dashboard/keys).
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

## Modes

- `rewrite` (default): extract hardcoded strings, rewrite the code to translation calls, write
  the locale files, and set up the i18n library if there isn't one.
- `translate`: only fill in missing (or out-of-date) translations in the locale files you
  already have: JSON, YAML, .po, .arb, strings.xml, .xcstrings, .strings, messages.json. Code isn't touched.
- `check`: fail the job when copy is hardcoded or translations are missing or out of date, with
  each problem annotated on the pull request. No API key needed.

## Inputs

| Input | Default | |
| --- | --- | --- |
| `github_token` | required | Token that pushes the branch and opens the PR (`contents` and `pull-requests: write`). |
| `api_key` | | localize.dev API key. Not needed for `mode: check` or `offline`. |
| `mode` | `rewrite` | `rewrite`, `translate` or `check`. |
| `target_locales` | localize.config.json, else `es,fr` | Comma-separated languages to translate into. |
| `src_dir` | the framework's own | Directory to scan. |
| `locales_path` | the framework's own | Where locale files go; `{locale}` is replaced. |
| `base_branch` | the triggering branch | Branch the PR targets. |
| `workspaces` | `false` | Monorepo: localize (or check) every app, each with its own locale files. |
| `tms_pull` / `tms_push` | `false` | Pull translations from, or push sources to, Crowdin, Phrase, Lokalise or Transifex. |
| `tms_token` | | The translation platform's API token. |
| `api_url` | `https://uselocalize.dev` | API base URL, for staging deployments. |
| `offline` | `false` | Generate keys without translating; no API key needed. |

## Outputs

- `pr_url`: the opened or updated pull request.
- `strings_count`: how many strings were replaced.
