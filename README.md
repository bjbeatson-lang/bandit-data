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

---

## Data sources and attribution

Built on the [nflverse](https://nflverse.nflverse.com/) data ecosystem.

Play-by-play, rosters, snap counts, Next Gen Stats and Pro Football Reference
advanced stats are accessed via [nflreadr](https://nflreadr.nflverse.com/).

Charting data is from **FTN Data**, and per its license, attribution is made to
**ftndata.com via nflverse**.

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

Note on timing, because it affects what any given refresh contains: nflverse
core play-by-play updates several times on game days, while FTN charting is
published roughly 48 hours after each game. A refresh run soon after Sunday's
games therefore reflects complete play-by-play but not yet that week's charting.
