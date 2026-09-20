# predicting future kaggle top performers

This repository contains my solution for the NEOAI 2026 Kaggleforces competition. The task was to rank users by their probability of finishing in the top 3 percent of a future Featured competition.

## a small clarification

The organizers provided the task, dataset, evaluation setup, statistical context, and a starter baseline. I used that baseline as a starting point instead of pretending this appeared from a completely blank notebook.

I wrote and adapted the Kaggle code myself, including the time-aware feature engineering, leakage control, validation, model training, and submission pipeline.

I am currently studying statistics independently. I use the statistical terms that the problem actually needs, but I am not pretending I have already mastered the whole subject. learning in public, basically.

## approach

Raw leaderboard positions are not directly comparable cuz competitions have very different numbers of teams. Rank 100 can slay in one competition and flop in another. That is why I convert public and private positions into relative ranks:

```text
relative rank = leaderboard rank / total teams
```

For every user, I build a historical profile using:

- number of previous competitions
- average and best relative rank
- submission statistics
- previous top-3-percent finishes
- medal and gold rates
- public-to-private rank changes
- recent performance
- score-gap and reward features

A `HistGradientBoostingClassifier` converts these features into a continuous ranking score.

## preventing leakage

Each training row uses only competitions that happened earlier. Expanding and rolling statistics are shifted by one row, so the current result cannot appear in its own features.

Validation is also time-based. Older competitions are used for training and newer competitions are used for evaluation.

## output

The final model is retrained on all usable historical rows. The notebook writes `submission.csv` with two columns:

```text
Id,pred_score
```

Higher values indicate a higher predicted chance of finishing in the top 3 percent.

## files

- `kaggleforces-future-gm-scout.ipynb`: feature engineering, validation, training, and submission code
- `README.md`: project overview

## possible improvements

- competition difficulty features
- exponentially weighted recent form
- CatBoost or LightGBM blends
- competition-level rank normalization
- out-of-fold encodings
