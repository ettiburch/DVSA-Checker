# DVSA Practical Test Slot Checker 🚗

Monitors the DVSA booking system for available practical test slots at a
specified test centre and date range, and sends an alert the moment one
appears — via email and phone/desktop push notification.

Runs every 15 minutes on GitHub Actions (free).

-----

## How it works

1. Logs into the DVSA booking site using an existing booking reference and licence number
1. Searches for your configured test centre
1. Checks all available dates within your target range
1. If slots are found: sends a Gmail email **and** a push notification via ntfy.sh
1. Repeats every 15 minutes, 24/7

-----

## Setup (one-time, ~10 minutes)

### Step 1 — Create a GitHub repository

1. Go to [github.com](https://github.com) and sign in (or create a free account)
1. Click **New repository**
1. Name it anything, e.g. `dvsa-checker`
1. Set it to **Public** (gives unlimited free Actions minutes)

> Your credentials are stored as encrypted Secrets — they are **never** visible
> to anyone, even on a public repo.
1. Upload all the files from this folder to the repo (drag and drop in the GitHub UI)

-----

### Step 2 — Add GitHub Secrets

These store your credentials securely. Go to your repo →
**Settings → Secrets and variables → Actions → New repository secret**.

Add each of the following:

|Secret name             |Value                                                        |
|------------------------|-------------------------------------------------------------|
|`DVSA_LICENCE_NUMBER`   |Your full UK driving licence number (e.g. `SMITH123456AB1CD`)|
|`DVSA_BOOKING_REFERENCE`|Your existing test booking reference number                  |
|`EMAIL_SENDER`          |Your Gmail address (e.g. `you@gmail.com`)                    |
|`EMAIL_APP_PASSWORD`    |A Gmail **App Password** (see Step 3 below)                  |
|`EMAIL_RECIPIENT`       |Where to send alerts (can be the same Gmail address)         |
|`NTFY_TOPIC`            |A unique topic name you choose (see Step 4 below)            |

-----

### Step 3 — Create a Gmail App Password

Google won’t let scripts log in with your normal password.
You need to create a special “App Password”:

1. Go to [myaccount.google.com/security](https://myaccount.google.com/security)
1. Enable **2-Step Verification** if not already on
1. Search for **App passwords** in the search bar
1. Create a new app password → name it “DVSA Checker”
1. Copy the 16-character code → paste it as the `EMAIL_APP_PASSWORD` secret

-----

### Step 4 — Set up ntfy.sh push notifications

ntfy.sh is a free push notification service — no account needed.

1. Choose a **unique topic name**, e.g. `dvsa-checker-a7x3q` (make it hard to guess)
1. Add it as the `NTFY_TOPIC` secret in GitHub

**To receive notifications on your phone:**

- Download the **ntfy** app ([Android](https://play.google.com/store/apps/details?id=io.heckel.ntfy) | [iPhone](https://apps.apple.com/gb/app/ntfy/id1625396347))
- Tap **+** and subscribe to your chosen topic name

**To receive notifications in your browser (desktop):**

- Visit `https://ntfy.sh/your-topic-name`
- Click **Subscribe** — you’ll get browser push notifications

-----

### Step 5 — Enable GitHub Actions

1. Go to your repo → **Actions** tab
1. If prompted, click **“I understand my workflows, go ahead and enable them”**
1. The checker will now run automatically every 15 minutes

**To test it immediately:** go to **Actions → DVSA Slot Checker → Run workflow**

-----

## What the notifications look like

**Email** — a formatted HTML email with:

- Number of available slots
- List of available dates
- A “Book now →” button linking directly to the DVSA login

**Push notification** — a high-priority phone/desktop alert with:

- Slot count and preview dates
- A “Book now” action button

-----

## Troubleshooting

|Problem             |Fix                                                                           |
|--------------------|------------------------------------------------------------------------------|
|Login failing       |Double-check `DVSA_LICENCE_NUMBER` and `DVSA_BOOKING_REFERENCE` — no spaces   |
|No emails arriving  |Check spam; verify Gmail App Password is correct                              |
|Workflow not running|Go to Actions tab and check it’s enabled; GitHub cron can lag by up to 15 min |
|Centre not found    |The DVSA site structure may have updated — open an issue or check `checker.py`|

If you need to debug, click a failed run in the **Actions** tab to see the full log output.

-----

## Notes

- This tool only **notifies** you — it never books a test automatically
- Popular test centres fill up fast; once notified, act within minutes
- The DVSA website occasionally changes its HTML structure; if the checker
  stops working, the logs will show where it’s failing so you can update
  the relevant section of `checker.py`
