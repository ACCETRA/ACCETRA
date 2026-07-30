name: heartbeat

# Stamps the Last boot line in the README once a day, so the profile has one
# genuinely live value on it. Optional. Delete this file if you would rather
# not have a daily commit on the profile repo.

on:
  schedule:
    - cron: "17 22 * * *"   # 03:17 PKT
  workflow_dispatch:

permissions:
  contents: write

jobs:
  stamp:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Stamp last boot
        run: |
          STAMP="$(TZ=Asia/Karachi date '+%d %b %Y, %H:%M PKT')"
          sed -i "s|<!-- boot:start -->.*<!-- boot:end -->|<!-- boot:start -->$STAMP<!-- boot:end -->|" README.md

      - name: Commit if changed
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add README.md
          git diff --quiet --cached || git commit -m "chore: stamp last boot"
          git push
