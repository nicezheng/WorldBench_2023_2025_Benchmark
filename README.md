# WorldBench 2023–2025

A benchmark for country-level factual recall, containing **2,112 questions across 216 countries and territories and 11 development indicators**. It updates the reference data used by [WorldBench](https://doi.org/10.1145/3630106.3658967) to the **2023–2025** window.

## Files

| File                             | Contents                                                            |
| -------------------------------- | ------------------------------------------------------------------- |
| `data/ground_truth.csv`        | Questions, reference values, annual values, units, and source links |
| `data/questions.jsonl`         | The same questions with one-shot chat messages, ready for inference |
| `data/annual_observations.csv` | Source observations for 2023, 2024, and 2025                        |
| `metadata/indicators.csv`      | Indicator definitions, units, source codes, and coverage            |
| `metadata/countries.csv`       | Country names, codes, regions, and income groups                    |
| `examples/swiss_examples.json` | Swiss question–answer examples used in the prompts                 |

The indicators cover population, GDP, GDP per person employed, agricultural land, education expenditure, access to electricity, CO₂ emissions, maternal mortality, renewable energy, unemployment, and women in parliament.

## Reference data

Ten indicators come from the World Bank's [World Development Indicators](https://databank.worldbank.org/source/world-development-indicators). Renewable-energy consumption comes from the [UNSD Global SDG Indicators Database](https://unstats.un.org/sdgs/dataportal), series `EG_FEC_RNEW` (SDG 7.2.1). The frozen data use the World Bank's October 8, 2026 update and the UNSD snapshot retrieved on October 10, 2026.

For each question, `ground_truth` is the mean of the available 2023–2025 observations. `gt_2023`, `gt_2024`, and `gt_2025` give the annual values; `gt_n_years` counts the contributing years. Blank cells denote missing observations. `ground_truth_full_precision` stores the unrounded mean; `ground_truth` preserves the benchmark's scoring precision.

Questions do not specify a year. Each prompt provides a Swiss example followed by the target-country question and requests a numerical answer. Switzerland is reserved for examples. The country and income classifications follow WorldBench.

## Quick start

```python
import json
import pandas as pd

references = pd.read_csv("data/ground_truth.csv").set_index("question_id")
with open("data/questions.jsonl", encoding="utf-8") as f:
    questions = [json.loads(line) for line in f]

item = questions[0]
messages = item["messages"]       # Pass to a chat model.
answer = references.loc[item["question_id"], "ground_truth"]
```

All questions have a reference value. Join files using `question_id`. To follow WorldBench's error metric, calculate `abs(prediction - ground_truth) / max(prediction, ground_truth)`; the error is zero when both values are zero.
