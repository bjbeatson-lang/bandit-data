# bandit-data

Published data files for [Bandit Football](https://bandit.football).

These are the outputs that the public pages on bandit.football read from. The
modelling and feature engineering happen elsewhere; this repository holds only
the finished files that get served to a page.

---

## What's in here

| File | What it is |
|---|---|
| `air_yards_wr.json` | Wide receiver air yards, current season. Feeds the *Running Hot and Cold* page. |
| `nfl/` | NFL teams, scoreboard and players, current season. See below. |

`air_yards_wr.json` is an array with one record per wide receiver:

| Field | Meaning |
|---|---|
| `receiver_id` | nflverse player ID |
| `full_name`, `short_name`, `team` | Identity |
| `headshot_url` | Player image, via nflverse rosters |
| `games_played`, `targets` | Volume |
| `actual_air_yards` | Air yards actually thrown his way (signed) |
| `projected_air_yards` | What the Bandit model expected, given situation and usage |
| `air_yards_delta` | Actual minus projected — the "hot or cold" number |
| `delta_per_target` | The same, per target |
| `complete_air_yards`, `incomplete_air_yards` | Air-yard opportunity split by outcome, absolute values |
| `bullseye_pct`, `prayer_pct` | Completed and incomplete shares of that opportunity. Sum to 1. |
| `total_epa` | Expected points added |

Note on the two kinds of air yards: `actual_air_yards` is a **signed** sum, so a
throw behind the line of scrimmage counts negative. `complete_air_yards` and
`incomplete_air_yards` use **absolute** values, because they measure opportunity
— a screen five yards behind the line is still five yards of opportunity thrown
someone's way. This is deliberate, and it means the two figures will not add up
to each other.

### The `nfl/` folder

Team, scoreboard and player files for the current NFL season, rebuilt
automatically as new data arrives. Every file has a `season` field and a UTC
time stamp showing when it was built.

| Path | What it is |
|---|---|
| `nfl/teams/index.json` | All 32 teams: name, conference, division, logo, record |
| `nfl/teams/<TEAM>.json` | One team (e.g. `CHI.json`): identity and colors, record, full schedule with results, spread (from that team's side) and over/under, and team stats by game plus season totals |
| `nfl/scoreboard/season_<season>.json` | Every game of the season |
| `nfl/scoreboard/current_week.json` | The current week's games (the earliest week with a game not yet final) |
| `nfl/players/index.json` | Every player with a file: ID, name, team, position, roster status |
| `nfl/players/<gsis_id>.json` | One player, by nflverse player ID: bio, this season game by game, season totals, and career by regular season |

Scoreboard games carry kickoff time (Eastern), status, scores and overtime,
betting favorite with spread and over/under, stadium, roof, surface,
temperature and wind (played games), starting quarterbacks, head coaches, and
nflverse and ESPN game IDs.

Game status is `scheduled` or `final` only. Final scores appear shortly after
each game ends; there are no live in-game scores.

Players included: everyone on a current-season NFL roster (active, reserve,
practice squad and other roster lists), plus anyone with a stat line this season.

Every stat line in a player file has a `half_ppr` field: **Bandit Football
scoring**, half-point PPR with **−1 per interception** (standard scoring uses −2).

---

## Data sources and attribution

This data is built from NFL data accessed through the
[nflverse](https://github.com/nflverse) ecosystem of open-source R packages.

**Play-by-play data and EPA:** Carl S, Baldwin B (2026). *nflfastR: Functions to
Efficiently Access NFL Play by Play Data.* R package version 5.2.0.9014,
<https://nflfastr.com/>

**Data access (rosters, Next Gen Stats, Pro Football Reference stats,
participation):** Ho T, Carl S (2026). *nflreadr: Download 'nflverse' Data.*
R package version 1.5.1.9000, <https://nflreadr.nflverse.com>

**Next Gen Stats:** player tracking data provided by NFL Next Gen Stats,
accessed via nflverse.

**Advanced stats and snap counts:** provided by Pro Football Reference
(pro-football-reference.com), accessed via nflverse.

**Participation data:** participation data from 2023 onward is provided by FTN
Data, accessed via nflverse, released under a CC BY-SA 4.0 license —
attribution to **FTN Data via nflverse**. Participation data prior to 2023 is
provided by NFL Next Gen Stats, accessed via nflverse.

**Game and schedule context (weather, surface, betting lines):** schedule data
maintained by Lee Sharpe, accessed via nflverse.

**Roster and player information:** nflverse roster data.

**Player and team stats by game and by season (`nfl/`):** nflverse player and
team stats, built from nflverse play-by-play. **Player bios:** nflverse players
data. **Team names, colors and logos:** nflverse teams data; logo links point
to images hosted by ESPN.

## Modifications

**These files are derived data, not raw nflverse data.** Per CC BY-SA 4.0's
requirement to indicate changes, here is what was done to it:

- Play-by-play records were aggregated from the play level to one row per
  receiver per season.
- Bandit-defined concepts were computed on top of the source data — target
  depth bands, field position and down-and-distance context, past-the-sticks
  intent, and rolling player and team histories.
- `projected_air_yards` is **model output**, not observed data. It comes from a
  gradient-boosted model trained on 2018–2023 seasons, tuned against 2024, and
  scored on data it did not see during training.
- `bullseye_pct` and `prayer_pct` are Bandit-defined measures, computed from
  absolute air yards so that they behave as shares of a receiver's own
  opportunity and sum to 1.
- Records are filtered to wide receivers.

For the `nfl/` folder:

- nflverse schedule, roster, bio and stats data were reorganized into one file
  per team, per player and for the scoreboard.
- `half_ppr` is a Bandit-defined score: nflverse standard fantasy points, plus
  1 per interception thrown, plus 0.5 per reception.
- In team files, the spread is converted to that team's own side (nflverse
  lists it from the home team's side).

No raw nflverse file is redistributed here in its original form.

---

## License

Released under
[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/), the same
license as the FTN charting data it is derived from.

You may share and adapt these files, including commercially, provided you give
attribution, indicate changes you make, and distribute your version under the
same license.

---

## Updates

Refreshed during the NFL season after the week's games complete.

The `nfl/` folder updates on its own schedule: scores and team records shortly
after each game ends, player and team stats each morning after nflverse
posts them, and rosters once a day. Only files whose data actually changed are
updated.

Note on timing, because it affects what any given refresh contains: nflverse
core play-by-play updates several times on game days, while FTN charting is
published roughly 48 hours after each game. A refresh run soon after Sunday's
games therefore reflects complete play-by-play but not yet that week's charting.
