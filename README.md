
# I-GLIDE: Input Groups for Latent health Indicators in Degradation Estimation

## Description

I-GLIDE is a project aimed at reproducing the C-MAPSS experiment using our architecture and the RaPP metrics (reconstruction along projected pathways). The dataset used is C-MAPSS, which is a popular dataset for predictive maintenance and prognostics.

### Key Features

- **Health Indicators (HI) Extraction:** We extract health indicators from our Autoencoder (AE)-based model.
- **RUL Prediction:** The extracted HIs are fed into a Random Forest (RF) to predict the Remaining Useful Life (RUL).
- **Architecture Choices:** You can choose between different architectures, including AE, VAE, I-GLIDE AE, and I-GLIDE VAE.

### Fig. 1. I-GLIDE Architecture Framework
<img width="751" alt="ensemble_architecture4" src="https://github.com/user-attachments/assets/d80a0f72-5dfb-4523-9ded-f03c40e77e3c" />

- **A:** Subsystem-specific encoder-decoder heads learn distinct latent representations, which are fused into a shared latent space $z$ via reconstruction loss (trained on healthy data).
- **B:** Health Indicators (HIs) are extracted using RaPP metrics [González-Muñiz et al., 2022] and Uncertainty Quantification (UQ) [Kingma & Welling, 2013] over full trajectories.
- **C:** Aggregated HIs are used to predict Remaining Useful Life (RUL), trained via a Random Forest (RF) regressor $\( \mathcal{F} \)$.


### Dataset

The C-MAPSS dataset is used for this experiment. For each trajectory, the last point is represented with `rmse_last`.

### How to Run

1. Navigate to the `/cmapss` directory.
2. Choose the Jupyter notebook corresponding to the architecture you want to use:
   - AE
   - VAE
   - I-GLIDE AE
   - I-GLIDE VAE
3. Follow the instructions in the notebook to run the experiment.

### Results

Below is a comparison of different sets of HIs extracted from various architectures to predict the RUL on the C-MAPSS test dataset using a Random Forest for $\mathcal{F}$. The best models are highlighted. RMSE was used as a metric over the RUL estimation for the last trajectories point available in C-MAPSS test set.

### Table: Model Performance Across C-MAPSS Subsets, Average performance and standard deviation
   **Model, HI Set**       | **FD001**       | **FD002**       | **FD003**       | **FD004**       | **Avg.**        | **Improvement** |
 |-------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|-----------------|
 | AE, HI_González [1]    | 19.00 ±4.78     | 25.69 ±4.19     | 18.38 ±6.18     | 19.46 ±2.46     | 20.63 ±4.40     | --              |
 | AE, HI_mono             | 13.14 ±2.50     | 20.35 ±3.46     | 13.87 ±5.07     | 17.73 ±3.56     | 16.27 ±3.65     | +21.13% / +17.15% |
 | I-GLIDE_AE, HI_groups   | **12.11 ±2.72** | 22.01 ±2.88     | **10.23 ±1.85** | 14.92 ±1.31     | 14.82 ±2.19     | +8.94% / +39.96% |
 | VAE, HI_González [1]    | 34.13 ±3.71     | 31.05 ±1.89     | 27.25 ±2.58     | 25.23 ±2.03     | 29.42 ±2.55     | --              |
 | VAE, HI_mono            | 27.19 ±5.97     | 22.81 ±2.86     | 24.64 ±5.26     | 22.89 ±1.82     | 24.38 ±3.98     | +17.10% / -55.83% |
 | I-GLIDE_VAE, HI_groups  | 15.32 ±2.08     | **18.83 ±1.51** | 11.12 ±2.29     | **14.19 ±1.11** | 14.87 ±1.75     | +39.03% / +56.07% |

**Note:**
- Underline (represented here as bold): Outperforms methods without HIs.
- Improvement: Average improvement over the previous row.
- Bold: Best results per subset

#### Table: Comparison of HI Sets, best models taken

| **HI Extractor** | **HI Set for $\mathcal{F}(.)$** | **FD001** | **FD002** | **FD003** | **FD004** |
|------------------|---------------------------------|-----------|-----------|-----------|-----------|
| AE | $\text{HI}_\text{González}$ [1] | 11.43     | 22.91     | 12.03     | 16.78     |
|   AE               | $\text{HI}_\text{mono}$         | 10.53     | **15.71** | **8.07**  | 14.35     |
|VAE} | $\text{HI}_\text{González}$ [1] | 27.56     | 28.62     | 24.36     | 22.33     |
| VAE                  | $\text{HI}_\text{mono}$         | 18.77     | 19.44     | 15.59     | 19.81     |
| $\text{I-GLIDE}_\text{AE}$ | $\text{HI}_\text{groups}$ | **9.47**  | 16.18     | 8.29      | 12.32     |
| $\text{I-GLIDE}_\text{VAE}$ | $\text{HI}_\text{groups}$ | 12.33     | 16.76     | 8.5       | **11.4**  |

### Table: Comparison of I-GLIDE Method for HI Extraction

| **Model**                      | **FD001** | **FD002** | **FD003** | **FD004** |
|--------------------------------|-----------|-----------|-----------|-----------|
| MLP [2]                        | 37.56     | 80.03     | 37.39     | 77.37     |
| CNN [2]                        | 18.45     | 30.29     | 19.82     | 29.16     |
| CNN-LSTM [3]                   | 11.17     | -         | 9.99      | -         |
| MS-DCNN [4]                    | 11.44     | 19.35     | 11.67     | 22.22     |
| VAE + RNN [5]                  | 11.44     | 24.12     | 14.88     | 26.54     |
| MLE(4X)+CCF [6]                | 11.57     | 18.84     | 11.83     | 20.78     |
| RVE [5]                        | 13.42     | 14.92     | 12.51     | 16.37     |
| Probabilistic RUL CNN [7]      | 12.42     | **13.72** | 12.16     | 15.95     |
| I-GLIDE_AE + RF (ours, best)         | **9.47**  | 16.18     | **8.29**  | 12.32     |
| I-GLIDE_VAE + RF (ours, best)        | 12.33     | 16.76     | 8.5       | **11.4**  |

**Note:** The best results for each subset are highlighted in bold.

**References:**
- [1] González-Muñiz, A., Díaz, I., Cuadrado, A.A., García-Pérez, D.: Health indica-
tor for machine condition monitoring built in the latent space of a deep au-
toencoder. Reliability Engineering & System Safety 224, 108482 (Aug 2022).
https://doi.org/10.1016/j.ress.2022.108482
- [2] Peng,C.,Chen,Y.,Chen,Q.,Tang,Z.,Li,L.,Gui,W.:Aremainingusefullifeprog-
nosis of turbofan engine using temporal and spatial feature fusion. Sensors 21(2),
418 (Jan 2021). https://doi.org/10.3390/s21020418, https://www.mdpi.com/1424-
8220/21/2/418
- [3] Li, H., Zhao, W., Zhang, Y., Zio, E.: Remaining useful life prediction
using multi-scale deep convolutional neural network. Applied Soft Com-
puting 89, 106113 (Apr 2020). https://doi.org/10.1016/j.asoc.2020.106113,
https://linkinghub.elsevier.com/retrieve/pii/S1568494620300533
- [4] Costa, N., Sánchez, L.: Variational encoding approach for interpretable as-
sessment of remaining useful life estimation. Reliability Engineering & Sys-
tem Safety 222, 108353 (Jun 2022). https://doi.org/10.1016/j.ress.2022.108353,
https://linkinghub.elsevier.com/retrieve/pii/S0951832022000321
- [5] Pillai, S., Vadakkepat, P.: Two stage deep learning for prognostics using multi-
loss encoder and convolutional composite features. Expert Systems with Ap-
plications 171, 114569 (Jun 2021). https://doi.org/10.1016/j.eswa.2021.114569,
https://linkinghub.elsevier.com/retrieve/pii/S0957417421000105
- [6] DePater,I.,Mitici,M.:Novelmetricstoevaluateprobabilisticremainingusefullife
prognostics with applications to turbofan engines. PHM Society European Con-
ference 7(1), 96–109 (Jun 2022). https://doi.org/10.36001/phme.2022.v7i1.3320,
https://papers.phmsociety.org/index.php/phme/article/view/3320
- [7] Zheng, S., Ristovski, K., Farahat, A., Gupta, C.: Long short-term memory net-
work for remaining useful life estimation. In: 2017 IEEE International Con-
ference on Prognostics and Health Management (ICPHM). p. 88–95. IEEE,
Dallas, TX, USA (Jun 2017). https://doi.org/10.1109/ICPHM.2017.7998311,
http://ieeexplore.ieee.org/document/7998311/
