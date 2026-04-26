# Video Spēļu Vērtējuma Prognozēšana

## Problēma
Vai videospēle saņems augstu RAWG vērtējumu (≥ 4.0)?
Spēļu izdevēji un izstrādātāji var izmantot šo modeli kā pirmo filtru,
lai novērtētu spēles potenciālu **pirms relīzes**, balstoties tikai uz
PRE-release metadatiem.

## Datasets
- Avots: [RAWG Video Game Dataset (Kaggle)](https://www.kaggle.com/datasets/jummyegg/rawg-game-dataset)
- Sākotnējais izmērs: 474 417 rindas × 27 kolonnas
- Pēc filtrēšanas (`ratings_count > 10`): **8 497 spēles × 40 features**
- Target: `rating ≥ 4.0` → 1 (Augsts), citādi → 0 (Zems)
- Klašu sadalījums: 73.8% Zems / 26.2% Augsts

## ⚠️ Data Leakage novēršana
Post-release kolonnas tika **noņemtas pilnībā**:
`completion_rate`, `added_status_*`, `ratings_count`, `reviews_count`,
`suggestions_count`, `achievements_count`. Šīs metrikas zināmas tikai
PĒC spēles iznākšanas un padarītu modeli nereālistisku biznesa kontekstā.

Modelis izmanto **tikai PRE-release datus**: žanru (multi-label),
platformu (multi-label, top 15), izlaišanas gadu, paredzēto spēlēšanas laiku,
nosaukuma garumu, žanru skaitu un sērijas spēļu skaitu.

## Pieeja
- **ML tips:** Klasifikācija (binārā)
- **Pipeline:** `SimpleImputer → StandardScaler → Model` (atbilstoši uzdevuma specifikācijai)
- **MultiLabelBinarizer** žanriem un platformām (saglabā visu informāciju, NEVIS tikai pirmo vērtību)
- **release_year_unknown** flag missing datumiem (NEVIS mediānas aizpilde)
- **Klases disbalanss:** `class_weight='balanced'` un threshold tuning
- **Optimizācija:** GridSearchCV (cv=5) ar `class_weight` parametru telpā

## Rezultāti

| Modelis | CV F1 (vidējais) | CV F1 (std) |
|---------|-----------------|-------------|
| Dummy (most_frequent) — kontrole | 0.000 | ±0.000 |
| Logistic Regression (baseline) | 0.375 | ±0.023 |
| Logistic Regression (balanced) | 0.552 | ±0.016 |
| Random Forest | 0.526 | ±0.017 |
| Random Forest (balanced) | 0.518 | ±0.019 |
| Gradient Boosting | 0.527 | ±0.006 |
| **Random Forest (GridSearchCV + balanced)** | **0.602** | — |

### Test set (Tuned threshold = 0.448)
- **Test F1 (Augsts) = 0.610**
- **Recall (Augsts) = 77.5%** (modelis atrod 3/4 labu spēļu)
- **Precision (Augsts) = 50.3%** (puse no prognozēm pareizas)
- **Accuracy = 74.1%** (vs 73.8% Dummy baseline)

### Top 5 iezīmes
1. `release_year` (0.202) — jaunākas spēles biežāk vērtē augstāk
2. `playtime` (0.143) — ilgāks spēlēšanas laiks korelē ar kvalitāti
3. `genre_indie` (0.083) — indie spēles ir augstāk vērtēta kategorija
4. `name_length` (0.081) — vāja, bet legitīma sakarība
5. `game_series_count` (0.072) — sērijas spēles biežāk augsti vērtētas

## Mapju struktūra
```
final-project/
├── README.md                         # Šis fails
├── requirements.txt
├── data/
│   └── game_info.csv                 # 72.5 MB
├── notebooks/
│   └── final_project.ipynb
├── images/
│   ├── feature_importance.png
│   ├── confusion_matrix.png
│   └── model_comparison.png
└── presentation/
    └── slides.pptx
```

## Kā palaist
1. `pip install -r requirements.txt`
2. Pārliecinies, ka `data/game_info.csv` ir uz vietas (vai lejupielādē no [Kaggle saites](https://www.kaggle.com/datasets/jummyegg/rawg-game-dataset))
3. Atver `notebooks/final_project.ipynb`
4. Izpildi visas šūnas (Kernel → Restart & Run All)

## Autors
**Vladimirs Orlovs** · FITA ML Kurss · 2026
