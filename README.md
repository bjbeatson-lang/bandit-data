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
| `college/` | College football (FBS and FCS) teams, scoreboard and players, current season. See below. |

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
| `nfl/advanced/<gsis_id>.json` | Advanced stats for one quarterback, running back, wide receiver, tight end or defender, current season: Next Gen Stats season to date (when he meets the NFL's qualifying minimum), Pro Football Reference rushing, receiving and passing detail by game, FTN charting by game, and for defenders Pro Football Reference pass rush, tackling and coverage by game |
| `nfl/advanced/team_ftn.json` | FTN team rates for all 32 teams and the NFL. Offense: where the QB lines up, backfield at the snap, motion, play action, RPO, screen, no-huddle. Defense: blitz rate, number of pass rushers, 8+ in the box against the run |

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

Advanced files: quarterbacks get pressure, blitz, bad-throw, out-of-pocket,
interception-worthy, throwaway and QB-fault sack counts by game, and their runs
split into scrambles, sneaks and other designed runs. Running backs and
quarterbacks get yards before and after contact and broken tackles by game.
Running backs, wide receivers and tight ends get targets by game with
catchable, contested and drop counts. A field is `null` when that source hasn't
posted the game, or has no row for the player in it.

`team_ftn.json` rates are shares of a team's runs and passes, including plays
wiped out by a penalty after the snap. Screens and play action are shares of
all those plays, not of passes only. FTN's backfield count is anyone besides the
quarterback lined up behind the line of scrimmage, inside the end man on the
line, at any depth.

Team defense rates are shares of opponents' plays that stood: blitz rate and
rusher counts out of opponents' dropbacks, and the 8+ box share out of
opponents' designed runs. In FTN's charting a blitz is any rush of five or
more, and blitzes aren't charted on running plays.

### The `college/` folder

Team, scoreboard and player files for the current college football season, covering FBS and
FCS. Rebuilt automatically as new data arrives. Every file has a `season` field and a UTC time
stamp showing when it was built.

| Path | What it is |
|---|---|
| `college/teams/index.json` | Every FBS and FCS team: school, conference, division, logo, record |
| `college/teams/<team_id>.json` | One team, by ESPN team ID: identity and colors, overall and conference record, full schedule with results, spread and over/under from that team's side, team stats by game plus season totals, players with stats this season, and the full roster |
| `college/scoreboard/season_<season>.json` | Every game involving an FBS or FCS team |
| `college/scoreboard/current_week.json` | The current week's games |
| `college/players/index.json` | Every player with a file: ID, name, team, position, key season numbers |
| `college/players/<espn_id>.json` | One player, by ESPN athlete ID: bio, this season game by game, season totals |

Games carry kickoff time (Eastern), status, scores, neutral-site and conference-game flags,
venue, attendance, game notes (bowl and kickoff-classic names), playoff round, and spread and
over/under where available.

Coverage: schedules and scores cover every FBS and FCS game. Box scores and player stats
cover games involving an FBS team (FBS vs FBS, and FBS vs FCS); games between two FCS
teams have a score but no box score.

Players included: anyone on an FBS or FCS team with at least one stat line this season. Full
rosters are listed in each team file.

Every stat line in a player file has a `half_ppr` field: **Bandit Football scoring**,
half-point PPR with **-1 per interception**. College box scores don't include two-point
conversions, so those aren't counted.

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

**Charting data (`nfl/advanced/`):** QB alignment, backfield, motion, play
action, screens, RPOs, no-huddle, sneaks, blitzers, pass rushers, defenders in
the box, catchable and contested
balls, drops, interception-worthy throws, throwaways, out-of-pocket plays and
QB-fault sacks are provided by FTN Data, accessed via nflverse, released under
a CC BY-SA 4.0 license — attribution to **FTN Data via nflverse**.

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

**College football (`college/`):** data from the
[SportsDataverse](https://github.com/sportsdataverse/sportsdataverse-data) data repository,
released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). It is the data
behind cfbfastR: Gilani S, Easwaran A, Lee J, Hess E (2026). *cfbfastR: Access College
Football Play by Play Data.* <https://cfbfastR.sportsdataverse.org/>,
doi:10.32614/CRAN.package.cfbfastR. Underlying game, box score and roster data from ESPN;
logo links point to images hosted by ESPN.

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

For the `nfl/advanced/` folder:

- FTN's play-level charting was joined to nflverse play-by-play by game and
  play ID, then counted per player per game and per team.
- Team rates are Bandit-defined: shares of a team's runs and passes, including
  plays wiped out by a penalty after the snap.
- Quarterback runs are split three ways: scrambles (nflverse scramble flag),
  sneaks (FTN's sneak tag) and other designed runs. Kneel-downs are not runs.
- QB-fault sacks are counted only on sacks that stood.
- Team defense rates are Bandit-defined shares of opponents' dropbacks and
  designed runs, from FTN's per-play rusher, blitzer and box counts.
- For defenders, Pro Football Reference leaves yards allowed blank when nothing
  was completed; those are recorded as 0.
- Pro Football Reference rows were matched to nflverse player IDs and kept by
  game; Next Gen Stats rows are the season-to-date summary.

For the `college/` folder:

- SportsDataverse schedule, team, roster and box score data were reorganized into one file per
  team, per player and for the scoreboard.
- Box score lines, which arrive as one row per stat category, were combined into one row per
  player per game.
- `half_ppr` is a Bandit-defined score: 0.04 per passing yard, 4 per passing TD, -1 per
  interception thrown, 0.1 per rushing or receiving yard, 6 per rushing, receiving or return
  TD, 0.5 per reception, -2 per fumble lost.
- In team files, the spread is converted to that team's own side.

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

The `nfl/advanced/` folder is rebuilt by hand for now, after Next Gen Stats,
Pro Football Reference and FTN have posted the week's games.

The `college/` folder updates every two hours on Saturdays during the season (scores and box
scores) and fully each Sunday morning. Only files whose data actually changed are updated.

Note on timing, because it affects what any given refresh contains: nflverse
core play-by-play updates several times on game days, while FTN charting is
published roughly 48 hours after each game. A refresh run soon after Sunday's
games therefore reflects complete play-by-play but not yet that week's charting.
