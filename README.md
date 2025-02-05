# GitHub Commit Reminder

This GitHub Action checks if you have committed to your repository today. If no commits are found for the day, it sends a reminder notification via email.

## Features
- Runs daily at 6 PM UTC using a cron schedule.
- Checks if a commit has been made today.
- Sends an email notification if no commit is found.
- Can be manually triggered via workflow dispatch.

## Setup Instructions

### 1️⃣ Enable GitHub Actions
Ensure GitHub Actions is enabled for your repository.

### 2️⃣ Create the Workflow File
Save the following workflow as `.github/workflows/commit-reminder.yml` in your repository:

```yaml
name: GitHub Commit Reminder

on:
  schedule:
    - cron: "0 18 * * *"  # Runs every day at 6 PM UTC ( if you're in VietNam: "0 11 * * *"  # Runs at 6 PM Vietnam time (11 AM UTC))
  workflow_dispatch:  # Allows manual execution

jobs:
  check-commit:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v2

      - name: Get Latest Commit Date
        id: commit-date
        run: |
          LAST_COMMIT=$(git log -1 --format=%ct || echo 0)
          echo "LAST_COMMIT=$LAST_COMMIT" >> $GITHUB_ENV

      - name: Get Today's Date
        id: today-date
        run: echo "TODAY=$(date +%s)" >> $GITHUB_ENV

      - name: Check if committed today
        run: |
          if [ $LAST_COMMIT -eq 0 ]; then
            echo "No commits found in the repository."
            exit 1
          elif [ $(($TODAY - $LAST_COMMIT)) -gt 86400 ]; then
            echo "No commit today! Sending a reminder..."
            exit 1
          else
            echo "Commit already made today. You're good! ✅"
          fi

      - name: Send Notification (via Email)
        if: failure()
        uses: dawidd6/action-send-mail@v3
        with:
          server_address: smtp.gmail.com
          server_port: 465
          username: ${{ secrets.EMAIL_USERNAME }}
          password: ${{ secrets.EMAIL_PASSWORD }}
          subject: "🚀 Reminder: Commit to GitHub Today!"
          body: "You haven't committed yet today! Keep your streak alive! 🔥"
          to: your-email@example.com
          from: GitHub Reminder Bot
```

### 3️⃣ Configure Email Notifications
Since GitHub Actions cannot directly send emails, this workflow uses `dawidd6/action-send-mail@v3`.

#### Add GitHub Secrets:
Go to **Settings > Secrets and Variables > Actions > New Repository Secret** and add:
- `EMAIL_USERNAME`: Your email address.
- `EMAIL_PASSWORD`: App password for SMTP authentication (to open app passwords, make sure you turn on your email two steps verification: [Gmail App Password](https://myaccount.google.com/apppasswords)).

### 4️⃣ Commit and Push
Commit the workflow file and push it to your repository. The action will automatically run daily and notify you if no commit is detected.


Enjoy keeping your GitHub streak alive! 🚀

