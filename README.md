# Behavioral Matchmaking Analysis — League of Legends

## Project Overview
A data analytics project analysing 270,000+ real League of Legends matches 
(sourced from the Riot Games API) to identify which pre-game player behavioral 
signals predict match outcomes — laying the analytical foundation for 
behavioral matchmaking optimization in a live-service gaming environment.

## Business Question
> Can we predict match outcomes using only pre-game behavioral history, 
> and use those signals to build smarter matchmaking that pairs players 
> by complementary playstyle profiles — improving match quality and long-term retention?

## Analytical Approach
1. **Behavioral Analysis** — Engineer player performance features from raw match telemetry (KDA efficiency, gold per minute, vision score, objective participation)
2. **Player Segmentation** — Cluster players into playstyle archetypes using KMeans (e.g. aggressive carry, utility support, consistent farmer)
3. **Predictive Modeling** — Train an XGBoost classifier using pre-game behavioral features to predict win probability, with SHAP explainability to identify key behavioral drivers

## Why Pre-Game Features Only?
Matchmaking happens before champion selection. A realistic matchmaking model 
can only use a player's historical behavioral profile — not what champion 
they pick in a specific game. This design decision makes the model 
directly applicable to real-world matchmaking systems.

## Dataset
Real match data collected via the official Riot Games API.  
270,000+ matches · 900,000+ summoner statistics · 24,000+ unique players  
7 relational tables · Europe, NA and Asia regions · Patch 25.19  
🔗 [Kaggle Dataset](https://www.kaggle.com/datasets/nathansmallcalder/lol-match-history-and-summoner-data-80k-matches)

## Tech Stack
- **Language:** Python
- **Libraries:** pandas, numpy, scikit-learn, xgboost, shap, matplotlib, seaborn
- **Environment:** Google Colab
- **Version Control:** GitHub

## Notebooks
| Notebook | Description |
|----------|-------------|
| 01_eda | Exploratory data analysis across all 7 tables |
| 02_feature_engineering | Table merging, feature engineering, outlier capping |
| 03_clustering | KMeans player archetype segmentation |
| 04_match_prediction | XGBoost match outcome prediction + SHAP explainability |

## Business Application
Findings are directly applicable to live-service game analytics at studios 
like EA and Riot — informing matchmaking algorithm design, player retention 
strategies, and game balance decisions across competitive titles.

## Limitations
- SummonerTbl excluded from public dataset — individual player journeys cannot be tracked across matches
- No session timestamps — player progression over time cannot be measured
- Pre-game only model trades accuracy for matchmaking realism
- Data from one patch window — findings may shift after balance updates
