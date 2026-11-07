# Figures from the manuscript

These figures document the computational workflow of the physiology-driven decision support system for heat-strain intervention planning in construction. They cover the forecasting setup, PDCAN as the core predictive component, predictive evaluation, component analysis, and comparison of candidate intervention scenarios.

## Fig. 1. Schematic diagram of the model prediction timeline

<img src="figures/manuscript/Fig01_prediction_timeline.png" alt="Model prediction timeline" width="800">

The blue segment is the 30-minute historical input window. At the decision time, the system receives a predefined environmental and workload control sequence covering the following 15 minutes and projects the corresponding physiological states. Planned control changes can occur within this forecast horizon.

## Fig. 2. Structure of PDCAN

<img src="figures/manuscript/Fig02_pdcan_architecture.png" alt="PDCAN architecture" width="1000">

PDCAN is the system’s core predictive component. It separately encodes static worker features, historical physiological and environmental states, and future control conditions before fusing their representations. It predicts HR, Tskin, and Tcore trajectories and estimates final mPSI, supplying the physiological information used for intervention-plan comparison.

## Fig. 3. PDCAN encoder

<img src="figures/manuscript/Fig03_encoder_detail.png" alt="PDCAN encoder" width="1000">

The encoder forms a static embedding, extracts local temporal patterns with a TCN and GRU, and represents the future control sequence before fusing the three information streams.

## Fig. 4. Control-aware recurrent physiological decoder

<img src="figures/manuscript/Fig04_decoder_detail.png" alt="Control-aware recurrent decoder" width="1000">

The decoder propagates temporal information through recurrent hidden states and incorporates the corresponding control conditions at each step of the 15-minute horizon. It generates HR, Tskin, and Tcore trajectories without feeding previous predicted physiological outputs into subsequent steps. The predicted physiological states are then used to estimate final mPSI through a physiological formula with residual calibration.

## Fig. 5. Heart-rate MAE across prediction steps

<img src="figures/manuscript/Fig05_hr_mae_by_horizon.png" alt="Heart-rate MAE" width="850">

This figure compares step-wise heart-rate errors for PDCAN, LSTM, Transformer, and GRU.

## Fig. 6. Skin-temperature MAE across prediction steps

<img src="figures/manuscript/Fig06_tskin_mae_by_horizon.png" alt="Skin-temperature MAE" width="850">

This figure compares the skin-temperature forecasting errors across the 15-minute horizon.

## Fig. 7. Core-temperature MAE across prediction steps

<img src="figures/manuscript/Fig07_tcore_mae_by_horizon.png" alt="Core-temperature MAE" width="850">

This figure compares the core-temperature forecasting errors across the 15-minute horizon.

## Fig. 8. Predicted versus observed mPSI

<img src="figures/manuscript/Fig08_mpsi_predicted_vs_observed.png" alt="Predicted versus observed mPSI" width="850">

The scatter plot shows the agreement between predicted and observed final mPSI values in the fixed test set.

## Fig. 9. Distribution of mPSI residuals

<img src="figures/manuscript/Fig09_mpsi_residuals.png" alt="mPSI residual distribution" width="850">

The residual distribution provides a complementary view of the final mPSI prediction error.

## Fig. 10. Individual and median physiological trajectories under control-sequence scenarios

<img src="figures/manuscript/Fig10_scenario_trajectories.png" alt="Scenario physiological trajectories" width="1000">

The figure compares predicted HR, Tskin, and Tcore under continued work, rest, rest plus environmental improvement, and observed workload reduction for the six representative transition blocks. Individual characteristics and observed history are held constant across candidate sequences, allowing the system to compare their predicted cardiovascular and thermal responses.

## Fig. 11. Predicted final mPSI across control-sequence scenarios

<img src="figures/manuscript/Fig11_scenario_final_mpsi.png" alt="Scenario final mPSI" width="850">

The paired lines show the projected change in final mPSI for each representative transition block. Together with the physiological trajectories in Fig. 10, these estimates provide information for comparing candidate intervention plans before implementation. The alternative scenarios are model projections; the observed workload-reduction sequence is the factual reference.
