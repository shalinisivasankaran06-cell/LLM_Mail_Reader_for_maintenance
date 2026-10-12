# LLM Mail Reader for Maintenance

When a data feed goes missing, this script checks the mailbox for planned maintenance or upgrade emails. Gemini AI reads each email and decides whether a maintenance window covers the current time. The result (no ticket needed, or create a ticket) is posted to Slack.

## Full flow

![Full flow diagram](docs/flow.svg)

## How it works

1. **Trigger.** A missing data feed is detected and `ai_mail.py` is run with the feed name.
2. **Search Gmail.** The Gmail API fetches emails matching `subject:upgrade newer_than:7d`.
3. **Read each email.** The body is taken from plain text, or from HTML converted to text, and trimmed to 8000 characters.
4. **Gemini decides.** The model returns JSON: `{"is_maintenance": true/false, "start": "ISO or null", "end": "ISO or null"}`.
   - `gemini-flash-latest` is tried first, then `gemini-flash-lite-latest`.
   - Errors 503 and 429 are retried up to 3 times with a growing delay.
   - Email content is treated as untrusted data, so instructions inside an email are ignored.
5. **Check the window.** For maintenance emails, start and end are converted to IST (Asia/Kolkata). If the current time is inside the window, the email is added to the active list.
6. **Verdict.**
   - At least one active window: the feed is missing because of planned maintenance, **no ticket needed**.
   - No active window: **create a ticket**.
7. **Notify.** The verdict is posted to Slack through an incoming webhook.

## Decision table

| Situation | Result |
|---|---|
| Email is not about maintenance | Listed as context, no effect on the window check |
| Maintenance email, start or end unknown | Skipped |
| Maintenance window does not include now | Ignored |
| Maintenance window includes now | Counted as active, no ticket needed |
| No active windows after all emails are checked | Create a ticket |
| Some emails failed to be read | Slack message includes a warning that the result may be incomplete |

## Requirements

- Python 3.9 or newer
- A Google Cloud project with the Gmail API enabled and an OAuth client (`client_secret.json`)
- A Gemini API key
- A Slack incoming webhook URL

```bash
pip install google-genai simplegmail requests
```

## Setup

1. **Gmail access.** Download the OAuth client file from Google Cloud Console (APIs & Services, Credentials) and save it as `client_secret.json` next to `ai_mail.py`. The first run opens a login flow and creates `gmail_token.json`.
2. **Environment variables.**

   ```bash
   export GEMINI_API_KEY="your-gemini-key"
   export SLACK_WEBHOOK_URL="https://hooks.slack.com/services/XXX/YYY/ZZZ"
   ```

3. **Run.**

   ```bash
   python ai_mail.py "Sales data feed"
   ```

## Configuration

These values are at the top of `ai_mail.py`.

| Setting | Default | Purpose |
|---|---|---|
| `MODELS` | `gemini-flash-latest`, `gemini-flash-lite-latest` | Models tried in order |
| `LOCAL_TZ` | `Asia/Kolkata` | Timezone used when an email gives none, and for display |
| `SEARCH` | `subject:upgrade newer_than:7d` | Gmail search query |
| `DEBUG` | `False` | Print what the model returned for each email |

## Example Slack messages

Maintenance in progress:

```
🟡 Sales data feed is missing, but planned maintenance is in progress. No ticket needed.
• Server upgrade notice (12 Oct 22:00 to 13 Oct 02:00 IST)
```

No maintenance found:

```
🔴 Sales data feed is missing and no active maintenance window was found. Create a ticket.
```

## Running on a schedule

Cron does not load your shell variables, so set them in the crontab line or a wrapper script.

```cron
*/30 * * * * GEMINI_API_KEY=xxx SLACK_WEBHOOK_URL=xxx /home/ubuntu/app/venv/bin/python /home/ubuntu/app/ai_mail.py "Sales data feed"
```

## Security

Never commit these files. They are listed in `.gitignore`.

- `client_secret.json`
- `gmail_token.json`
- `.env`

If a credential is ever pushed by mistake, revoke it in Google Cloud Console (or regenerate the Slack webhook and Gemini key) and create a new one.

## Project structure

```
.
├── ai_mail.py          # main script
├── docs/
│   └── flow.svg        # flow diagram used in this README
├── client_secret.json  # Google OAuth client (secret, NOT committed)
├── gmail_token.json    # Gmail access/refresh token, created on first run (secret, NOT committed)
├── .env                # optional: GEMINI_API_KEY, SLACK_WEBHOOK_URL (secret, NOT committed)
├── .gitignore          # lists the secret files above
└── README.md
```

The three secret files exist only on your server or machine. A fresh clone will not contain them, so recreate them using the Setup steps above.
