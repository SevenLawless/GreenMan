# GreenBot

Automated diary-style commits via [`commit.py`](commit.py). You can rely on **GitHub Actions** (cloud, no PC) or run **locally**.

## Pick how you run

| Approach | Best for |
|----------|----------|
| **GitHub Actions** | Automated runs and push without leaving your PC on |
| **Local `python commit.py`** | Testing, or committing without using Actions |

Both use the same script and rules (`commit.py`, [`.commit_tracker.json`](.commit_tracker.json)).

## GitHub Actions

Workflow: [.github/workflows/activity.yml](.github/workflows/activity.yml).

1. Push this repository to GitHub.
2. In the repo settings, ensure **Actions** are enabled. If pushes were blocked before, workflows need **Workflow permissions → Read and write**.
3. Open **Actions → Natural GitHub Activity Bot → Run workflow** for a manual run, or rely on the schedule triggers.

Cron expressions in the workflow use **UTC** (not IST). Confirm the UTC times against the times you want in your timezone.

The workflow installs Python + `pytz`, runs `commit.py`, rebases onto `origin/main`, and pushes **only when** local `main` is ahead.

## Run locally

From the repository root:

```bash
pip install -r requirements.txt
# If `pip` is not on your PATH (common on Windows), use:
python -m pip install -r requirements.txt
python commit.py
```

Requirements: Python 3, Git on `PATH`, and Git user identity configured.

The script **only creates commits**; it does **not** `git push`. Push yourself or rely on Actions.

### Why nothing happened?

`commit.py` may exit **without committing** when:

- Today’s weekday is not in this week’s random set (stored in `.commit_tracker.json` → `week_data`).
- Today has already reached the daily maximum commits (`max_total`, default **15** in `commit.py`).

That behavior is intentional.

## Logs and wiping files

`commit.py` **appends** to:

- `daily_log.txt`, `progress.md`, `inspiration.txt` (one of these chosen per synthetic commit line)
- `commit_log.txt` (summary after a successful batch)

Truncating those files **does not break** the script. If they are tracked in Git, wiping them creates a large diff until you commit. Clearing them does **not** reset `.commit_tracker.json` (weekly days and daily counts stay unless you edit that JSON).

## Keeping your PC on

- **Actions only:** your computer can be off during scheduled runs.
- **Manual local runs:** PC on only while you execute the commands.
- **Windows Task Scheduler** (if you automate locally): PC must run or wake for the scheduled time (or tasks run later per your scheduler settings).
