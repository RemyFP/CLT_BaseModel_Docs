# Flu Model Input Requirements

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

> **_Written by LP, edited by Susan Ptak, updated 04/08/2025 (work in progress)_** 

> **_Important note: we are updating this page with wastewater inputs -- check back soon._**

Here, we document input requirements for creating a `FluSubpopModel` instance and a `FluMetapopModel` instance, as well as provide mappings between the input variable names in the [code](flu_components_reference.md) and the mathematical variables in the [mathematical formulation](math_flu_components.md). For users to customize the *values* (not the structure) of the flu model given in `flu_components.py`, they must abide by certain input specifications.

Recall that the class `FluSubpopModel` simulates the flu model given by [this mathematical formulation](math_flu_components.md) for a specific subpopulation. Multiple `FluSubpopModel` instances can be combined as input into a `FluMetapopModel` instance to simulate multiple subpopulations and travel between these subpopulations. 

Each `FluSubpopModel` instance requires user-specified inputs for demographics and initial values of compartments and epi metrics, values of fixed parameters, experiment configuration details, and a school-work calendar. Each `FluMetapopModel` instance requires data for pairwise travel proportions (the proportion of people in subpopulation $\ell$ who travel to a different subpopulation $k$.) Depending on the type of input, these inputs may be read from `JSON` or `CSV` files, or defined directly in code as a `dict` or `pd.DataFrame`.

As a concrete example, we refer the user to `flu_demo.py` and its input folder `flu_demo_input_files`. The demo model has two subpopulations with the exact same inputs. The folder contains 4 `JSON` input files used to run `flu_demo.py`: `compartments_epi_metrics_init_vals.json`, `fixed_params.json`, `config.json`, and `travel_proportions.json`. These input files respectively are used to initialize a `FluSubpopState` instance, `FluSubpopParams` instance, `Config` instance, and `FluInterSubpopRepo` instance. The folder also contains a file `school_work_calendar.csv` that provides necessary calendar data (indicating if a date is a school or work day). In general, for an arbitrary number of subpopulations with different inputs, the user should have 5 input files per `FluSubpopModel` instance (4 are the aforementioned `JSON` files and 1 is the aforementioned `CSV` file).

The file `travel_proportions.JSON` provides pairwise travel proportions between subpopulations. This is used for travel computations in the `FluMetapopModel` instance that contains the subpopulations. This file is *not required* if only one `FluSubpopModel` is used (so there is no metapopulation model or travel model).

In the following sections we discuss the input requirements for: for demographics and initial values of compartments and epi metrics (for each `FluSubpopModel`), values of fixed parameters (for each `FluSubpopModel`), experiment configuration details (for each `FluSubpopModel`), school-work calendars (for each `FluSubpopModel`), and travel proportions (for each `FluMetapopModel`).

## Demographics and initial values of state variables

The table below has a variable name to math variable mapping for the `JSON` file used to initialize `FluSubpopState` instances. This file specifies initial conditions for `StateVariable` objects (which includes `Compartment`, `EpiMetric`, `Schedule`, and `DynamicVal` objects). 

Important notes:

- `Schedule` instances get their values deterministically updated according to a schedule. At every simulation day `t`, their values are well-defined (taken from a schedule calendar), regardless of the initial value. Therefore, the initial value does not matter, so they are excluded from inputs. However, these objects are indeed updated and used in the simulation. Similar logic applies to `DynamicVal` instances.

- Currently, `beta_reduct` is not a part of the [mathematical formulation](math_flu_components.md). In the code, `beta_reduct` is a `DynamicVal` that decreases `beta_baseline` when a certain simulation state is triggered. This emulates a very simple staged-alert policy. In `flu_components.py`, `beta_reduct` is automatically disabled, so that the simple staged-alert policy is not in effect during default flu model runs. When enabled, `beta_reduct` has a value of `0.5` (corresponding to a $50\%$ decrease in transmission rates) when more than $5\%$ of the population is infected, and it has a value of `0.0` otherwise. Again, these values are "toy" values for the sake of demonstration.

Note that there is one `FluSubpopState` for each `SubpopModel` instance. The following matrices are for a specific subpopulation. But here we remove the superscript $(\ell)$ for subpopulation $\ell$ for readability ease. 

| Name                       | Math Variable                        | Dimension                                |
|----------------------------|--------------------------------------|------------------------------------------|
| `S`                        | $\boldsymbol{S}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `E`                        | $\boldsymbol{E}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `IP`                        | $\boldsymbol{IP}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `IS`                        | $\boldsymbol{IS}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `IA`                        | $\boldsymbol{IA}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `H`                        | $\boldsymbol{H}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `R`                        | $\boldsymbol{R}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `D`                        | $\boldsymbol{D}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `population_immunity_inf`  | $\boldsymbol{M}^I(0)$              | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `population_immunity_hosp` | $\boldsymbol{M}^H(0)$              | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |


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

Notes

- There is one `FluSubpopParams` instance for each `SubpopModel` instance. 
- The following variables are for a specific subpopulation, although many of these variables will be the same across subpopulations in practice. The [mathematical formulation](math_flu_components.md) for the flu model assumes that only $\boldsymbol{N}^{(\ell)}$ and $\beta_0^{(\ell)}$ are different across subpopulations, although it is straightforward to generalize the notation and relax this assumption. Here in the table below we remove the superscript $(\ell)$ for subpopulation $\ell$ for readability ease. 
- Variables that are positive scalars can be generalized to be age or risk dependent.

| Name                            | Math Variable              | Dimension                                |
|---------------------------------|----------------------------|------------------------------------------|
| `num_age_groups`                | $\lvert \mathcal A \rvert$          | positive `int`                           |
| `num_risk_groups`               | $\lvert \mathcal R \rvert$          | positive `int`							  |
| `beta_baseline`                 | $\beta_0$                  | positive scalar                          |
| `total_pop_age_risk`          | $\boldsymbol{N}$           | $\lvert \mathcal A \rvert$                           |
| `humidity_impact`               | $\xi$                      | scalar                                   |
| `hosp_immune_gain` | $g^H$                      | scalar                                   |
| `inf_immune_gain`  | $g^I$                      | scalar                                   |
| `immune_saturation`  | $\boldsymbol{O}$           | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `hosp_immune_wane`            | $w^H$                      | positive scalar 						  |
| `inf_immune_wane`             | $w^I$                      | positive scalar                          |
| `hosp_risk_reduce`           | $\boldsymbol{K}^H$         | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `inf_risk_reduce`            | $\boldsymbol{K}^I$         | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `death_risk_reduce`          | $\boldsymbol{K}^D$         | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `R_to_S_rate`                   | $\rateRtoS$                     | positive scalar                          |
| `E_to_I_rate`                   | $\rateEtoI$                   | positive scalar                          |
| `IP_to_IS_rate`				  | $\rateIPtoIS$					   | positive scalar						  |
| `IS_to_R_rate`                  | $\rateIStoR$                   | positive scalar                          |
| `IA_to_R_rate`				  | $\rateIAtoR$			   | positive scalar						  |
| `IS_to_H_rate`                  | $\rateIStoH$                    | positive scalar                          |
| `H_to_R_rate`                   | $\rateHtoR$                 | positive scalar                          |
| `H_to_D_rate`                   | $\rateHtoD$                      | positive scalar                          |
| `E_to_IA_prop`                  | $\propIA$                     | scalar in $[0,1]$                        |
| `H_to_D_adjusted_prop`    	  | $\boldsymbol{\adjustedpropD}$ | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `IS_to_H_adjusted_prop`   	  | $\boldsymbol{\adjustedpropH}$ | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `IP_relative_inf`   			  | $r_{IP}$ 				   | positive scalar 						  |
| `IA_relative_inf`			   	  | $r_{IA}$ 				   | positive scalar                          |
| `relative_suscept_by_age`			   	  | $\psi$ 				   | $\lvert \mathcal A \rvert \times 1$                          |
| `prop_time_away_by_age`			   	  | $c$ 				   | $\lvert \mathcal A \rvert \times 1$                          |
| `contact_mult_travel`			   	  | $h_{\text{travel}}$ 				   | scalar                         |
| `contact_mult_symp`			   	  | $h_{\text{symp}}$ 				   | scalar                         |
| `total_contact_matrix`			   	  | see contact matrix below 				   | $\lvert \mathcal A \rvert \times \lvert A \rvert$                         |
| `school_contact_matrix`			   	  | see contact matrix below 				   | $\lvert \mathcal A \rvert \times \lvert A \rvert$                         |
| `work_contact_matrix`			   	  | see contact matrix below				   | $\lvert \mathcal A \rvert \times \lvert A \rvert$                         |

## Contact matrix and school-work calendar CSV

Let $\phi^{(\ell), \text{total}}$, $\phi^{(\ell), \text{work}}$, and $\phi^{(\ell), \text{school}}$ represent the `total_contact_matrix`, `school_contact_matrix`, and `work_contact_matrix` respectively for subpopulation $\ell$.

The school-work calendar CSV must have 3 columns with names: `"date"`, `"is_school_day"`, and `"is_work_day"`. The `"date"` column contains strings corresponding to dates, in the format `"YYYY-MM-DD"`. The other two columns are Booleans indicating if the real-world date is a school day or work day. 

Then the contact matrix for the subpopulation at time $t$ is
$$
\phi^{(\ell)}(t) := \phi^{(\ell), \text{total}} - (1 - d_{\text{work}}(t)) \phi^{(\ell), \text{work}} - (1 - d_{\text{school}}(t)) \phi^{(\ell), \text{school}}
$$
where $d_{\text{work}}(t)$ is $1$ if the real-world date corresponding to simulation time $t$ is a work day and $0$ otherwise, specified by the school-work calendar input CSV, and $d_{\text{school}}(t)$ is defined analogously, but for school days.


## Travel model's travel proportions JSON 

The travel proportions `JSON` must have the following fields

 Name                            | Math Variable              | Dimension                                |
|---------------------------------|----------------------------|------------------------------------------|
| `subpop_names_mapping`                |    N/A       | `dict`                            |
| `travel_proportions_array`               |    $v^{\ell \rightarrow k}$       | $\lvert \mathcal L \rvert \times \lvert \mathcal L \rvert$                              |

The field `subpop_names_mapping` must be a dictionary where

- Keys are strings that match the names of each associated `FluSubpopModel` instance.
- Values are integers in $\{0, 1, \dots, \lvert \mathcal L \rvert\}$.

This dictionary provides a mapping between the `FluSubpopModel` instance (its name) and the index it occupies in the `travel_proportions_array`.

The field `travel_proportions_array` must be a list of $\lvert \mathcal L \rvert$ lists, each of length $\lvert \mathcal L \rvert$. Recall that `JSON` does not support `numpy` arrays natively, but the base model code is able to convert lists to `numpy` arrays for use. The $(\ell, k)$th element of `travel_proportions_array` corresponds to the proportion of people in subpopulation $\ell$ who travel to subpopulation $k$. Again, the mapping between indices and subpopulations is given in `subpop_names_mapping`.