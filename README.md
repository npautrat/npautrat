name: Update LoL Stats

on:
  schedule:
    - cron: '0 */6 * * *'
  workflow_dispatch:

permissions:
  contents: write

jobs:
  update-readme:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Fetch LoL Stats
        run: |
          SUMMONER="Sollipitie-TDB"
          
          STATS_HTML="<table>\n  <tr>\n    <td align=\"center\" width=\"50%\">\n      <b>OP.GG Profile</b><br/><br/>\n      <a href=\"https://www.op.gg/summoners/euw/${SUMMONER}\">\n        <img src=\"https://img.shields.io/badge/LoL-Sollipitie%23TDB-blue?style=for-the-badge&logo=leagueoflegends\" />\n      </a>\n    </td>\n    <td align=\"center\" width=\"50%\">\n      <b>Live Rank Status</b><br/><br/>\n      <a href=\"https://www.op.gg/summoners/euw/${SUMMONER}\">\n        <img src=\"https://img.shields.io/badge/Status-OP.GG%20Verified-success?style=for-the-badge\" />\n      </a>\n    </td>\n  </tr>\n</table>"
          
          python3 -c "
          with open('README.md', 'r') as f:
              content = f.read()
          start_marker = '<!--LOL-STATS-START-->
<table>
  <tr>
    <td align="center" width="50%">
      <b>OP.GG Profile</b><br/><br/>
      <a href="https://www.op.gg/summoners/euw/Sollipitie-TDB">
        <img src="https://img.shields.io/badge/LoL-Sollipitie%23TDB-blue?style=for-the-badge&logo=leagueoflegends" />
      </a>
    </td>
    <td align="center" width="50%">
      <b>Live Rank Status</b><br/><br/>
      <a href="https://www.op.gg/summoners/euw/Sollipitie-TDB">
        <img src="https://img.shields.io/badge/Status-OP.GG%20Verified-success?style=for-the-badge" />
      </a>
    </td>
  </tr>
</table>
<!--LOL-STATS-END-->'
          new_content = content.split(start_marker)[0] + start_marker + '\n' + '''$STATS_HTML''' + '\n' + end_marker + content.split(end_marker)[1]
          with open('README.md', 'w') as f:
              f.write(new_content)
          "

      - name: Commit & Push changes
        run: |
          git config --global user.name 'github-actions[bot]'
          git config --global user.email 'github-actions[bot]@users.noreply.github.com'
          git add README.md
          git diff-index --quiet HEAD || git commit -m "auto: update LoL stats"
          git push
