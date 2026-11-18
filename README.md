# AlertHub Notify Action

Send instant CI/CD build, test, and deployment alerts to **Mobile Push (Android)**, **Slack**, **Telegram**, **WhatsApp**, **Email**, and **Webhooks** via [AlertHub](https://alerthub.io).

Built as a lightweight composite action with zero external dependencies. Runs in milliseconds on GitHub-hosted runners and automatically maps your workflow status, branch, commit SHA, actor, and run log URL.

## Quickstart

Add this step to the end of any job to automatically alert your team when a workflow fails or finishes:

```yaml
- name: Notify AlertHub
  if: always()
  uses: alerthubio/notify-action@v1
  with:
    user_id: ${{ secrets.ALERTHUB_USER_ID }}
    api_key: ${{ secrets.ALERTHUB_API_KEY }}
```

When `title`, `body`, `status`, and `priority` are omitted, the action automatically:
- Maps `job.status` (`success` → `success`, `failure` → `error`, `cancelled` → `warning`).
- Elevates `priority` to `high` on failures and `normal` otherwise.
- Formats a headline with the workflow name and repository, plus a body containing the branch, short commit SHA, actor, and a direct link to the workflow run logs.

## Setup

1. Create a free account at **[alerthub.io](https://alerthub.io)** and configure your notification channels (Mobile Push, Slack, Telegram, WhatsApp, Email, or Webhooks).
2. Copy your **User ID** and **API Key** from your AlertHub dashboard.
3. In your GitHub repository, go to **Settings → Secrets and variables → Actions** and add two repository secrets:
   - `ALERTHUB_USER_ID`
   - `ALERTHUB_API_KEY`

## Examples

### Alert Only on Failure

```yaml
- name: Alert on Build Failure
  if: failure()
  uses: alerthubio/notify-action@v1
  with:
    user_id: ${{ secrets.ALERTHUB_USER_ID }}
    api_key: ${{ secrets.ALERTHUB_API_KEY }}
```

### Custom Production Deployment Alert

Route deployment notifications to a specific AlertHub notification profile with a custom headline and message:

```yaml
- name: Notify Production Deploy
  if: success()
  uses: alerthubio/notify-action@v1
  with:
    user_id: ${{ secrets.ALERTHUB_USER_ID }}
    api_key: ${{ secrets.ALERTHUB_API_KEY }}
    title: 'Production deploy succeeded'
    body: 'Deployed ${{ github.repository }} (${{ github.sha }}) by ${{ github.actor }}.'
    status: 'success'
    priority: 'normal'
    profile: 'default'
```

### Strict Mode (Fail Workflow If Notification Fails)

By default, a network hiccup reaching AlertHub logs a warning without failing your CI pipeline. Set `fail_on_error: 'true'` if notification delivery is mandatory:

```yaml
- name: Critical Compliance Alert
  if: always()
  uses: alerthubio/notify-action@v1
  with:
    user_id: ${{ secrets.ALERTHUB_USER_ID }}
    api_key: ${{ secrets.ALERTHUB_API_KEY }}
    fail_on_error: 'true'
```

## Inputs

| Input | Required | Default | Description |
| :--- | :--- | :--- | :--- |
| `user_id` | **Yes** | — | Your AlertHub User ID. |
| `api_key` | **Yes** | — | Your AlertHub API Key (`X-API-KEY`). |
| `title` | No | *Auto-generated* | Custom alert headline (max 50 characters; auto-truncated if longer). |
| `body` | No | *Auto-generated* | Custom alert details. Defaults to repository, branch, short SHA, actor, and run URL. |
| `status` | No | `'auto'` | Event status: `auto`, `info`, `success`, `warning`, or `error`. |
| `priority` | No | `'auto'` | Alert priority: `auto` (`high` on `error`, `normal` otherwise), `low`, `normal`, or `high`. |
| `source` | No | `'github-actions'` | Source identifier displayed in your AlertHub live stream. |
| `profile` | No | `''` | Optional AlertHub notification profile name for channel routing. |
| `fail_on_error` | No | `'false'` | Set to `'true'` to fail the workflow step if the AlertHub API returns an error. |

## Outputs

| Output | Description |
| :--- | :--- |
| `http_status` | The HTTP status code returned by the AlertHub ingestion API (`200` / `201` on success). |

## Resources & Community

- **Dashboard & Free Account:** [alerthub.io](https://alerthub.io)
- **API Documentation:** [alerthub.io/docs](https://alerthub.io/docs)
- **Guides & Tutorials:** [alerthub.io/guides](https://alerthub.io/guides)
- **Android App:** [Google Play Store](https://play.google.com/store/apps/details?id=io.alerthub.app)
- **Community:** [r/alerthub](https://www.reddit.com/r/alerthub/) · [X (@AlertHubio)](https://x.com/AlertHubio)

## Security & Version Pinning

For standard setups, pin to the floating major version tag (`@v1`) to receive backwards-compatible bug fixes and updates automatically. For high-security or regulated environments, pin directly to the immutable 40-character commit SHA of a verified release:

```yaml
- name: Notify AlertHub
  if: always()
  uses: alerthubio/notify-action@072223d21e71d541d961b1e9a9b2046b7671a686 # v1.0.1
  with:
    user_id: ${{ secrets.ALERTHUB_USER_ID }}
    api_key: ${{ secrets.ALERTHUB_API_KEY }}
```

All official releases and tags in this repository are cryptographically signed and protected against unauthorized modification or force-pushes.

## License

MIT — see [LICENSE](LICENSE) for details.
