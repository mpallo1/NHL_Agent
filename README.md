# NHL Line Scout — v1

A single-file NHL puck line model. Runs entirely in the browser. No server, no build step.

## Sync Order (run once per session, in this order)

| Step | Site | What it grabs |
|------|------|---------------|
| 1 | `moneypuck.com/teams.htm` | Team xGF%, HDCF%, SV%, PP%, PK%, PDO |
| 2 | `naturalstattrick.com/teamtable.php` | 5v5 CF%, FF%, xGF% |
| 3 | `dailyfaceoff.com/starting-goalies` | Confirmed starters, projected lines & D-pairs, impact player flags |
| 4 | `espn.com/nhl/scoreboard` | Live puck lines, game slate, playoff flags |

Open each site in Chrome, paste the generated console script (F12 → Console), data syncs back automatically via BroadcastChannel.

## Model Components

| Weight | Component | Source |
|--------|-----------|--------|
| xg (25%) | xGF% share differential | MoneyPuck |
| shotquality (20%) | HDCF% edge | MoneyPuck |
| goalie (20%) | Starter SV% × 30 shots (falls back to team SV%) | Daily Faceoff + NST |
| powerplay (15%) | PP% + PK% combined | MoneyPuck |
| recency (10%) | 5v5 xGF% hot/cold trend | NST |
| context (10%) | B2B fatigue, road trip depth | ESPN |

Home ice: +0.22 goals (research-backed). Zero for playoffs and neutral sites.

## Lineup Intelligence

Daily Faceoff sync provides:
- **Confirmed starters** — starter name + confidence (confirmed / expected / unconfirmed) displayed on every game card
- **Individual SV%** — starter-level save percentage replaces blended team SV% in the goalie formula when available
- **Impact player flags** — 36 tracked players league-wide; absent players flagged with ⚠ on the game card and sent to the model in the analysis prompt

## Output Per Game

- **Model puck line** — xG formula output rounded to nearest 0.5
- **Puck line edge** — model goals vs market ±1.5
- **ML lean** — FAV / DOG / EVEN with HIGH / MED / LOW confidence (separate from puck line edge)
- **PDO flag** — teams above 102 PDO tagged for regression risk
- **Starter panel** — both goalies named with SV% and confirmation status
- **Impact flags** — missing players highlighted per team

## Training Loop

Post-mortem analysis (Performance tab) computes Pearson correlation per component across all archived games, identifies systematic over/underweighting, and recommends weight adjustments. Weight changelog tracks MAE before/after every change.

## Deployment

Single `index.html`. Deploy to GitHub Pages — no dependencies beyond Google Fonts.
