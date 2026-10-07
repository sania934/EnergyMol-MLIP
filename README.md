# EnergyMol-MLIP

**Transferable Equivariant Machine-Learning Interatomic Potentials for Small-Molecule Energy Materials**

EnergyMol-MLIP is a computational workflow for developing machine-learning interatomic potentials (MLIPs) for small organic energy-material molecules. The project combines density functional theory (DFT), chemically informed configuration generation, equivariant machine learning, learning-curve analysis, and molecule-level transfer testing.

The current dataset contains **563 DFT-labeled configurations for 24 molecules spanning six structural families**. DFT energies and forces were calculated using **PBE0-D3(BJ)/def2-SVP in PySCF**. An equivariant **MACE** model was then trained and evaluated using molecule-independent splits.


## Dataset Generation

Initial Cartesian perturbations were tested during configuration generation. These produced some unrealistic structures with excessive bond stretching and large energy changes.

The final dataset therefore uses **MMFF normal-mode-based perturbations** to generate chemically more reasonable configurations. The resulting structures were subjected to geometry and energy-quality checks before DFT labeling.

The final dataset contains 24 molecules distributed across six structural families, providing variation in molecular size, functional groups, and chemical composition.

## DFT Calculations

DFT calculations were performed using **PySCF** with:

* Functional: **PBE0**
* Dispersion: **D3(BJ)**
* Basis set: **def2-SVP**
* Spin state: **neutral singlet**
* Calculated properties: **total energy and atomic forces**

These calculations provide the reference labels used for MACE training and evaluation.

## MACE Model

An equivariant MACE model was trained using the DFT energies and forces.

The dataset was divided at the **molecule level** rather than by randomly distributing configurations. This prevents configurations from the same molecule from appearing in both training and transfer sets.

The final split was:

| Dataset    | Configurations |
| ---------- | -------------: |
| Training   |            372 |
| Validation |             96 |
| Transfer   |             95 |
| **Total**  |        **563** |

## Learning-Curve Results

The effect of training-set size was evaluated using training subsets of 93, 186, 279, and 372 configurations.

Validation force performance improved as the training set increased:

| Training configurations | Force RMSE (eV/Å) | Force R² |
| ----------------------: | ----------------: | -------: |
|                      93 |            0.8296 |    0.774 |
|                     186 |            0.7458 |    0.817 |
|                     279 |            0.7193 |    0.830 |
|                     372 |            0.6110 |    0.877 |

Thus, increasing the training set improved force learning. Energy RMSE did not improve monotonically with training-set size.

## Transferability

Transfer testing was performed on four molecules that were not included in the training set.

The aggregate transfer force RMSE at the full training size was **0.773 eV/Å**, with a force R² of **0.738**.

Transfer performance varied substantially between molecules. Some unseen molecules were predicted with relatively low force errors, while others showed considerably larger errors. This indicates that increasing the number of configurations from existing molecules does not by itself guarantee uniform chemical transferability.

## Interpretation

This project establishes a small-molecule DFT dataset and a baseline MLIP workflow for atomistic active learning.

The results show that increasing configuration count can improve force learning, but chemical transferability remains molecule-dependent. Future improvement should therefore consider **model uncertainty, molecular diversity, and functional-group coverage** when selecting new DFT configurations.

The current model should be regarded as a **baseline research model**, not as a production-quality potential for broad chemical-space simulations.


## Reproducibility

The notebooks document the main computational workflow from molecular structure generation through DFT labeling, MACE training, and transfer evaluation.

## Future Work

The next stage of the project is an **active-learning workflow** for selecting additional DFT configurations.

Potential selection criteria include:

* model uncertainty,
* molecular diversity,
* functional-group diversity,
* structural novelty,
* and regions of configuration space where the current MLIP performs poorly.

The aim is to improve chemical transferability without relying only on large numbers of randomly generated configurations.
