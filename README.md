# Behavioral Matchmaking Analysis — League of Legends

Predicting match outcomes from player behavior, and uncovering what actually drives winning — using 270,000+ real League of Legends matches collected via the Riot Games API.

---

## TL;DR

This project set out to predict League of Legends match outcomes from player behavior, with the goal of informing **behavioral matchmaking** — pairing players by playstyle and skill rather than a single MMR number.

The key finding emerged from a deliberate two-model comparison:

| Model | Question | Accuracy | AUC |
|-------|----------|----------|-----|
| **Pre-game** | Can a player's *history* predict a match they're about to play? | **50%** | **0.508** |
| **Explanatory** | Which *in-game* behaviors distinguish wins from losses? | **80%** | **0.884** |

The 30-point gap is the story: **in-game behavior is strongly tied to winning, but that behavior is unknowable before the match** — and individual history alone can't predict a 10-player outcome that matchmaking deliberately balances to ~50/50. Recognizing *why* the pre-game model lands at chance is the core analytical insight.

---

## Business Context

League of Legends is a live-service competitive game — the same category as EA titles like Apex Legends and EA SPORTS FC Ultimate Team. Studios running these games care about:

- **Matchmaking quality** — balanced games keep players engaged
- **Player retention** — chronic losing drives churn
- **Behavioral understanding** — what separates successful players from struggling ones

This project applies the analytical workflow those teams use: behavioral feature engineering, player segmentation, predictive modeling, and explainability — translated into business recommendations.

---

## Dataset

Real match data collected via the official Riot Games API.

- **270,000+ matches** · **900,000+ summoner records** · **24,000+ players**
- **7 relational tables** (match stats, team data, summoner-match links, rank, champion, item lookups)
- Europe, NA, and Asia regions · sampled Unranked → Challenger
- 🔗 [Kaggle source](https://www.kaggle.com/datasets/nathansmallcalder/lol-match-history-and-summoner-data-80k-matches)

*Note: SummonerTbl was excluded from the public release for privacy, which shapes a key limitation (see below).*

---

## Approach

### 1. Behavioral Feature Engineering ([notebook 02](notebooks/02_feature_engineering.ipynb))
Merged the 7-table relational schema and engineered player performance features: KDA ratio, gold per minute, damage per minute, CS per minute, vision score, and objective participation. Outliers capped at the 99th percentile.

**Key methodological step:** to build a legitimate *pre-game* model, behavioral features were re-engineered as **historical averages** — for each match, a player's stats from their *previous* matches only (using `groupby` + `shift` + expanding mean), never the current game. This avoids the circularity of using a game's own stats to "predict" its result.

### 2. Player Archetype Clustering ([notebook 03](notebooks/03_clustering.ipynb))
KMeans clustering (k=4, chosen via elbow method) on historical behavioral features segmented players into playstyle archetypes: **The Carry**, **The Vision Support**, **The Playmaker**, and **The Inconsistent**.

A revealing check: archetypes built on *historical* features showed only a ~2-point win-rate spread, versus a 42-point spread when (incorrectly) built on in-game features. That collapse is direct evidence the original spread was leakage — playstyle alone barely predicts a single match.

### 3. Pre-Game Predictive Model ([notebook 04](notebooks/04_match_prediction.ipynb))
XGBoost trained on pre-game features only (historical behavior + role + archetype). Result: **50% accuracy, AUC 0.508** — essentially chance.

![Pre-game SHAP](outputs/shap_pregame_importance.png)

*Even the most influential pre-game feature shifts win probability by only ~3.5%.*

### 4. Explanatory Model ([notebook 05](notebooks/05_explanatory_analysis.ipynb))
A second model using *in-game* behavioral features to answer a descriptive question: **which behaviors distinguish wins from losses?** Logistic regression (AUC 0.873) and XGBoost (AUC 0.884) performed nearly identically — indicating a largely linear relationship.

![Explanatory SHAP](outputs/shap_explanatory_importance.png)

*The same features now shift predictions by up to 1.42 — ~40x the pre-game effect.*

---

## Key Findings

**1. Pre-game prediction hits a principled wall.**
Individual player history cannot predict a team-based outcome that matchmaking deliberately balances. The near-chance result is partly *evidence the matchmaking is working*.

**2. In-game behavior strongly explains winning (80%, AUC 0.884).**
The dominant differentiators are KDA ratio, gold efficiency, and objective participation.

**3. Not all important features are actionable.**
KDA dominates the model, but it's tightly coupled with winning by nature (low deaths ≈ winning team) — more a *signature* of a won game than a lever. The actionable insights are gold efficiency, objective control, and role-specific play.

**4. Vision score matters less than conventional wisdom suggests.**
At the individual level, vision score was a surprisingly weak differentiator — a data-driven challenge to community assumptions.

---

## Business Implications

- **Matchmaking** would require *team-level* features (aggregated skill across all 10 players, skill differentials between teams) — not available in this single-player-per-row dataset, but the clear direction for a production system.
- **Player development / coaching** tools could target the actionable drivers (gold efficiency, objective participation) rather than raw KDA.
- **Game design** could revisit how vision is rewarded, given its weak individual-level impact.

---

## What Would Make It Better

The biggest limitation is **incomplete team information**: each row is one player, with no reliable way to reconstruct which 5 players shared a team. A dataset preserving team composition per match would enable the team-level skill aggregation that real matchmaking depends on — and is the single most promising extension of this work.

---

## Tech Stack

**Python** · pandas · numpy · scikit-learn · XGBoost · SHAP · matplotlib · seaborn
**Environment:** Google Colab · **Version control:** GitHub

---

## Notebooks

| Notebook | Focus |
|----------|-------|
| [01_eda](notebooks/01_eda.ipynb) | Exploratory analysis across 7 relational tables |
| [02_feature_engineering](notebooks/02_feature_engineering.ipynb) | Table merging + historical behavioral features |
| [03_clustering](notebooks/03_clustering.ipynb) | KMeans player archetype segmentation |
| [04_match_prediction](notebooks/04_match_prediction.ipynb) | Pre-game predictive model (the 50% finding) |
| [05_explanatory_analysis](notebooks/05_explanatory_analysis.ipynb) | What in-game behaviors drive winning (80%) |
