# Data Source

## Dataset
**League of Legends Match History and Summoner Data (80K+ Matches)**

## Source
Kaggle — collected via the official Riot Games API

🔗 https://www.kaggle.com/datasets/nathansmallcalder/lol-match-history-and-summoner-data-80k-matches

## About the Data
Real match and player data from League of Legends collected across Europe, NA, and Asia regions. 
Contains 270,000+ matches and 900,000+ summoner statistics from Patch 25.19, 
sampled across all rank tiers from Unranked to Challenger.

## Files (7 tables)
| File | Description |
|------|-------------|
| MatchStatsTbl.csv | Player-level stats per match — KDA, gold, damage, vision score, win/loss |
| MatchTbl.csv | Match metadata — rank tier, game duration, queue type, patch |
| TeamMatchTbl.csv | Team-level objective stats — dragon/baron/tower kills per team |
| SummonerMatchTbl.csv | Bridge table linking summoner → match → champion |
| RankTbl.csv | Rank ID to rank name mapping |
| ChampionTbl.csv | Champion ID to champion name mapping |
| ItemTbl.csv | Item ID to item name mapping |

## Usage Note
Raw CSV files are not tracked in this repository.
Download the dataset from the Kaggle link above and place files 
in this folder locally before running the notebooks.
