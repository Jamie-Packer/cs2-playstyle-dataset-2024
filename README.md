# CS2 Playstyle & Roles Dataset (2024)
[![DOI](https://zenodo.org/badge/1069609087.svg)](https://doi.org/10.5281/zenodo.17264782)


**Single-file dataset** capturing player playstyle features and roles across professional Counter-Strike 2 matches (2024).  
It’s designed for quick exploration (clustering, similarity search, role analysis) and to be easy to load in Python/R.

- **File:** `data/cs2_playstyle_roles_2024.csv`
- **Rows:** players (unique by `steamid`)
- **Key features:** opening attempts (OAP), trading behaviour (PODT/POKT), time-alive-per-death (TAPD), and positioning **team-relative ranking** metrics (ADNT/ADAT), plus per-side and overall role labels.

> **License:** CC BY 4.0 (attribution required).  
> **Credit:** dataset engineered with the help of the [`awpy`](https://github.com/pnxenopoulos/awpy) library; role labels derived from Harry Richards’ [(@NER0cs)](https://x.com/NER0cs) [Positions Database](https://public.tableau.com/app/profile/harry.richards4213/viz/OLDPositionsDatabaseArchived/PositionsDatabaseNER0cs) (credited below). Special Thanks!

---

## What’s inside

- **Identifiers:** `steamid`, `player_name`, `team_clan_name`, `map_count`
- **Playstyle features (per-side + overall):**
  - `tapd_*` – Time Alive Per Death (seconds)
  - `oap_*` – Opening Attempt % (0–100)
  - `podt_*` – % of deaths that were traded (0–100)
  - `pokt_*` – % of kills that were trades (0–100)
- **Positioning (team-relative ranking, 0.2–1.0):**
  - `adnt_rank_*` – relative rank for distance to nearest teammate
  - `adat_rank_*` – relative rank for distance from team centre
- **Roles:** `role_t`, `role_ct`, `role_overall`

See the full column descriptions in **[`docs/DATA_DICTIONARY.md`](docs/DATA_DICTIONARY.md)**.

---

## How the features were built (short version)

- Demos were parsed with **awpy** to produce per-round/player events and pre-plant tick snapshots.
- **TAPD**: average seconds alive at time of death.  
- **OAP**: opening attempt rate = (opening duels participated) / (team rounds) × 100.  
- **PODT/POKT**: traded-death% / trade-kill% within a 5-second window.  
- **Positioning (ADNT/ADAT)**: computed from 3D player positions every 3s, starting 10s after freeze end, **pre-plant only**, **alive-only**, and **only when a full 5v5 is alive** (to focus on structured setups).
  - For each map and team, per-player map-level means are converted to **team-relative ranking scores** in (0.2–1.0], where 0.2 ≈ closest to teammates and 1.0 ≈ furthest.
  - Ranks are then **round-weighted across maps** to produce `*_t`, `*_ct`, and `*_overall`.

> Rationale for design: we target **initial round tendencies** (pre-plant, 5v5) and avoid map-size/veto bias by using team-relative ranks instead of absolute distances.

## Data coverage & limitations

- **Demo selection:** Demos were collected from professional Counter-Strike 2 matches played in 2024.  
  Only `.dem` files that were publicly available and successfully parsed by `awpy` were included.  
  Some matches may be absent if demos were missing, corrupted, or caused parse failures.

- **Role coverage:** Not every player has a role label.  
  Role data come from [Harry Richards’ Positions Database](https://public.tableau.com/app/profile/harryrichards);  
  players or teams not covered by that source are left blank in the `role_t`, `role_ct`, and `role_overall` columns.


---

## Load & use

**Python (pandas)**
```python
import pandas as pd
df = pd.read_csv("data/cs2_playstyle_roles_2024.csv")
```

**R (readr)**
```r
library(readr)
df <- read_csv("data/cs2_playstyle_roles_2024.csv")
```

---

## Licensing & credits

- **License:** Creative Commons **CC BY 4.0** (see `LICENSE`). You may use, share, and adapt with attribution.
- **Please attribute:**  
  - Jamie Packer (this repo).  
  - [**awpy**](https://github.com/pnxenopoulos/awpy) (demo parsing).  
  - [**Harry Richards’ Positions Database**](https://public.tableau.com/app/profile/harry.richards4213/viz/OLDPositionsDatabaseArchived/PositionsDatabaseNER0cs) (source for role labels).

Suggested attribution line:
> “This work uses the CS2 Playstyle & Roles Dataset (2024), created by Jamie Packer, generated using awpy; role labels adapted from Harry Richards’ Positions Database. Licensed under CC BY 4.0.”

---

## How to cite

If you create a paper, post, or tool using this dataset, please cite the repository.
Please also credit [**Harry Richards’ Positions Database**](https://public.tableau.com/app/profile/harry.richards4213/viz/OLDPositionsDatabaseArchived/PositionsDatabaseNER0cs) when roles are used.

```
Jamie Packer (2024). CS2 Playstyle & Roles Dataset (2024). GitHub repository.
```


