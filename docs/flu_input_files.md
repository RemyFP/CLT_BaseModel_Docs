# Flu Model Input Files

<span style="display: none;">
$\def\rateRtoS{\sigma^{R\rightarrow S}}$
$\def\rateEtoI{\sigma^{E \rightarrow [IP, IA]}}$
$\def\rateIPtoIS{\sigma^{IP \rightarrow IS}}$
$\def\rateIStoH{\sigma^{IS\rightarrow H}}$
$\def\rateHtoD{\sigma^{H\rightarrow D}}$
$\def\rateIAtoR{\gamma^{IA\rightarrow R}}$
$\def\rateHtoR{\gamma^{H\rightarrow R}}$
$\def\rateIStoR{\gamma^{IS\rightarrow R}}$
$\def\totalforceofinfection{\lambda^{(\ell), \text{total}}_{a,r}(t)}$
$\def\propIA{\pi^{IA}}$
$\def\propH{\pi^H}$
$\def\propD{\pi^D}$
$\def\adjustedpropH{\tilde{\pi}^H}$
$\def\adjustedpropD{\tilde{\pi}^D}$
</span>

> **_Written by LP, updated 01/23/2025 (work in progress)_** 

> **_Important note: we are updating this page with travel model and wastewater inputs -- check back soon._**

Here we provide a mapping between the input variable names in the flu model's `JSON` files (and corresponding `dataclasses`), which become object attribute names in the model) and the mathematical variable. 

The folder `flu_demo_input_files` contains 3 `JSON` input files used to run `flu_demo.py`: `config.json`, `fixed_params.json`, and `compartments_epi_metrics_init_vals.json`. These input files respectively initialize `Config` instances, `FluSubpopParams` instances, and `FluSubpopState` instances.

In general, for users to customize the *values* (not the structure) of the flu model given in `flu_components.py`, they must provide analogous 3 `JSON` input files with the following formats.

## Simulation configuration

The table below describes the `JSON` file used to initialize `Config` instances. These values specify the simulation experiment configuration. 

| Name                                  | Explanation                | Format                                   |
|---------------------------------------|----------------------------|------------------------------------------|
| `timesteps_per_day`                   | number of discretized timesteps per day -- larger values may increase accuracy of approximation to continuous time but slow down simulation           							 | positive `int`                           |
| `transition_type`           			| specifies distribution of stochastic or deterministic population transitions between compartments -- see [mathematical definition of transition types](math_flu_components.md) for specific formulas         | `str`, must have value in `TransitionTypes` to be valid                                    |
| `start_real_date`                 	| real-world date corresponding to start of simulation                  | `str`, must have format `"YYYY-MM-DD"` |
| `save_daily_history`          		| indicates if daily history is saved on `Compartment` instances -- may want to strategically turn off to save time during performance runs          | `bool` |


## Fixed parameters

The table below has a variable name to math variable mapping for the `JSON` file used to initialize `FluSubpopParams` instances. We can think of this file as the "Greek letter file" -- it specifies values such as transition rates as well as number of age groups and risk groups. We assume that these values do not change throughout the course of the simulation.  

| Name                            | Math Variable              | Dimension                                |
|---------------------------------|----------------------------|------------------------------------------|
| `num_age_groups`                | $\lvert A \rvert$          | positive `int`                           |
| `num_risk_groups`               | $\lvert L \rvert$          | positive `int`							  |
| `beta_baseline`                 | $\beta_0$                  | positive scalar                          |
| `total_pop_age_risk`          | $\boldsymbol{N}$           | $\boldsymbol{\tilde{\nu}}$ | $\lvert A \rvert                            |
| `humidity_impact`               | $\xi$                      | scalar                                   |
| `hosp_immune_gain` | $g^H$                      | scalar                                   |
| `inf_immune_gain`  | $g^I$                      | scalar                                   |
| `immune_saturation`  | $\boldsymbol{O}$           | $\lvert A \rvert \times \lvert L \rvert$ |
| `hosp_immune_wane`            | $w^H$                      | positive scalar 						  |
| `inf_immune_wane`             | $w^I$                      | positive scalar                          |
| `hosp_risk_reduce`           | $\boldsymbol{K}^H$         | $\lvert A \rvert \times \lvert L \rvert$ |
| `inf_risk_reduce`            | $\boldsymbol{K}^I$         | $\lvert A \rvert \times \lvert L \rvert$ |
| `death_risk_reduce`          | $\boldsymbol{K}^D$         | $\lvert A \rvert \times \lvert L \rvert$ |
| `R_to_S_rate`                   | $\rateRtoS$                     | positive scalar                          |
| `E_to_I_rate`                   | $\rateEtoI$                   | positive scalar                          |
| `IP_to_IS_rate`				  | $\rateIPtoIS$					   | positive scalar						  |
| `IS_to_R_rate`                  | $\rateIStoR$                   | positive scalar                          |
| `IA_to_R_rate`				  | $\rateIAtoR$			   | positive scalar						  |
| `IS_to_H_rate`                  | $\rateIStoH$                    | positive scalar                          |
| `H_to_R_rate`                   | $\rateHtoR$                 | positive scalar                          |
| `H_to_D_rate`                   | $\rateHtoD$                      | positive scalar                          |
| `E_to_IA_prop`                  | $\propIA$                     | scalar in $[0,1]$                        |
| `H_to_D_adjusted_prop`    	  | $\boldsymbol{\adjustedpropD}$ | $\lvert A \rvert \times \lvert L \rvert$ |
| `IS_to_H_adjusted_prop`   	  | $\boldsymbol{\adjustedpropH}$ | $\lvert A \rvert \times \lvert L \rvert$ |
| `IP_relative_inf`   			  | $r_{IP}$ 				   | positive scalar 						  |
| `IA_relative_inf`			   	  | $r_{IA}$ 				   | positive scalar                          |

## Initial values of state variables

The table below has a variable name to math variable mapping for the `JSON` file used to initialize `FluSubpopState` instances. This file specifies initial conditions for `StateVariable` objects (which includes `Compartment`, `EpiMetric`, `Schedule`, and `DynamicVal` objects). 

Important notes:

- `Schedule` instances get their values deterministically updated according to a schedule. At every simulation day `t`, their values are well-defined (taken from a schedule calendar), regardless of the initial value. Therefore, the initial value does not matter, so they are excluded from inputs. However, these objects are indeed updated and used in the simulation. Similar logic applies to `DynamicVal` instances.

- Currently, `beta_reduct` is not a part of the [mathematical formulation](math_flu_components.md). In the code, `beta_reduct` is a `DynamicVal` that decreases `beta_baseline` when a certain simulation state is triggered. This emulates a very simple staged-alert policy. In `flu_components.py`, `beta_reduct` is automatically disabled, so that the simple staged-alert policy is not in effect during default flu model runs. When enabled, `beta_reduct` has a value of `0.5` (corresponding to a $50\%$ decrease in transmission rates) when more than $5\%$ of the population is infected, and it has a value of `0.0` otherwise. Again, these values are "toy" values for the sake of demonstration.

| Name                       | Math Variable                        | Dimension                                |
|----------------------------|--------------------------------------|------------------------------------------|
| `S`                        | $\boldsymbol{S}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `E`                        | $\boldsymbol{E}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `IP`                        | $\boldsymbol{I^P}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `IS`                        | $\boldsymbol{I^S}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `IA`                        | $\boldsymbol{I^A}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `H`                        | $\boldsymbol{H}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `R`                        | $\boldsymbol{R}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `D`                        | $\boldsymbol{D}(0)$                | $\lvert A \rvert \times \lvert L \rvert$ |
| `population_immunity_inf`  | $\boldsymbol{M}^I(0)$              | $\lvert A \rvert \times \lvert L \rvert$ |
| `population_immunity_hosp` | $\boldsymbol{M}^H(0)$              | $\lvert A \rvert \times \lvert L \rvert$ |
