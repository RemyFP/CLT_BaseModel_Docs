# Flu Model Code

> ***Written by LP, updated 11/12/2024 (work in progress)***

## Briefing

The module `flu_components.py` extends the base classes of `base_components.py` to implement a compartmental model with a specific structure. This model is given by the [mathematical formulation of the flu model](math_flu_components.md) with a few simplifying assumptions. The script `flu_demo.py` runs the flu model with input data given in `flu_demo_input_files`. Note that this input data gives "toy" values. For a realistic model, we would estimate or calibrate these values using real-world historical data. 

The simplifying assumptions of `flu_components.py` include:

- We model only one strain (and thus disregard prevalence) and we do not model vaccination -- thus, there is only one immunity inducing event (say, H1N1).
- Matrices that would otherwise have $3$ indices (e.g. $\boldsymbol{M}_{a, \ell, H1}^H(t)$ because they are $\lvert A \rvert \times \lvert L \rvert \times \lvert \mathcal I \rvert$) only have $2$ indices in the code (for age-risk group). In other words, these matrices are now only $\lvert A \rvert \times \lvert L \rvert$ and we drop the immunity-inducing event dimension entirely.

A more comprehensive model relaxing these simplifying assumptions is coming soon!

## Flu Demo Input Files

Here we provide a mapping between the input variable names in the `JSON` files of `flu_demo_input_files` (which become object attribute names in the model) and the mathematical variable.

### Fixed Parameters

The table below has a variable name to math variable mapping for `fixed_params.json`. We can think of this file as the "Greek letter file" -- it specifies values such as transition rates as well as number of age groups and risk groups. We assume that these values do not change throughout the course of the simulation.  

| Name                            | Math Variable              | Dimension                                |
|---------------------------------|----------------------------|------------------------------------------|
| `num_age_groups`                | $\lvert A \rvert$          | scalar                                   |
| `num_risk_groups`               | $\lvert L \rvert$          | scalar                                   |
| `beta_baseline`                 | $\beta_0$                  | scalar                                   |
| `total_population_val`          | $\boldsymbol{N}$           | $\boldsymbol{\tilde{\nu}}$ | $\lvert A \rvert                            |
| `humidity_impact`               | $\xi$                      | scalar                                   |
| `immunity_hosp_increase_factor` | $g^H$                      | scalar                                   |
| `immunity_inf_increase_factor`  | $g^I$                      | scalar                                   |
| `immunity_saturation_constant`  | $\boldsymbol{O}$           | $\lvert A \rvert \times \lvert L \rvert$ |
| `waning_factor_hosp`            | $w^H$                      | scalar                                   |
| `waning_factor_inf`             | $w^I$                      | scalar                                   |
| `hosp_risk_reduction`           | $\boldsymbol{K}^H$         | $\lvert A \rvert \times \lvert L \rvert$ |
| `inf_risk_reduction`            | $\boldsymbol{K}^I$         | $\lvert A \rvert \times \lvert L \rvert$ |
| `death_risk_reduction`          | $\boldsymbol{K}^D$         | $\lvert A \rvert \times \lvert L \rvert$ |
| `R_to_S_rate`                   | $\eta$                     | scalar                                   |
| `E_to_I_rate`                   | $\sigma$                   | scalar                                   |
| `I_to_R_rate`                   | $\gamma$                   | scalar                                   |
| `I_to_H_rate`                   | $\zeta$                    | scalar                                   |
| `H_to_R_rate`                   | $\gamma_H$                 | scalar                                   |
| `H_to_D_rate`                   | $\pi$                      | scalar                                   |
| `H_to_D_adjusted_proportion`    | $\boldsymbol{\tilde{\nu}}$ | $\lvert A \rvert \times \lvert L \rvert$ |
| `I_to_H_adjusted_proportion`    | $\boldsymbol{\tilde{\mu}}$ | $\lvert A \rvert \times \lvert L \rvert$ |

### Initial Values of State Variables

The table below has a variable name to math variable mapping for `state_variables_init_vals.json`. This file specifies initial conditions for `StateVariable` objects (which includes `EpiCompartment`, `EpiMetric`, and `Schedule` objects). 

| Name                       | Math Variable                        | Dimension                                |
|----------------------------|--------------------------------------|------------------------------------------|
| `S`                        | $\boldsymbol{S}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `E`                        | $\boldsymbol{E}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `I`                        | $\boldsymbol{I}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `H`                        | $\boldsymbol{H}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `R`                        | $\boldsymbol{R}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `D`                        | $\boldsymbol{D}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `population_immunity_inf`  | $\boldsymbol{M}^I(0)$              | $\lvert A \rvert \times \lvert L \rvert$ |
| `population_immunity_hosp` | $\boldsymbol{M}^H(0)$              | $\lvert A \rvert \times \lvert L \rvert$ |
| `absolute_humidity`        | `absolute_humidity` $= \xi / q(0)$ | scalar                                   |
| `flu_contact_matrix`       | $\boldsymbol{\phi}(0)$ 			  | $\lvert A \rvert \times \lvert L \rvert \times \lvert A \rvert \times \lvert L \rvert$ |