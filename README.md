# GitHub Commit Reminder

This GitHub Action sends a daily email reminder regardless of whether a commit has been made. It runs at 6 PM Vietnam time and provides the current date and time in the notification.

## Features
- Runs daily at 6 PM Vietnam time using a cron schedule.
- Sends an email notification every day.
- Includes the current date and time in the email.
- Can be manually triggered via workflow dispatch.

## Setup Instructions

### 1️⃣ Enable GitHub Actions
Ensure GitHub Actions is enabled for your repository.

### 2️⃣ Create the Workflow File
Save the following workflow as `.github/workflows/commit-reminder.yml` in your repository:

```yaml
name: Daily Email Reminder

on:
  schedule:
    - cron: "0 11 * * *"  # Runs at 6 PM Vietnam time (11 AM UTC)
  workflow_dispatch:  # Allows manual execution

jobs:
  send-email:
    runs-on: ubuntu-latest
    steps:
      - name: Set Date & Time Variables
        run: |
          echo "CURRENT_DATE=$(date +"%A, %B %d, %Y")" >> $GITHUB_ENV
          echo "CURRENT_TIME=$(date +"%H:%M:%S UTC")" >> $GITHUB_ENV

      - name: Send Daily Reminder Email
        uses: dawidd6/action-send-mail@v3
        with:
          server_address: smtp.gmail.com
          server_port: 465
          username: ${{ secrets.EMAIL_USERNAME }}
          password: ${{ secrets.EMAIL_PASSWORD }}
          subject: "🚀 Daily GitHub Reminder"
          body: |
            Hi there! 👋

            This is your **daily reminder** from GitHub! 🚀  
            Stay on track and keep pushing forward.  

            📅 **Date:** ${{ env.CURRENT_DATE }}  
            ⏰ **Current Time (UTC):** ${{ env.CURRENT_TIME }}  

            Best,  
            **GitHub Reminder Bot**
          to: your-email@gmail.com
          from: "GitHub Reminder Bot <your-email@gmail.com>"

      - name: Confirm Email Sent
        run: echo "✅ Email sent successfully on ${{ env.CURRENT_DATE }} at ${{ env.CURRENT_TIME }}"
```

### 3️⃣ Configure Email Notifications
Since GitHub Actions cannot directly send emails, this workflow uses `dawidd6/action-send-mail@v3`.

#### Add GitHub Secrets:
Go to **Settings > Secrets and Variables > Actions > New Repository Secret** and add:
- `EMAIL_USERNAME`: Your email address.
- `EMAIL_PASSWORD`: App password for SMTP authentication (Ensure two-step verification is enabled for your email: [Gmail App Password](https://myaccount.google.com/apppasswords)).

### 4️⃣ Commit and Push
Commit the workflow file and push it to your repository. The action will automatically run daily and send an email reminder.

---

Stay productive with your daily GitHub reminder! 🚀

