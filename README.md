# A physiology-driven decision support system for heat-strain intervention planning in construction

This repository provides the research implementation and reproducibility materials for the manuscript of the same title. The proposed decision support system links worker-state information, candidate management interventions, and predicted physiological responses, providing a computational basis for managers to compare and select heat-safety intervention plans before implementation.

The **Physiology-Driven Control-Aware Network (PDCAN)** is the system’s core predictive component. Given static worker characteristics, historical physiological and environmental states, and predefined future environmental and workload controls, it predicts minute-by-minute trajectories of heart rate (HR), skin temperature (Tskin), and core body temperature (Tcore) over the next 15 minutes and estimates mPSI at the end of the forecast horizon. The repository includes processed data, source code, trained model artifacts, result tables, and figures supporting predictive evaluation and intervention-scenario comparison.

## Decision support workflow

| Stage | Role in the system |
|---|---|
| Worker-state representation | Combine static worker characteristics with a 30-minute history of physiological, environmental, and workload observations. |
| Candidate intervention inputs | Represent continued work, rest, rest combined with environmental improvement, and workload reduction as alternative 15-minute future control sequences. |
| Physiological prediction and heat-strain estimation | Use PDCAN to generate HR, Tskin, and Tcore trajectories and estimate final mPSI through a physiological formula with residual calibration. |
| Intervention-plan comparison | Hold individual characteristics and observed history constant, compare the predicted responses across candidate sequences, and provide information for managerial decisions about feasible site interventions. |

The system extends risk prediction and warning by linking specified management actions to projected physiological outcomes. The supplied scripts implement the computational workflow and scenario analyses; they do not automatically optimise or execute intervention plans. Evaluation uses controlled heat-exposure data, while the scenario results demonstrate how the system can support short-term intervention-plan comparison for construction heat-safety management.

## What is included

```text
data/
  data.csv                         processed training matrix
  test.csv                         fixed 336-window test matrix; final column is `type`
src/
  pdcan_scenario_core.py           PDCAN architecture, scaling, loss, and inference
  prepare_selected_control_sequences.py
                                   selection and donor matching for Section 5.6
  run_selected_control_scenarios.py
                                   fixed-epoch fitting, scenario inference, and validation
  make_selected_scenario_figures.py
                                   publication-format exports for Figs. 10 and 11
notebooks/
  model_comparison.ipynb           PDCAN, LSTM, GRU, and Transformer comparison
  pdcan_ablation.ipynb             PDCAN component-ablation analysis
models/pdcan_scenario/
  final_model_state_dict.pt        checkpoint paired with the scenario results
  scalers_train_full.json          training-only scaling parameters
  hyperparameters.json             PDCAN hyperparameters
  fixed_epochs.json                fixed training epoch count
  training_provenance.json         seed, data hash, software, and device provenance
results/
  model_comparison/                comparison tables
  ablation/                        component-ablation tables
  scenario/                        scenario inputs, predictions, and diagnostics
  figures/                         model-comparison and factual-prediction figures
figures/manuscript/                figures appearing in the manuscript, Figs. 1–11
FIGURES.md                          figure-by-figure guide
```

The old exploratory `alternative_control_scenarios_outputs` directory is not included. It used an earlier all-window protocol and is separate from the selected transition-window experiment reported in Section 5.6.

## Main figures

The figure files are collected in [`FIGURES.md`](FIGURES.md). A few representative figures are shown here to make the analysis easier to follow.

### Forecasting setup

<img src="figures/manuscript/Fig01_prediction_timeline.png" alt="Forecasting timeline" width="720">

At the decision time, the system receives a 30-minute historical window and a predefined control sequence covering the following 15 minutes. The sequence specifies environmental exposure and workload throughout the forecast horizon, including the timing of planned changes.

### Core predictive component: PDCAN

<img src="figures/manuscript/Fig02_pdcan_architecture.png" alt="PDCAN architecture" width="900">

PDCAN separately encodes static worker characteristics, historical physiological and environmental states, and future control conditions before fusing their representations. Its control-aware recurrent decoder generates the three physiological trajectories without feeding previous predicted physiological outputs into subsequent steps. Trajectory smoothness regularization and joint training support multivariable prediction, while a physiological formula and residual calibration module produce the final mPSI estimate.

### Forecasting performance

<img src="figures/manuscript/Fig05_hr_mae_by_horizon.png" alt="Heart-rate MAE by forecasting horizon" width="760">

The step-wise evaluation compares PDCAN with the three baseline sequence models over the 15-minute horizon.

<img src="figures/manuscript/Fig08_mpsi_predicted_vs_observed.png" alt="Predicted versus observed mPSI" width="760">

The mPSI comparison evaluates the model’s final heat-strain estimate against the observed test-set value. On the fixed 336-window test set from controlled heat-exposure experiments, the reported results are:

| Measure | PDCAN result |
|---|---:|
| Final mPSI MAE | 0.602 |
| Final mPSI RMSE | 0.821 |
| Final mPSI R² | 0.920 |
| Tcore trajectory MAE | 0.068 °C |

PDCAN outperformed the GRU, LSTM, and Transformer baselines on the evaluated physiological trajectory prediction tasks. Detailed results are provided in [`results/model_comparison/final_test_summary_all_models.csv`](results/model_comparison/final_test_summary_all_models.csv); component-ablation results are available under [`results/ablation/`](results/ablation/).

### Intervention-scenario comparison

<img src="figures/manuscript/Fig10_scenario_trajectories.png" alt="Scenario physiological trajectories" width="900">

For six representative workload-transition blocks, the system holds individual characteristics and observed history constant and evaluates four future control sequences. Comparing the resulting cardiovascular and thermal trajectories provides complementary information about the predicted response to each candidate intervention. The alternative sequences are model projections; the observed workload-reduction sequence is the factual reference.

<img src="figures/manuscript/Fig11_scenario_final_mpsi.png" alt="Scenario final mPSI" width="760">

This paired comparison shows how the projected final mPSI changes across the four control sequences. Managers can compare these estimates alongside the physiological trajectories when considering feasible site interventions. The results support descriptive scenario comparison and do not establish causal intervention effects.

## Reproduce the Section 5.6 scenario experiment

The scripts can be run from the repository root with Python 3.10 or newer:

```powershell
python -m pip install -r requirements.txt
python src/run_selected_control_scenarios.py --stage prepare
python src/run_selected_control_scenarios.py --stage train
python src/run_selected_control_scenarios.py --stage scenarios
python src/make_selected_scenario_figures.py
```

`--stage all` combines preparation, fixed-epoch fitting, and scenario inference. The screening protocol identifies 30 Type 3 workload-transition windows in the fixed test set. Six representative windows (sample IDs 255, 271, 287, 303, 319, and 335; zero-based) form the primary descriptive cohort, while 24 overlapping windows are retained for supplementary sensitivity analysis.

The four future control sequences are continued work, rest, rest plus environmental improvement, and the observed workload-reduction sequence. Only the last sequence has paired physiological observations. The other sequences are model-generated projections and should not be interpreted as causal intervention effects.

## Reproduce the comparison and ablation analyses

The two notebooks preserve the fixed-epoch comparison and PDCAN component-ablation workflows used to generate the manuscript tables. The corresponding CSV and Excel result files are already provided under `results/model_comparison/` and `results/ablation/`. The notebooks use the processed matrices in `data/`.

## Data provenance and availability

The processed matrices were reconstructed from the public FACT dataset (PROSPIE). The original dataset record is:

> Havenith, G., Davey, S., Downie, V., Griggs, K., and Richmond, V. (2024). *Dataset for “Prediction of Core Body Temperature from Multiple Variables” and “The physiological strain index does not reliably identify individuals at risk of reaching a thermal tolerance limit” PROSPIE project*. Loughborough University Research Repository. https://doi.org/10.17028/rd.lboro.26076577.v1

Consult and cite the original dataset record for the data-collection protocol, source-data provenance, and reuse conditions. The processed matrices and their schema are documented in [`data/README.md`](data/README.md).

## Data availability

> The processed data and code supporting this study are publicly available at: https://github.com/Tianchenxcl/pdcan-heat-strain-intervention-simulation.
