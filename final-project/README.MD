# Video Spēļu Vērtējuma Prognozēšana

## Problēma
Vai videospēle saņems augstu RAWG vērtējumu (≥ 4.0)?
Spēļu izdevēji var izmantot šo modeli kā pirmo filtru, lai novērtētu spēles potenciālu pirms relīzes.

## Datasets
- Avots: [RAWG Video Game Dataset (Kaggle)](https://www.kaggle.com/datasets/jummyegg/rawg-game-dataset)
- Izmērs: 474 417 rindas → 8 497 pēc filtrēšanas × 17 kolonnas
- Target kolonna: `rating` (≥ 4.0 = 1, < 4.0 = 0)

## Pieeja
- ML tips: Klasifikācija (binārā)
- Pipeline: filtrēšana → trīrīšana → feature engineering → StandardScaler → RandomForest
- Optimizācija: GridSearchCV (max_depth=20, n_estimators=200)

## Rezultāti
| Modelis | CV F1 |
|---------|-------|
| Bāzes (Logistic Regression) | 0.609 |
| Random Forest | 0.664 |
| **Random Forest (GridSearchCV)** | **0.669** |

- Accuracy = 84%
- Galvenā iezīme: `completion_rate` (importance: 0.194)

## Kā palaist
1. `pip install -r requirements.txt`
2. Ielādē datasetu no Kaggle saites augstāk → saglabā `data/game_info.csv`
3. Atver `notebooks/final_project.ipynb`
4. Izpildi visas šūnas (Kernel → Restart & Run All)

## Autors
Vladimirs Orlovs · FITA ML Kurss · 2026
