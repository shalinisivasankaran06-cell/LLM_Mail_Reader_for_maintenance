 # LLM Mail Reader for Maintenance

When a data feed goes missing, this script checks the mailbox for planned maintenance or upgrade emails. Gemini AI reads each email and decides whether a maintenance window covers the current time. The result (no ticket needed, or create a ticket) is posted to Slack.

## Full flow
  <title>LLM Mail Reader for Maintenance - full flow</title>
  <defs>
    <marker id="a" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse">
      <path d="M0 0L10 5L0 10z" fill="#4B5563"/>
    </marker>
  </defs>

  <rect width="880" height="1300" fill="#FFFFFF"/>

  <!-- Start -->
  <rect x="290" y="25" width="300" height="50" rx="25" fill="#FFEDD5" stroke="#EA580C" stroke-width="2"/>
  <text x="440" y="56" text-anchor="middle" font-size="15" font-weight="700" fill="#111827">Data feed is missing</text>

  <!-- Run script -->
  <rect x="290" y="105" width="300" height="56" rx="10" fill="#EEF2FF" stroke="#6366F1" stroke-width="2"/>
  <text x="440" y="129" text-anchor="middle" font-size="14" font-weight="700" fill="#111827">Run ai_mail.py</text>
  <text x="440" y="147" text-anchor="middle" font-size="11" fill="#4B5563">python ai_mail.py "Feed name"</text>

  <!-- Gmail -->
  <rect x="290" y="191" width="300" height="56" rx="10" fill="#EEF2FF" stroke="#6366F1" stroke-width="2"/>
  <text x="440" y="215" text-anchor="middle" font-size="14" font-weight="700" fill="#111827">Search Gmail</text>
  <text x="440" y="233" text-anchor="middle" font-size="11" fill="#4B5563">subject:upgrade newer_than:7d</text>

  <!-- Read email -->
  <rect x="290" y="277" width="300" height="56" rx="10" fill="#EEF2FF" stroke="#6366F1" stroke-width="2"/>
  <text x="440" y="301" text-anchor="middle" font-size="14" font-weight="700" fill="#111827">Read each email</text>
  <text x="440" y="319" text-anchor="middle" font-size="11" fill="#4B5563">HTML to plain text, first 8000 characters</text>

  <!-- Gemini -->
  <rect x="290" y="363" width="300" height="56" rx="10" fill="#F3E8FF" stroke="#9333EA" stroke-width="2"/>
  <text x="440" y="387" text-anchor="middle" font-size="14" font-weight="700" fill="#111827">Gemini AI decides</text>
  <text x="440" y="405" text-anchor="middle" font-size="11" fill="#4B5563">flash-latest, then flash-lite fallback</text>

  <!-- Decision 1 -->
  <polygon points="440,440 550,485 440,530 330,485" fill="#FEF3C7" stroke="#D97706" stroke-width="2"/>
  <text x="440" y="489" text-anchor="middle" font-size="12" font-weight="700" fill="#111827">Maintenance email?</text>

  <!-- Decision 2 -->
  <polygon points="440,550 550,595 440,640 330,595" fill="#FEF3C7" stroke="#D97706" stroke-width="2"/>
  <text x="440" y="599" text-anchor="middle" font-size="12" font-weight="700" fill="#111827">Start and end found?</text>

  <!-- IST -->
  <rect x="290" y="670" width="300" height="50" rx="10" fill="#EEF2FF" stroke="#6366F1" stroke-width="2"/>
  <text x="440" y="700" text-anchor="middle" font-size="14" font-weight="700" fill="#111827">Convert start and end to IST</text>

  <!-- Decision 3 -->
  <polygon points="440,740 550,785 440,830 330,785" fill="#FEF3C7" stroke="#D97706" stroke-width="2"/>
  <text x="440" y="789" text-anchor="middle" font-size="12" font-weight="700" fill="#111827">Now inside window?</text>

  <!-- Add active -->
  <rect x="290" y="860" width="300" height="50" rx="10" fill="#EEF2FF" stroke="#6366F1" stroke-width="2"/>
  <text x="440" y="890" text-anchor="middle" font-size="14" font-weight="700" fill="#111827">Add to active list</text>

  <!-- Decision 4 -->
  <polygon points="440,950 550,995 440,1040 330,995" fill="#FEF3C7" stroke="#D97706" stroke-width="2"/>
  <text x="440" y="999" text-anchor="middle" font-size="12" font-weight="700" fill="#111827">Any active window?</text>

  <!-- Side boxes -->
  <rect x="620" y="457" width="220" height="56" rx="10" fill="#F3F4F6" stroke="#6B7280" stroke-width="2"/>
  <text x="730" y="481" text-anchor="middle" font-size="13" font-weight="700" fill="#111827">Not maintenance</text>
  <text x="730" y="499" text-anchor="middle" font-size="11" fill="#4B5563">kept as context, next email</text>

  <rect x="620" y="567" width="220" height="56" rx="10" fill="#F3F4F6" stroke="#6B7280" stroke-width="2"/>
  <text x="730" y="591" text-anchor="middle" font-size="13" font-weight="700" fill="#111827">Time unknown</text>
  <text x="730" y="609" text-anchor="middle" font-size="11" fill="#4B5563">skipped, next email</text>

  <rect x="620" y="757" width="220" height="56" rx="10" fill="#F3F4F6" stroke="#6B7280" stroke-width="2"/>
  <text x="730" y="781" text-anchor="middle" font-size="13" font-weight="700" fill="#111827">Window not active</text>
  <text x="730" y="799" text-anchor="middle" font-size="11" fill="#4B5563">ignored, next email</text>

  <!-- Outcomes -->
  <rect x="60" y="1100" width="340" height="70" rx="10" fill="#FEF9C3" stroke="#CA8A04" stroke-width="2"/>
  <text x="230" y="1124" text-anchor="middle" font-size="14" font-weight="700" fill="#111827">Planned maintenance found</text>
  <text x="230" y="1142" text-anchor="middle" font-size="12" fill="#111827">No ticket needed</text>
  <text x="230" y="1158" text-anchor="middle" font-size="11" fill="#4B5563">Slack lists the active windows</text>

  <rect x="480" y="1100" width="340" height="70" rx="10" fill="#FEE2E2" stroke="#DC2626" stroke-width="2"/>
  <text x="650" y="1124" text-anchor="middle" font-size="14" font-weight="700" fill="#111827">No maintenance found</text>
  <text x="650" y="1142" text-anchor="middle" font-size="12" fill="#111827">Create a ticket</text>
  <text x="650" y="1158" text-anchor="middle" font-size="11" fill="#4B5563">Slack lists the unrelated emails</text>

  <!-- Slack -->
  <rect x="280" y="1215" width="320" height="56" rx="10" fill="#DCFCE7" stroke="#16A34A" stroke-width="2"/>
  <text x="440" y="1239" text-anchor="middle" font-size="14" font-weight="700" fill="#111827">Post result to Slack</text>
  <text x="440" y="1257" text-anchor="middle" font-size="11" fill="#4B5563">Incoming webhook (SLACK_WEBHOOK_URL)</text>

  <!-- Main arrows -->
  <g stroke="#4B5563" stroke-width="2" fill="none" marker-end="url(#a)">
    <line x1="440" y1="75" x2="440" y2="105"/>
    <line x1="440" y1="161" x2="440" y2="191"/>
    <line x1="440" y1="247" x2="440" y2="277"/>
    <line x1="440" y1="333" x2="440" y2="363"/>
    <line x1="440" y1="419" x2="440" y2="440"/>
    <line x1="440" y1="530" x2="440" y2="550"/>
    <line x1="440" y1="640" x2="440" y2="670"/>
    <line x1="440" y1="720" x2="440" y2="740"/>
    <line x1="440" y1="830" x2="440" y2="860"/>
    <line x1="440" y1="910" x2="440" y2="950"/>

    <!-- No branches -->
    <line x1="550" y1="485" x2="620" y2="485"/>
    <line x1="550" y1="595" x2="620" y2="595"/>
    <line x1="550" y1="785" x2="620" y2="785"/>

    <!-- Final decision -->
    <path d="M330 995 H230 V1100"/>
    <path d="M550 995 H650 V1100"/>

    <!-- Into Slack -->
    <path d="M230 1170 V1243 H280"/>
    <path d="M650 1170 V1243 H600"/>
  </g>

  <!-- Loop back -->
  <path d="M290 885 H90 V219 H288" stroke="#4B5563" stroke-width="2" fill="none" stroke-dasharray="6 5" marker-end="url(#a)"/>
  <text x="72" y="552" text-anchor="middle" font-size="12" fill="#4B5563" transform="rotate(-90 72 552)">repeat for every email</text>

  <!-- Yes / No labels -->
  <g font-size="12" font-weight="700">
    <text x="448" y="545" fill="#16A34A">Yes</text>
    <text x="448" y="655" fill="#16A34A">Yes</text>
    <text x="448" y="845" fill="#16A34A">Yes</text>
    <text x="565" y="478" fill="#DC2626">No</text>
    <text x="565" y="588" fill="#DC2626">No</text>
    <text x="565" y="778" fill="#DC2626">No</text>
    <text x="270" y="987" fill="#16A34A">Yes</text>
    <text x="598" y="987" fill="#DC2626">No</text>
  </g>
</svg>

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
