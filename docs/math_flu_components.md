# MetroFluSim Mathematical Formulation

<span style="display: none;">
$\def\rateRtoS{\sigma^{R\rightarrow S}}$
$\def\rateEtoI{\sigma^{E \rightarrow [IP, IA]}}$
$\def\rateIPtoIS{\sigma^{IP \rightarrow [ISR, ISH]}}$
$\def\rateIStoR{\gamma^{IS\rightarrow R}}$
$\def\rateISRtoR{\gamma^{ISR\rightarrow R}}$
$\def\rateIStoH{\sigma^{IS\rightarrow H}}$
$\def\rateISHtoH{\sigma^{ISH\rightarrow [HR, HD]}}$
$\def\rateHtoD{\sigma^{H\rightarrow D}}$
$\def\rateHRtoR{\gamma^{HR\rightarrow R}}$
$\def\rateHDtoD{\sigma^{HD\rightarrow D}}$
$\def\rateIAtoR{\gamma^{IA\rightarrow R}}$
$\def\rateHtoR{\gamma^{H\rightarrow R}}$
$\def\totalforceofinfection{\lambda^{(\ell), \text{total}}_{a}(t)}$
$\def\propIA{\pi^{IA}}$
$\def\propH{\pi^H}$
$\def\propD{\pi^D}$
$\def\adjustedpropH{\tilde{\pi}^H}$
$\def\adjustedpropD{\tilde{\pi}^D}$
$\def\proptravelelltok{p^{\ell \rightarrow k}}$
$\def\propdaytravelktoell{p^{k \rightarrow \ell}}$
$\def\LambdaIlocagerisktime{\Lambda^{(\ell), I}_{a,r}(t)}$
$\def\LambdaHlocagerisktime{\Lambda^{(\ell), H}_{a,r}(t)}$
$\def\LambdaDlocagerisktime{\Lambda^{(\ell), D}_{a,r}(t)}$
$\def\locationell{^{(\ell)}}$
$\def\locationk{^{(k)}}$
$\def\locagerisk{\locationell_{a, r}}$
$\def\locagerisktime{\locagerisk(t)}$
$\def\agetime{_a(t)}$
$\def\agerisk{_{a, r}}$
$\def\agerisktime{_{a, r}(t)}$
$\def\ageprimeriskprime{_{a^\prime, r^\prime}}$
$\def\locageprimeriskprime{\locationell_{a^\prime, r^\prime}}$
$\def\Nlocagerisk{N\locationell_{a,r}}$
$\def\effectiveNlocagerisktime{\tilde{N}\locationell_{a,r}(t)}$
$\def\effectiveNlocageprimeriskprimetime{\tilde{N}\locationell_{a^\prime,r^\prime}(t)}$
$\def\multipliersymptom{h_{\text{symp}}}$
$\def\tvarloc{y^{(\ell)}}$
$\def\jointtvarloc{y^{(\ell) *}}$
$\def\simstate{\boldsymbol{\Xi}_t}$
$\def\agegroups{\mathcal A}$
$\def\riskgroups{\mathcal R}$
$\def\numagegroups{\lvert \agegroups \rvert}$
$\def\numriskgroups{\lvert \riskgroups \rvert}$
$\def\poik{\text{POI}(k)}$
$\def\poiell{\text{POI}(\ell)}$
</span>

The mathematical framework is inspired by the immunoSEIRS model of the Meyers Lab (see [Bi and Bandekar et al. 2023](https://www.researchgate.net/publication/375193467_Scenario_Projections_for_SARS-CoV-2_Influenza_and_RSV_Burden_in_the_US_2023-2024), [Bi et al. 2022](https://repositories.lib.utexas.edu/server/api/core/bitstreams/da7bb152-3343-4135-86e3-040546d6e5b5/content) and [Bouchnita et al. 2021](https://repositories.lib.utexas.edu/items/e8d50517-6e78-488b-8c95-8c1138d90c28) for some related recent publications).

Please note that the MetroFluSim model requires extensive notation -- please review the entirety of each section for provided definitions.

## Flu model: diagram

![flu_model_diagram](figs/flu_model_diagram.png)

## Flu model: deterministic differential equations

We start off with common indices and arguments:
- $t \in \mathbb N$: current simulation day
- $\ell$: location, i.e. subpopulation, $\mathcal L$: set of all locations/subpopulations
- $a$: age group, $\agegroups$: set of all age groups
- $r$: risk group, $\riskgroups$: set of all risk groups
- $i$: type of immunity-inducing event, $\mathcal{I} := \left\{\text{infection}, \text{vaccine}\right\}$: the set of all types of immunity-inducing events: infection and vaccination, respectively.

### Population-level immunity 

For each $\ell \in \mathcal L$, $a \in \agegroups$, $r \in \riskgroups$:

\begin{align*}
\frac{dM\locationell\agerisktime}{dt} &= \frac{\rateRtoS(t) R\locagerisktime}{N\locationell_{a, r}} \cdot (1 - oM\locationell\agerisktime - o_v MV\locationell\agerisktime) - wM\locationell\agerisktime \tag{M1} \\
\frac{dMV\locationell\agerisktime}{dt} &= V\locationell\agerisk(t - \delta) - w_v MV\locationell\agerisktime \tag{M2}
\end{align*}

where

- $\rateRtoS$: rate at which recovered individuals become susceptible, so that $1/\rateRtoS$ is the average number of days a person is totally immune from reinfection until being susceptible again.
- $V\locationell\agerisktime$: proportion of individuals residing in location $\ell \in \mathcal L$ in age-risk group $a$, $r$ who receive vaccines at time $t$.
- $o$, $o_v$: positive constants modeling the saturation of antibody production in individuals who have infection-induced immunity and vaccination-induced immunity, respectively.
- $\delta$: number of days after dose for vaccine to become effective.
- $w$: rate at which infection-induced immunity wanes.
- $w_V$: rate at which vaccine-induced immunity wanes.

### Compartment equations

Note that the following are all $\numagegroups \times \numriskgroups \times \lvert \mathcal I \rvert$ matrices:

- **$\boldsymbol{K}^I = [\boldsymbol{K}^I, \boldsymbol{K}^I_{V}]$**: reduction in infection risk from given immunity-inducing event.  
- **$\boldsymbol{K}^{H} = [\boldsymbol{K}^H, \boldsymbol{K}^H_{V}]$**: reduction in hospitalization risk from given immunity-inducing event.  
- **$\boldsymbol{K}^D = [\boldsymbol{K}^D, \boldsymbol{K}^D_{V}]$**: reduction in death risk from given immunity-inducing event.  
- **$\boldsymbol{M}\locationell = \boldsymbol{M}\locationell(t) = [\boldsymbol{M}\locationell(t), \boldsymbol{M}\locationell_{V}(t)]$**: location $\ell \in \mathcal L$ population-level immunity.  

To simplify notation, we have the following terms that characterize the effect of population-level immunities for a given subpopulation $\ell$, age $a$, and risk $r$:

\begin{align*}
\LambdaIlocagerisktime &= \left[\frac{\boldsymbol{K_{a,r}^{I}}}{\boldsymbol{1}_{\lvert \mathcal L \rvert \times 1} - \boldsymbol{K_{a,r}^{I}}}\right]^T \boldsymbol{M_{a,r}\locationell(t)} \tag{I1} \\
\LambdaHlocagerisktime &= \left[\frac{\boldsymbol{K_{a,r}^{H}}}{\boldsymbol{1}_{\lvert \mathcal L \rvert \times 1} - \boldsymbol{K_{a,r}^{H}}}\right]^T \boldsymbol{M_{a,r}\locationell(t)} \tag{I2} \\
\LambdaDlocagerisktime &= \left[\frac{\boldsymbol{K_{a,r}^{D}}}{\boldsymbol{1}_{\lvert \mathcal L \rvert \times 1} - \boldsymbol{K_{a,r}^{D}}}\right]^T \boldsymbol{M_{a,r}\locationell(t)} \tag{I3}
\end{align*}

where $\boldsymbol{1}_{\lvert \mathcal L \rvert \times 1}$ is an $\lvert \mathcal L \rvert \times 1$ vector of $1$'s and the fraction notation indicates element-wise division. 

For each $\ell \in \mathcal L$, $a \in \agegroups$, $r \in \riskgroups$, we have the following equations that characterize transitions between compartments:

\begin{align}
\frac{dS\locagerisktime}{dt} &= \underbrace{\rateRtoS R\locagerisktime}_{\text{$R$ to $S$}} 
-\underbrace{S\locagerisktime \frac{\beta\locationell(t) \cdot \totalforceofinfection}{\left(1 + \LambdaIlocagerisktime\right)}}_{\text{$S$ to $E$}} \tag{C1} \\[1.5em]
\frac{dE\locagerisktime}{dt} &= \underbrace{S\locagerisktime \frac{\beta\locationell(t) \cdot \totalforceofinfection}{\left(1 + \LambdaIlocagerisktime\right)}}_{\text{$S$ to $E$}} - \underbrace{\rateEtoI (1-\propIA) E\locagerisktime}_{\text{$E$ to $IP$}} - \underbrace{\rateEtoI \propIA E\locagerisktime}_{\text{$E$ to $IA$}} \tag{C2} \\[1.5em]
\frac{dIP\locagerisktime}{dt} &= \underbrace{\rateEtoI (1-\propIA) E\locagerisktime}_{\text{$E$ to $IP$}} - \underbrace{\rateIPtoIS IP\locagerisktime}_{\text{$IP$ to $ISR$ and $ISH$}} \tag{C3} \\[1.5em]
\frac{dISR\locagerisktime}{dt} &= \underbrace{\left(1-\frac{\pi^H}{1 + \LambdaHlocagerisktime}\right)\rateIPtoIS IP\locagerisktime}_{\text{$IP$ to $ISR$}} 
- \underbrace{\rateISRtoR ISR\locagerisktime}_{\text{$ISR$ to $R$}} \tag{C4} \\[1.5em]
\frac{dISH\locagerisktime}{dt} &= \underbrace{\frac{\pi^H \rateIPtoIS IP\locagerisktime}{1 + \LambdaHlocagerisktime}}_{\text{$IP$ to $ISH$}} - \underbrace{\rateISHtoH ISH\locagerisktime}_{\text{$ISH$ to $HR$ and $HD$}} \tag{C5} \\[1.5em]
\frac{dIA\locagerisktime}{dt} &= \underbrace{\rateEtoI \propIA E\locagerisktime}_{\text{$E$ to $IA$}} 
- \underbrace{\rateIAtoR IA\locagerisktime}_{\text{$IA$ to $R$}} \tag{C6} \\[1.5em]
\frac{dHR\locagerisktime}{dt} &= \underbrace{\left(1-\frac{\pi^D_{a, r}}{1 + \LambdaDlocagerisktime}\right) \rateISHtoH ISH\locagerisktime}_{\text{$ISH$ to $HR$}} - \underbrace{\rateHRtoR HR\locagerisktime}_{\text{$HR$ to $R$}} \tag{C7} \\[1.5em]
\frac{dHD\locagerisktime}{dt} &= \underbrace{\frac{\pi^D_{a, r} \rateISHtoH ISH\locagerisktime}{1 + \LambdaDlocagerisktime}}_{\text{$ISH$ to $HD$}} - \underbrace{\rateHDtoD HD\locagerisktime}_{\text{$HD$ to $D$}} \tag{C8} \\[1.5em]
\frac{dR\locagerisktime}{dt} &= \underbrace{\rateISRtoR ISR\locagerisktime}_{\text{$ISR$ to $R$}} + \underbrace{\rateIAtoR IA\locagerisktime}_{\text{$IA$ to $R$}} + \underbrace{\rateHRtoR HR\locagerisktime}_{\text{$HR$ to $R$}}
- \underbrace{\rateRtoS R\locagerisktime}_{\text{$R$ to $S$}} \tag{C9} \\[1.5em]
\frac{dD\locagerisktime}{dt} &= \underbrace{\rateHDtoD HD\locagerisktime}_{\text{$HD$ to $D$}}. \tag{C10}
\end{align}

where

- The $\lambda$-terms are location/subpopulation mixing terms that we define in the next section on the travel model. 
- $\boldsymbol{N}\locationell$: $\numagegroups \times \numriskgroups$ matrix corresponding to total population in location $\ell \in \mathcal L$, where element $N\locagerisk$ is the total population of age group $a$ and risk group $\ell$ in location $\ell$.
- $\beta\locationell(t) = \beta\locationell_0 (1 + q(t))$: time-dependent transmission rate per day for individuals residing in location $\ell \in \mathcal L$. 
- $q(t) = \xi \cdot \exp^{-180 * h(t)}$: humidity adjustment, where $\xi$ is the humidity impact factor and $h(t)$ is absolute humidity. This formula is taken from [this paper](https://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.1000316#s4).
- $\propIA$: proportion exposed who are completely asymptomatic when infectious.
- $r_{IP}$, $r_{IA}$: relative infectiousness (compared to infected symptomatic people) of infected presymptomatic and infected asymptomatic people respectively. 
- $\rateISRtoR, \rateHRtoR, \rateIAtoR$: recovery rates for infected symptomatic ($ISR$),  hospital ($HR$), and infected asymptomatic ($IA$) compartments respectively, so that $1/\gamma$ is the average number of days it takes for an infected person not in the hospital to recover, and $1/\rateHtoR$ is analogous, but for an infected person in the hospital. 
- $\rateEtoI$: infection rate (both exposed to infected presymptomatic transition rate and exposed to infected asymptomatic transition rate), so that $1/\rateEtoI$ is the average number of days after exposure before a person becomes infectious.
- $\rateIPtoIS$: infected presymptomatic to infected symptomatic (both $ISR$ and $ISH$) transition rate, so that $1/\rateIPtoIS$ is the average number of days that an infected person is presymptomatic before becoming symptomatic. 
- $\rateISHtoH$: hospitalization rate (infected to hospital transition rate), so that $1/\rateISHtoH$ is the average number of days a person is infected before going to the hospital.
- $\rateHDtoD$: death rate from hospital, so that $1/\rateHDtoD$ is the average number of days a person spends in the hospital before dying.
<!-- - $\boldsymbol{\adjustedpropH}$, where $\adjustedpropH_{a, r} = \frac{\propH_{a, r}\rateIStoR}{\rateIStoH - \propH_{a, r}(\rateIStoH-\rateIStoR)}$: adjusted proportion hospitalized based on age-risk group $a, r$ group actually used in model -- this adjustment is necessary to ensure actual proportion hospitalized recapitulates $[\propH_{a, r}]$. -->
- $\boldsymbol{\propH}$: $\numagegroups \times \numriskgroups$ proportion hospitalized based on age-risk group $a, r$.
<!-- - $\boldsymbol{\adjustedpropD}$, where $\adjustedpropD_{a, r} = \frac{\propD_{a, r} \rateHtoR}{\rateHtoD - \propD_{a, r} (\rateHtoD-\rateHtoR)}$: adjusted in-hospital mortality rate (as in, proportion who die in the hospital based on age group) actually used in model -- this adjustment is necessary to ensure actual proportion who die in the hospital recapitulates $[\propD_{a, r} ]$. -->
- $\boldsymbol{\propD}$: $\numagegroups \times \numriskgroups$ in-hospital mortality rate (proportion who die based on age-risk group $a, r$).

## Flu model: travel model

### Exposure intensity

For each $\ell \in \mathcal L$, $k \in \mathcal L \setminus \{\ell\}$, $a \in \agegroups$, we have

\[
\totalforceofinfection = \lambda^{(\ell), \text{local}}\agetime + \lambda^{(\ell), \text{visitors}}\agetime + \lambda^{(\ell), \text{residents traveling}}\agetime. \tag{T1}
\]

This can loosely can be interpreted as exposure intensity: the (weighted) proportion of the population that interacts with $\ell,a$ individuals that are infectious. 

The decompositions model the following phenomenon:

- $\lambda^{(\ell), \text{local}}\agetime$: rate at which individuals in location $\ell$ get exposed to infected people who live in location $\ell$ (this contact occurs in location $\ell$).
- $\lambda^{(\ell), \text{visitors from } k}\agetime$: rate at which individuals in location $\ell$ get exposed to infected people who live in location $k$ but travel to location $\ell$ (this contact occurs in location $\ell$).
- $\lambda^{(\ell), \text{residents traveling to } k}\agetime$: rate at which individuals in location $\ell$ get exposed to infected people who live in location $k$, due to individuals who live in location $\ell$ traveling to location $k$ (this contact occurs in location $k$).

Note that we assume this exposure intensity is the same for a given age group regardless of risk group, so we do not have the $r$-subscript here.

Specifically, we have

\begin{align*}
\lambda^{(\ell), \text{local}}\agetime &= \psi_a \left(1 - m_a \sum_{k \in \mathcal L \setminus \{\ell\}} \proptravelelltok \right) \cdot \sum \limits_{a^\prime \in \agegroups} \phi\locationell_{a,a^\prime}(t) \frac{\texttt{I_weighted}\locationell_{a^\prime}(t)}{\sum_{r^\prime \in \riskgroups} \effectiveNlocageprimeriskprimetime} \tag{T2} \\[1.5em]
\lambda^{(\ell), \text{visitors}}\agetime &= \sum_{k \in \mathcal L \setminus \{\ell\}} \underbrace{\left(\psi_a \cdot \propdaytravelktoell \cdot \sum\limits_{a^\prime \in \agegroups} m_{a^\prime}(t) \cdot \phi\locationell_{a, a^\prime}(t) \frac{\texttt{I_weighted}\locationk_{a^\prime}(t)}{\sum_{r^\prime \in \riskgroups} \effectiveNlocageprimeriskprimetime}\right)}_{\text{Each summand: } \lambda^{(\ell), \text{visitors from } k}\agetime} \tag{T3} \\[1.5em]
\lambda_{a,r}^{(\ell), \text{residents traveling}}(t) &= \sum_{k \in \mathcal L \setminus \{\ell\}}  \underbrace{\left(\psi_a \cdot \proptravelelltok  \cdot m_a \cdot \sum\limits_{a^\prime \in \agegroups} \phi\locationell_{a, a^\prime}(t) \frac{\texttt{I_weighted}\locationk_{a^\prime}(t)}{\sum_{r^\prime \in \riskgroups} \tilde{N}^{(k)}_{a^\prime, r^\prime} (t)}\right)}_{\text{Each summand: } \lambda\agerisk^{(\ell), \text{residents traveling to } k}(t)} \tag{T4}
\end{align*}

where

\[
\texttt{I_weighted}\locationell_{a^\prime}(t) := \sum_{r^\prime \in \riskgroups} \left[ISR\locationell\ageprimeriskprime(t) + ISH\locationell\ageprimeriskprime(t) + r_{IP} IP\locationell\ageprimeriskprime(t) + r_{IA} IA\locationell\ageprimeriskprime(t)\right] \tag{T5}
\]

and where

\begin{align*}
\effectiveNlocagerisktime &= \Nlocagerisk + m_a \cdot \sum_{k \in \mathcal L \setminus \{\ell\}} \propdaytravelktoell \cdot (N^{(k)}_{a, r} - HR^{(k)}_{a,r}(t) - HD^{(k)}_{a,r}(t)) \\ &\quad\quad\quad - m_a \cdot \sum_{k \in \mathcal L \setminus \{\ell\}} \proptravelelltok  \cdot (N\locagerisk - HR\locationell_{a,r}(t) - HD\locationell_{a,r}(t)) \tag{T6}
\end{align*}

is the effective population in location $\ell \in \mathcal L$ and age-risk group $a \in \agegroups$, $r \in \riskgroups$ at time $t$.

### Contact matrix

The contact matrix is defined as

- $\phi_{a, a^\prime}\locationell(t)$: the number of contacts that individuals in age group $a \in \agegroups$ residing in location $\ell \in \mathcal L$ have with other individuals (regardless of location) in age group $a^\prime \in \agegroups$ on day $t$.

Let $\phi^{(\ell), \text{total}}$, $\phi^{(\ell), \text{work}}$, and $\phi^{(\ell), \text{school}}$ represent the total contact matrix, school contact matrix, and work contact matrix, respectively for subpopulation $\ell$.

Then the contact matrix for the subpopulation at time $t$ is
$$
\phi^{(\ell)}(t) := \phi^{(\ell), \text{total}} - (1 - d_{\text{work}}(t)) \phi^{(\ell), \text{work}} - (1 - d_{\text{school}}(t)) \phi^{(\ell), \text{school}}
$$
where $d_{\text{work}}(t)$ is $1$ if the real-world date corresponding to simulation time $t$ is a work day and $0$ otherwise, and $d_{\text{school}}(t) \in [0, 1]$ is defined analogously for school days and represents the proportion of schools open on day $t$.

### Other travel parameters

We have

- $\psi_a \in [0, 1]$: relative susceptibility of individuals in age group $a \in \agegroups$.
- $m_a$: a positive scalar modifying travel intensity depending on age $a$.
- $\propdaytravelktoell$: (on average) proportion of the day that a resident of $k$ spends traveling to location $\ell \in \mathcal L$.

Note that the arrows in $\propdaytravelktoell$ correspond to direction of travel (e.g. $k \rightarrow \ell$ represents residents of location $k$ traveling to location $\ell$). The $\propdaytravelktoell$ values are calculated from mobility data, corresponding to

\begin{align*}
\bar{p}^{k \rightarrow \ell} &= \sum_{\poiell} c^{\poiell} \cdot v^{k \rightarrow \poiell} \tag{T7} \\[1.5em]
\propdaytravelktoell &= \frac{\bar{p}^{k \rightarrow \ell}}{\sum_{\ell \in \mathcal L} \bar{p}^{k \rightarrow \ell}} \tag{T8}
\end{align*}

where

- $c^{\poiell}$: average proportion of a day spent at $\poiell$.
- $v^{k \rightarrow \poiell}$: average number of visits per day per resident of $k$ to $\poiell$.

Therefore the rows of the mobility matrix $p$ sum to 1: $\sum_{\ell \in \mathcal L} \propdaytravelktoell = 1$.

## Flu model: discretized stochastic implementation

To actually implement/simulate this compartmental model, we discretize the deterministic differential equations and treat transitions between compartments as stochastic to model uncertainty. We extend the notation from the deterministic differential equations to capture the stochastic elements.

Let **$\boldsymbol{\mathcal X}(t) = \left\{\boldsymbol{S}(t), \boldsymbol{E}(t), \boldsymbol{IA}(t), \boldsymbol{IP}(t), \boldsymbol{ISR}(t), \boldsymbol{ISH}(t), \boldsymbol{HR}(t), \boldsymbol{HD}(t), \boldsymbol{R}(t), \boldsymbol{D}(t), \boldsymbol{M}(t), \boldsymbol{MV}(t), q(t), \boldsymbol{\phi}(t), V\locationell(t)\right\}$** be the "simulation state" at time $t$. **$\boldsymbol{\mathcal X}(t)$** is a set of matrices. 

Let **$\boldsymbol{\Theta}$** be the set of fixed parameters. 

Then given initial state $\boldsymbol{\mathcal X}_0 = \boldsymbol{\mathcal X}(0)$, we can formulate our discretized stochastic implementation as

$$
\boldsymbol{\mathcal X}(t + \Delta t) = \boldsymbol{\mathcal X}(t) + f\left(\boldsymbol{\mathcal X}(t), \Delta t, \omega; \boldsymbol{\Theta}\right) \quad \text{for} \quad t \ge 0,
$$

where $f$ is parametrized by $\boldsymbol{\Theta}$, and depends on the step size of discretization $\Delta t$ and a sample path $\omega$. We assume that each sample path $\omega$ is realized from a random process that does not depend on $\boldsymbol{\mathcal X}(t)$ or $\Delta t$ for each $t$. When we are discussing a single model with a fixed set of parameters $\boldsymbol{\Theta}$, we drop the $\boldsymbol{\Theta}$ notation for simplicity.  

Now we formulate how we implement discretized stochastic transitions. We assume that $q(t)$, **$\boldsymbol{\phi}(t)$**, and $V\locationell(t)$  are updated deterministically according to some "schedule."  

We model stochastic transitions between compartments using "transition variables." Transition variables correspond to incoming and outgoing flows of epidemiological compartments (see the compartment equations above). 

Below we formulate the discretized stochastic transitions. Note that the population-level immunity variables behave as aggregate epidemiological metrics. They are deterministic functions of the simulation state and transitions between compartments.

> **IMPORTANT NOTE_: all $y$ and $y^*$-variables depend on $\left(\boldsymbol{\mathcal X}(t), \Delta t, \omega\right)$. For notation simplicity, we define $\simstate := \left(\boldsymbol{\mathcal X}(t), \Delta t, \omega\right)$ and write $y$ and $y^*$-variables as functions of $\simstate$.**

For each $\ell \in \mathcal L$, $a \in \agegroups$, and $r \in \riskgroups$:

\begin{align}
M_{a, r}\locationell(t + \Delta t) &= M\locationell\agerisktime + \frac{dM\locationell\agerisktime}{dt} \cdot \Delta t \\
MV_{a,r}\locationell(t + \Delta t) &= MV\agerisktime\locationell + \frac{dMV\agerisktime\locationell}{dt} \cdot \Delta t \\
S\locagerisk(t + \Delta t) &= S_{a, r}\locationell(t) + \underbrace{\tvarloc_{R\rightarrow S, a, r}(\Xi_t)}_{\text{$R$ to $S$}} - \underbrace{\tvarloc_{S\rightarrow E, a, r}(\Xi_t)}_{\text{$S$ to $E$}} \\
E\locagerisk(t + \Delta t) &= E_{a, r}\locationell(t) + \underbrace{\tvarloc_{S\rightarrow E, a, r}(\Xi_t)}_{\text{$S$ to $E$}} - \underbrace{\jointtvarloc_{E\rightarrow IP, a, r}(\Xi_t)}_{\text{$E$ to $IP$}} - \underbrace{\jointtvarloc_{E\rightarrow IA, a, r}(\Xi_t)}_{\text{$E$ to $IA$}} \\
IP\locagerisk(t + \Delta t) &= IP\locagerisktime + \underbrace{\jointtvarloc_{E\rightarrow IP, a, r}(\Xi_t)}_{\text{$E$ to $IP$}} - \underbrace{\tvarloc_{IP \rightarrow ISR, a, r}(\Xi_t)}_{\text{$IP$ to $ISR$}} - \underbrace{\tvarloc_{IP \rightarrow ISH, a, r}(\Xi_t)}_{\text{$IP$ to $ISH$}} \\
IA_{a, r}\locationell(t + \Delta t) &= IA_{a, r}\locationell(t) + \underbrace{\jointtvarloc_{E\rightarrow IA, a, r}(\Xi_t)}_{\text{$E$ to $IA$}} - \underbrace{\tvarloc_{IA \rightarrow R, a, r}(\Xi_t)}_{\text{$IA$ to $R$}} \\
ISR\locagerisk(t + \Delta t) &= ISR_{a, r}\locationell(t) + \underbrace{\tvarloc_{IP \rightarrow ISR, a, r}(\Xi_t)}_{\text{$IP$ to $ISR$}} - \underbrace{\jointtvarloc_{ISR \rightarrow R, a, r}(\Xi_t)}_{\text{$ISR$ to $R$}} \\
ISH\locagerisk(t + \Delta t) &= ISH_{a, r}\locationell(t) + \underbrace{\tvarloc_{IP \rightarrow ISH, a, r}(\Xi_t)}_{\text{$IP$ to $ISH$}} - \underbrace{\jointtvarloc_{ISH \rightarrow HR, a, r}(\Xi_t)}_{\text{$ISH$ to $HR$}} - \underbrace{\jointtvarloc_{ISH \rightarrow HD, a, r}(\Xi_t)}_{\text{$ISH$ to $HD$}} \\
HR\locagerisk(t + \Delta t) &= HR_{a, r}\locationell(t) + \underbrace{\jointtvarloc_{ISH \rightarrow HR, a, r}(\Xi_t)}_{\text{$ISH$ to $HR$}} - \underbrace{\jointtvarloc_{HR\rightarrow R, a, r}(\Xi_t)}_{\text{$HR$ to $R$}} \\
HD\locagerisk(t + \Delta t) &= HD_{a, r}\locationell(t) + \underbrace{\jointtvarloc_{ISH \rightarrow HD, a, r}(\Xi_t)}_{\text{$ISH$ to $HD$}} - \underbrace{\jointtvarloc_{HD\rightarrow D, a, r}(\Xi_t)}_{\text{$HD$ to $D$}} \\
R\locagerisk(t + \Delta t) &= R_{a, r}\locationell(t) + \underbrace{\jointtvarloc_{IA \rightarrow R, a, r}(\Xi_t)}_{\text{$IA$ to $R$}} + \underbrace{\jointtvarloc_{ISR \rightarrow R, a, r}(\Xi_t)}_{\text{$ISR$ to $R$}} + \underbrace{\jointtvarloc_{HR\rightarrow R, a, r}(\Xi_t)}_{\text{$HR$ to $R$}} - \underbrace{\tvarloc_{R\rightarrow S, a, r}(\Xi_t)}_{\text{$R$ to $S$}} \\
D\locagerisk(t + \Delta t) &= D_{a, r}\locationell(t) + \underbrace{\jointtvarloc_{HD\rightarrow D, a, r}(\Xi_t)}_{\text{$HD$ to $D$}}. 
\end{align}


> **IMPORTANT NOTE: the "$*$" superscript indicates that the transition variable has a joint distribution with another transition variable. In general, if a compartment has more than one outgoing transition variable, these transition variables must be modeled jointly.** 

Consider the two transitions out of the infected symptomatic compartment, for example. Given that a patient is infected and symptomatic, exactly one outcome occurs: they recover (from home) or they go to the hospital. Since one and only one of these outcomes must occur, we must model these two transition variables jointly. Joint distribution derivation details are provided in the next section on transition types.

Each transition variable depends on a "base count" and a "rate" (which both depend on the current state of the system). This decomposition is displayed in the table below. Note that these transitions are for a given location $\ell \in \mathcal L$.

| Transition            | Transition variable                                                      | Base count               | Rate                                                                                                                                                                                           |
|-----------------------|-------------------------------------------------------------------------|--------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| $R$ to $S$            | $\tvarloc_{R \rightarrow S, a, r}(\simstate)$                         | $R_{a, r}\locationell(t)$    | $\rateRtoS$                                                                                                                                                                                           |
| $S$ to $E$            | $\tvarloc_{S \rightarrow E, a, r}(\simstate)$                         | $S_{a, r}\locationell(t)$    | $\frac{\beta(t) \totalforceofinfection}{1 + \LambdaIlocagerisktime}$ |
| $E$ to $IP$           | $\jointtvarloc_{E \rightarrow IP, a, r}(\simstate)$                      | $E_{a, r}\locationell(t)$    | $\rateEtoI (1 - \propIA)$                                                                                                                                                                                |
| $E$ to $IA$           | $\jointtvarloc_{E \rightarrow IA, a, r}(\simstate)$                      | $E_{a, r}\locationell(t)$    | $\rateEtoI \propIA$                                                                                                                                                                                     |
| $IP$ to $ISR$          | $\tvarloc_{IP \rightarrow ISR, a, r}(\simstate)$                       | $IP_{a, r}\locationell(t)$   | $\rateIPtoIS \left(1-\frac{\pi^H_{a,r}}{1 + \LambdaHlocagerisktime}\right)$                                                                                                                                                                                     |
| $IP$ to $ISH$          | $\tvarloc_{IP \rightarrow ISH, a, r}(\simstate)$                       | $IP_{a, r}\locationell(t)$   | $\rateIPtoIS \frac{\pi^H_{a,r}}{1 + \LambdaHlocagerisktime}$                                                                                                                                                                                     |
| $ISR$ to $R$           | $\jointtvarloc_{ISR \rightarrow R, a, r}(\simstate)$                      | $ISR_{a, r}\locationell(t)$   | $\gamma^{ISR\rightarrow R}$                                                                                                                                                                    |
| $ISH$ to $HR$           | $\jointtvarloc_{ISH \rightarrow HR, a, r}(\simstate)$                      | $ISH_{a, r}\locationell(t)$   | $\rateISHtoH \left(1-\frac{\pi^D_{a,r}}{1 + \LambdaDlocagerisktime}\right)$                                                                                                            |
| $ISH$ to $HD$           | $\jointtvarloc_{ISH \rightarrow HD, a, r}(\simstate)$                      | $ISH_{a, r}\locationell(t)$   | $\rateISHtoH \frac{\pi^D_{a,r}}{1 + \LambdaDlocagerisktime}$                                                                                                            |
| $IA$ to $R$           | $\tvarloc_{IA \rightarrow R, a, r}(\simstate)$                        | $IA_{a, r}\locationell(t)$   | $\rateIAtoR$                                                                                                                                                              |
| $HR$ to $R$            | $\jointtvarloc_{HR \rightarrow R, a, r}(\simstate)$                       | $HR_{a, r}\locationell(t)$    | $\rateHRtoR$                                                                                                                                                                 |
| $HD$ to $D$            | $\jointtvarloc_{HD \rightarrow D, a, r}(\simstate)$                       | $HD_{a, r}\locationell(t)$    | $\rateHDtoD$                                                                                                              |


The base count and rate of a transition variable parameterize the distribution that defines its realization. 

See [this page](math_transitions.md) for mathematical formulations of marginal and joint stochastic transitions between compartments. 

## General model: discretized stochastic implementation

We make the important note that the flu model's discretized stochastic implementation can be generalized to models with different structures. More broadly, we let $\boldsymbol{\mathcal C}(t)$ be a model's set of epidemiological compartments, $\boldsymbol{\mathcal M}(t)$ its set of aggregate epidemiological metrics, and $\boldsymbol{S (t)}$ its set of schedule-dependent (time-dependent) deterministic values. Then the above formulation still holds.

In fact, in our code, we model $\boldsymbol{\mathcal C(t)}$ using an `Compartment` class, $\boldsymbol{\mathcal M}(t)$ using an `EpiMetric` class, and $\boldsymbol{\mathcal S(t)}$ using a `Schedule` class. We handle stochastic transitions using `TransitionVariable` and `TransitionVariableGroup` classes. These classes form some of the building blocks of the base model code. 

> Updated 12/12/2025. Documentation written by LP and updated by Rémy Pasco, mathematical notation by LP (advised by Lauren Meyers and Dave Morton, edited by Susan Ptak, Meyers Lab, and Shiyuan Liang), travel model conceptualized by Rémy and Susan Ptak, immunity formulation by Anass Bouchnita.