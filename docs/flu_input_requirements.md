# Math-to-Code Input Mappings

<span style="display: none;">
$\def\rateRtoS{\sigma^{R\rightarrow S}}$
$\def\rateEtoI{\sigma^{E \rightarrow [IP, IA]}}$
<!-- $\def\rateIPtoIS{\sigma^{IP \rightarrow IS}}$ -->
$\def\rateIPtoIS{\sigma^{IP \rightarrow [ISR, ISH]}}$
$\def\rateIStoH{\sigma^{IS\rightarrow H}}$
$\def\rateISRtoR{\gamma^{ISR\rightarrow R}}$
$\def\rateISHtoH{\sigma^{ISH\rightarrow [HR, HD]}}$
$\def\rateHtoD{\sigma^{H\rightarrow D}}$
$\def\rateHRtoR{\gamma^{HR\rightarrow R}}$
$\def\rateHDtoD{\sigma^{HD\rightarrow D}}$
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

Here, we provide mappings between the mathematical variables in the [mathematical formulation](math_flu_components.md) and input variable names in the [code](flu_components_reference.md). For users to customize the *values* (not the structure) of the flu model given in `flu_components.py`, they must abide by certain input specifications.

Recall that the class `FluSubpopModel` simulates the flu model given by [this mathematical formulation](math_flu_components.md) for a specific subpopulation. Multiple `FluSubpopModel` instances can be combined as input into a `FluMetapopModel` instance to simulate multiple subpopulations and travel between these subpopulations. To implement a concrete `FluSubpopModel` instance requires user-specified inputs, including demographics, epidemiological parameters, and simulation settings.


## `FluSubpopState`

Note that there is one `FluSubpopState` for each `SubpopModel` instance. The following matrices are for a specific subpopulation. But here we remove the superscript $(\ell)$ for subpopulation $\ell$ for readability ease. 

| Dataclass Field Name                       | Math Variable                        | Dimension                                |
|----------------------------|--------------------------------------|------------------------------------------|
| `S`                        | $\boldsymbol{S}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `E`                        | $\boldsymbol{E}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `IP`                        | $\boldsymbol{IP}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `ISR`                        | $\boldsymbol{ISR}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `ISH`                        | $\boldsymbol{ISH}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `IA`                        | $\boldsymbol{IA}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `HR`                        | $\boldsymbol{HR}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `HD`                        | $\boldsymbol{HD}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `R`                        | $\boldsymbol{R}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `D`                        | $\boldsymbol{D}(0)$                | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `M`  | $\boldsymbol{M}(0)$              | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `MV` | $\boldsymbol{MV}(0)$              | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |


## `FluSubpopParams`

This dataclass specifies the subpopulation's epidemiological parameters -- it specifies values such as transition rates as well as number of age groups and risk groups. We assume that these values do not change throughout the course of the simulation.  

The following variables are for a specific subpopulation, although many of these variables will be the same across subpopulations in practice. Here in the table below we remove the superscript $(\ell)$ for subpopulation $\ell$ for readability ease. Variables that are positive floats can be generalized to be age-risk dependent.

| Dataclass Field Name                            | Math Variable              | Dimension                                |
|---------------------------------|----------------------------|------------------------------------------|
| `num_age_groups`                | $\lvert \mathcal A \rvert$          | positive `int`                           |
| `num_risk_groups`               | $\lvert \mathcal R \rvert$          | positive `int`							  |
| `beta_baseline`                 | $\beta_0$                  | positive `float`                          |
| `total_pop_age_risk`          | $\boldsymbol{N}$           | $\lvert \mathcal A \rvert \times  \lvert \mathcal R \rvert$                           |
| `humidity_impact`               | $\xi$                      | `float`                                   |
| `inf_induced_saturation`  | $o$           | nonnegative `float` |
| `vax_induced_saturation`  | $o_v$           | nonnegative `float` |
| `inf_induced_immune_wane`  | $w$           | nonnegative `float` |
| `vax_induced_immune_wane`  | $w_v$           | nonnegative `float` |
| `inf_induced_inf_risk_reduce`  | $k^I$           | `float` in $[0,1)$ |
| `inf_induced_hosp_risk_reduce`  | $k^H$           | `float` in $[0,1)$ |
| `inf_induced_death_risk_reduce`  | $k^D$           | `float` in $[0,1)$ |
| `vax_induced_inf_risk_reduce`  | $k^I_v$           | `float` in $[0,1)$ |
| `vax_induced_hosp_risk_reduce`  | $k^H_v$           | `float` in $[0,1)$ |
| `vax_induced_death_risk_reduce`  | $k^D_v$           | `float` in $[0,1)$ |
| `R_to_S_rate`                   | $\rateRtoS$                     | positive `float`                          |
| `E_to_I_rate`                   | $\rateEtoI$                   | positive `float`                          |
| `IP_to_IS_rate`				  | $\rateIPtoIS$					   | positive `float`						  |
| `ISR_to_R_rate`                  | $\rateISRtoR$                   | positive `float`                          |
| `ISH_to_H_rate`                  | $\rateISHtoH$                    | positive `float`                          |
| `IA_to_R_rate`				  | $\rateIAtoR$			   | positive `float`						  |
| `HR_to_R_rate`                   | $\rateHRtoR$                 | positive `float`                          |
| `HD_to_D_rate`                   | $\rateHDtoD$                      | positive `float`                          |
| `E_to_IA_prop`                  | $\propIA$                     | `float` in $[0,1]$                        |
| `IP_to_ISH_prop`                  | $\propH$                     | `float` in $[0,1]$                        |
| `ISH_to_HD_prop`                  | $\propD$                     | `float` in $[0,1]$                        |
| `IP_relative_inf`   			  | $r_{IP}$ 				   | positive `float` 						  |
| `IA_relative_inf`			   	  | $r_{IA}$ 				   | positive `float`                          |
| `relative_suscept`			   	  | $\psi$ 				   | $\lvert \mathcal A \rvert \times 1$                          |
| `mobility_modifier`			   	  | $m$ 				   | $\lvert \mathcal A \rvert \times 1$                          |
| `total_contact_matrix`			   	  | $\phi^{(\ell), \text{total}}$				   | $\lvert \mathcal A \rvert \times \lvert A \rvert$                         |
| `school_contact_matrix`			   	  | $\phi^{(\ell), \text{work}}$ 				   | $\lvert \mathcal A \rvert \times \lvert A \rvert$                         |
| `work_contact_matrix`			   	  | $\phi^{(\ell), \text{school}}$				   | $\lvert \mathcal A \rvert \times \lvert A \rvert$                         |
<!-- | `H_to_D_adjusted_prop`    	  | $\boldsymbol{\adjustedpropD}$ | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ |
| `IS_to_H_adjusted_prop`   	  | $\boldsymbol{\adjustedpropH}$ | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ | -->

See the next section on `FluSubpopSchedules` for how $d_{\text{work}}(t)$ and $d_{\text{school}}(t)$ are defined.

## `FluSubpopSchedules`

 Dataclass Field Name  | Column Name             |  Math Variable              | Dimension                                |
|----------------------|----------------------| ----------------------------|------------------------------------------|
| `absolute_humidity` | `absolute_humidity` | $h(t)$ |  positive `float` |
| `flu_contact_matrix` | `is_school_day` | $d_{\text{work}}(t)$ | `float` in $[0,1]$  |
| `flu_contact_matrix` | `is_work_day` | $d_{\text{school}}(t)$ | `bool` |
| `daily_vaccines` | `daily_vaccines` | $V^{(\ell)}_{a, r}(t)$ | $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ of `float` in $[0,1]$  |

## `FluMixingParams`

 Dataclass Field Name                            | Math Variable              | Dimension                                |
|---------------------------------|----------------------------|------------------------------------------|
| `num_locations`                |    $\lvert \mathcal A \rvert$      | positive `int`                            |
| `travel_proportions`               |    $p^{\ell \rightarrow k}$       | $\lvert \mathcal L \rvert \times \lvert \mathcal L \rvert$ of `float` in $[0,1]$                             |

## `SimulationSettings`

The field `timesteps_per_day` is the number of timesteps to take per day, which equals $1/(\Delta t)$, where $\Delta t$ is the discretization interval. The field `transition_type` determines the distribution used for transitions between compartments (see [page on transitions](math_transitions.md)). The field `start_real_date` is the real-world date in string format `"YYYY-MM-DD"` corresponding to the start of the simulation. 

See `SimulationSettings` docstring for other fields (those are not directly related to the mathematical formulation and only specify how simulation output is saved).

> Updated 12/12/2025. Written by LP and updated by Rémy Pasco, edited by Susan Ptak.