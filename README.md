# github-stats

Self-hosted, automatically-updating GitHub stats cards for [monam.tech](https://monam.tech) — no third-party rate limits, no downtime, no stale data.

### Why this exists

Public stats card services like `github-readme-stats.vercel.app` and `streak-stats.demolab.com` are shared by thousands of users and frequently hit rate limits or go down. This repo generates the same cards using a scheduled GitHub Action commits them as static files, and serves them via `raw.githubusercontent.com` — which is backed by GitHub's CDN and effectively never fails.

## What's inside

| File | Description |
|---|---|
| `profile/stats.svg` | Overall GitHub stats card (stars, commits, PRs, issues, contributed-to repos) |
| `profile/streak.svg` | Contribution streak card (total contributions, current streak, longest streak) |
| `profile/stats.json` | Raw counts (`public_repos`, `followers`, `total_stars`) consumed by the live counters on the portfolio site |

## How it works

A GitHub Actions workflow (`.github/workflows/update-stats.yml`) runs:

- **Daily** at midnight UTC (`cron: "0 0 * * *"`)
- **On demand** via the **Run workflow** button in the Actions tab

Each run:
1. Generates `stats.svg` via [`stats-organization/github-readme-stats-action`](https://github.com/stats-organization/github-readme-stats-action)
2. Generates `streak.svg` via [`DenverCoder1/github-readme-streak-stats`](https://github.com/DenverCoder1/github-readme-streak-stats)
3. Pulls the latest GitHub API data and writes `stats.json`
4. Commits and pushes any changed files back to this repo

## Usage on the site

The portfolio embeds the generated files directly:

```html
<img src="https://raw.githubusercontent.com/Monam1083/github-stats/main/profile/stats.svg" alt="GitHub Stats">
<img src="https://raw.githubusercontent.com/Monam1083/github-stats/main/profile/streak.svg" alt="GitHub Streak">
```

```js
const res = await fetch('https://raw.githubusercontent.com/Monam1083/github-stats/main/profile/stats.json');
const { public_repos, followers, total_stars } = await res.json();
```

## Manually triggering an update

1. Go to the **Actions** tab
2. Select **Update README cards**
3. Click **Run workflow**

Changes typically appear on the live site within a few minutes (subject to `raw.githubusercontent.com`'s short cache window).

---

Built and maintained by [Muhammad Monam Majeed](https://monam.tech) · [@Monam1083](https://github.com/Monam1083)
