# MetroFluSim Mathematical Formulation

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
$\def\proptravelelltok{v^{\ell \rightarrow k}}$
$\def\proptravelktoell{v^{k \rightarrow \ell}}$
$\def\LambdaIlocagerisktime{\Lambda^{(\ell), I}_{a,r}(t)}$
$\def\LambdaHlocagerisktime{\Lambda^{(\ell), H}_{a,r}(t)}$
$\def\LambdaDlocagerisktime{\Lambda^{(\ell), D}_{a,r}(t)}$
$\def\locagerisk{^{(\ell)}_{a, r}}$
$\def\locagerisktime{\locagerisk(t)}$
$\def\locationell{^{(\ell)}}$
$\def\locationk{^{(k)}}$
$\def\agerisk{_{a, r}}$
$\def\agerisktime{_{a, r}(t)}$
$\def\ageprimeriskprime{_{a^\prime, r^\prime}}$
$\def\locageprimeriskprime{^{(\ell)}_{a^\prime, r^\prime}}$
$\def\Nlocagerisk{N^{(\ell)}_{a,r}}$
$\def\effectiveNlocagerisktime{\tilde{N}^{(\ell)}_{a,r}(t)}$
$\def\effectiveNlocageprimeriskprimetime{\tilde{N}^{(\ell)}_{a^\prime,r^\prime}(t)}$
$\def\multipliertravel{h_{\text{travel}}}$
$\def\multipliersymptom{h_{\text{symp}}}$
$\def\tvarloc{y^{(\ell)}}$
$\def\jointtvarloc{y^{(\ell) *}}$
$\def\simstate{\boldsymbol{\Xi}_t}$
$\def\agegroups{\mathcal A}$
$\def\riskgroups{\mathcal R}$
$\def\numagegroups{\lvert \agegroups \rvert}$
$\def\numriskgroups{\lvert \riskgroups \rvert}$
</span>

> **_Written by LP, edited by Susan Ptak, travel model formulated by Rémy Pasco, immunity formulation advised by Anass Bouchnita, overall formulation advised by Lauren Meyers and Dave Morton, updated 08/05/2025 (work in progress)_**

## Flu model: diagram

![flu_model_diagram](figs/flu_model_diagram.png)

## Flu model: deterministic differential equations
- $t \in \mathbb N$: current simulation day
- $\ell$: location, i.e. subpopulation, $\mathcal L$: set of all locations/subpopulations
- $a$: age group, $\agegroups$: set of all age groups
- $r$: risk group, $\riskgroups$: set of all risk groups
- $i$: type of immunity-inducing event, $\mathcal{I} := \left\{\text{infection}, \text{vaccine}\right\}$: the set of all types of immunity-inducing events: infection and vaccination, respectively.
- $\boldsymbol{O}$: $\numagegroups \times \numriskgroups \times \lvert \mathcal{I} \rvert$ matrix, where the $(a, r, i)$th element is the positive constant modeling the saturation of antibody production in individuals in age group $a$ and risk group $r$ who had immunity-inducing event $i$.

**Population-level immunity**

For each $\ell \in \mathcal L$, $a \in \agegroups$, $r \in \riskgroups$:

\begin{align*}
\frac{dM^{(\ell)}\agerisktime}{dt} &= \frac{\rateRtoS(t) R\locagerisktime}{N^{(\ell)}_{a, r}} \cdot (1 - oM^{(\ell)}\agerisktime - o_v MV^{(\ell), I}\agerisktime) - wM^{(\ell), I}\agerisktime\\
\frac{dMV^{(\ell)}\agerisktime}{dt} &= \frac{V^{(\ell)}\agerisk(t - \delta)}{N^{(\ell)}\agerisk} - w_v MV^{(\ell)}\agerisktime 
\end{align*}

where

- $\rateRtoS$: rate at which recovered individuals become susceptible, so that $1/\rateRtoS$ is the average number of days a person is totally immune from reinfection until being susceptible again.
- $V^{(\ell)}\agerisktime$: number of vaccine doses administered at time $t$ to individuals residing in location $\ell \in \mathcal L$ in age-risk group $a$, $r$.
- $\delta$: number of days after dose for vaccine to become effective.
- $w$: rate at which infection-induced immunity wanes.
- $w_V$: rate at which vaccine-induced immunity wanes.

**Compartment equations**

Note that the following are all $\numagegroups \times \numriskgroups \times \lvert \mathcal I \rvert$ matrices:

- **$\boldsymbol{K}^I = [\boldsymbol{K}^I, \boldsymbol{K}^I_{V}]$**: reduction in infection risk from given immunity-inducing event.  
- **$\boldsymbol{K}^{H} = [\boldsymbol{K}^H, \boldsymbol{K}^H_{V}]$**: reduction in hospitalization risk from given immunity-inducing event.  
- **$\boldsymbol{K}^D = [\boldsymbol{K}^D, \boldsymbol{K}^D_{V}]$**: reduction in death risk from given immunity-inducing event.  
- **$\boldsymbol{M}^{(\ell)} = \boldsymbol{M}^{(\ell)}(t) = [\boldsymbol{M}^{(\ell)}(t), \boldsymbol{M}^{(\ell)}_{V}(t)]$**: location $\ell \in \mathcal L$ population-level immunity.  

To simplify notation, we have the following terms that characterize the effect of population-level immunities for a given subpopulation $\ell$, age $a$, and risk $r$:

\begin{align*}
\LambdaIlocagerisktime &= \left[\frac{\boldsymbol{K_{a,r}^{I}}}{\boldsymbol{1}_{\lvert \mathcal L \rvert \times 1} - \boldsymbol{K_{a,r}^{I}}}\right]^T \boldsymbol{M_{a,r}^{(\ell)}(t)} \\
\LambdaHlocagerisktime &= \left[\frac{\boldsymbol{K_{a,r}^{H}}}{\boldsymbol{1}_{\lvert \mathcal L \rvert \times 1} - \boldsymbol{K_{a,r}^{H}}}\right]^T \boldsymbol{M_{a,r}^{(\ell)}(t)} \\
\LambdaDlocagerisktime &= \left[\frac{\boldsymbol{K_{a,r}^{D}}}{\boldsymbol{1}_{\lvert \mathcal L \rvert \times 1} - \boldsymbol{K_{a,r}^{D}}}\right]^T \boldsymbol{M_{a,r}^{(\ell)}(t)}
\end{align*}

where $\boldsymbol{1}_{\lvert \mathcal L \rvert \times 1}$ is an $\lvert \mathcal L \rvert \times 1$ vector of $1$'s and the fraction notation indicates element-wise division. 

For each $\ell \in \mathcal L$, $a \in \agegroups$, $r \in \riskgroups$, we have the following equations that characterize transitions between compartments:

\begin{align}
\frac{dS\locagerisktime}{dt} &= \underbrace{\rateRtoS R\locagerisktime}_{\text{$R$ to $S$}} 
-\underbrace{S\locagerisktime \frac{\beta^{(\ell)}(t) \cdot \totalforceofinfection}{\left(1 + \LambdaIlocagerisktime\right)}}_{\text{$S$ to $E$}} \\[1.5em]
\frac{dE\locagerisktime}{dt} &= \underbrace{S\locagerisktime \frac{\beta^{(\ell)}(t) \cdot \totalforceofinfection}{\left(1 + \LambdaIlocagerisktime\right)}}_{\text{$S$ to $E$}} - \underbrace{\rateEtoI (1-\propIA) E\locagerisktime}_{\text{$E$ to $IP$}} - \underbrace{\rateEtoI \propIA E\locagerisktime}_{\text{$E$ to $IA$}} \\[1.5em]
\frac{dIP\locagerisktime}{dt} &= \underbrace{\rateEtoI (1-\propIA) E\locagerisktime}_{\text{$E$ to $IP$}} - \underbrace{\rateIPtoIS IP\locagerisktime}_{\text{$IP$ to $IS$}} \\[1.5em]
\frac{dIS\locagerisktime}{dt} &= \underbrace{\rateIPtoIS IP\locagerisktime}_{\text{$IP$ to $IS$}} 
- \underbrace{\left(1-\frac{\adjustedpropH_{a, r}}{1 + \LambdaHlocagerisktime}\right)\rateIStoR IS\locagerisktime}_{\text{$IS$ to $R$}} - \underbrace{\frac{\rateIStoH \adjustedpropH_{a, r} IS\locagerisktime}{1 + \LambdaHlocagerisktime}}_{\text{$IS$ to $H$}} \\[1.5em]
\frac{dIA\locagerisktime}{dt} &= \underbrace{\rateEtoI \propIA E\locagerisktime}_{\text{$E$ to $IA$}} 
- \underbrace{\rateIAtoR IA\locagerisktime}_{\text{$IA$ to $R$}} \\[1.5em]
\frac{dH\locagerisktime}{dt} &= \underbrace{\frac{\rateIStoH \adjustedpropH_{a, r} IS\locagerisktime}{1 + \LambdaHlocagerisktime}}_{\text{$IS$ to $H$}} - \underbrace{\left(1-\frac{\adjustedpropD_{a, r}}{1 + \LambdaDlocagerisktime}\right)\rateHtoR H\locagerisktime}_{\text{$H$ to $R$}} 
- \underbrace{\frac{\rateHtoD \adjustedpropD_{a, r} H\locagerisktime}{1 + \LambdaDlocagerisktime}}_{\text{$H$ to $D$}} \\[1.5em]
\frac{dR\locagerisktime}{dt} &= \underbrace{\left(1-\frac{\adjustedpropH_{a, r}}{1 + \LambdaHlocagerisktime}\right) \rateIStoR IS\locagerisktime}_{\text{$IS$ to $R$}} + \underbrace{\rateIAtoR IA\locagerisktime}_{\text{$IA$ to $R$}} 
\\[1em] &\quad\quad\quad + \underbrace{\left(1-\frac{\adjustedpropD_{a, r}}{1 + \LambdaDlocagerisktime}\right)\rateHtoR H\locagerisktime}_{\text{$H$ to $R$}} 
- \underbrace{\rateRtoS R\locagerisktime}_{\text{$R$ to $S$}} \\[1.5em]
\frac{dD\locagerisktime}{dt} &= \underbrace{\frac{\rateHtoD \adjustedpropD_{a, r} H\locagerisktime}{1 + \LambdaDlocagerisktime}}_{\text{$H$ to $D$}}. 
\end{align}


where

- The $\lambda$-terms are location/subpopulation mixing terms that we define in the next section on the travel model. 
- $\boldsymbol{N}^{(\ell)}$: $\numagegroups \times \numriskgroups$ matrix corresponding to total population in location $\ell \in \mathcal L$, where element $N\locagerisk$ is the total population of age group $a$ and risk group $\ell$ in location $\ell$.
- $\beta^{(\ell)}(t) = \beta^{(\ell)}_0 (1 + q(t))$: time-dependent transmission rate per day for individuals residing in location $\ell \in \mathcal L$. 
- $q(t)$: seasonality parameter based on absolute humidity, where $q(t)$ is a function of historical absolute humidity data times $\xi$, a humidity impact factor.
- $\propIA$: proportion exposed who are completely asymptomatic when infectious.
- $r_{IP}$, $r_{IA}$: relative infectiousness (compared to infected symptomatic people) of infected presymptomatic and infected asymptomatic people respectively. 
- $\rateIStoR, \rateHtoR, \rateIAtoR$: recovery rates for infected symptomatic ($IS$),  hospital ($H$), and infected asymptomatic ($IA$) compartments respectively, so that $1/\gamma$ is the average number of days it takes for an infected person not in the hospital to recover, and $1/\rateHtoR$ is analogous, but for an infected person in the hospital. 
- $\rateEtoI$: infection rate (both exposed to infected presymptomatic transition rate and exposed to infected asymptomatic transition rate), so that $1/\rateEtoI$ is the average number of days after exposure before a person becomes infectious.
- $\rateIPtoIS$: infected presymptomatic to infected symptomatic transition rate, so that $1/\rateIPtoIS$ is the average number of days that an infected person is presymptomatic before becoming symptomatic. 
- $\rateIStoH$: hospitalization rate (infected to hospital transition rate), so that $1/\rateIStoH$ is the average number of days a person is infected before going to the hospital.
- $\rateHtoD$: death rate from hospital, so that $1/\rateHtoD$ is the average number of days a person spends in the hospital before dying.
- $\boldsymbol{\adjustedpropH}$, where $\adjustedpropH_{a, r} = \frac{\propH_{a, r}\rateIStoR}{\rateIStoH - \propH_{a, r}(\rateIStoH-\rateIStoR)}$: adjusted proportion hospitalized based on age-risk group $a, r$ group actually used in model -- this adjustment is necessary to ensure actual proportion hospitalized recapitulates $[\propH_{a, r}]$.
- $\boldsymbol{\propH}$: $\numagegroups \times \numriskgroups$ proportion hospitalized based on age-risk group $a, r$.
- $\boldsymbol{\adjustedpropD}$, where $\adjustedpropD_{a, r} = \frac{\propD_{a, r} \rateHtoR}{\rateHtoD - \propD_{a, r} (\rateHtoD-\rateHtoR)}$: adjusted in-hospital mortality rate (as in, proportion who die in the hospital based on age group) actually used in model -- this adjustment is necessary to ensure actual proportion who die in the hospital recapitulates $[\propD_{a, r} ]$.
- $\boldsymbol{\propD}$: $\numagegroups \times \numriskgroups$ in-hospital mortality rate (proportion who die based on age-risk group $a, r$).

## Flu model: travel model

For each $\ell \in \mathcal L$, $k \in \mathcal L \setminus \{\ell\}$, $a \in \agegroups$, $r \in \riskgroups$, we have

\begin{align*}
\totalforceofinfection &= \lambda^{(\ell), \ell \rightarrow \ell}_{a, r}(t) + \sum_{k \in \mathcal L \setminus \{\ell\}} \left(\lambda^{(\ell), k \rightarrow \ell}_{a, r}(t) + \lambda^{(\ell), \ell \rightarrow k}_{a, r}(t)\right) \\[1.5em]
\lambda^{(\ell), \ell \rightarrow \ell}_{a, r}(t) &= \psi_a \left(1 - c_a \sum_{k \in \mathcal L \setminus \{\ell\}} \proptravelelltok \right) \cdot \sum \limits_{a^\prime \in \agegroups} \phi^{(\ell)}_{a,a^\prime}(t) \frac{\sum_{r^\prime \in \riskgroups} \left[IS\locageprimeriskprime(t) + r_{IP} IP\locageprimeriskprime(t) + r_{IA} IA\locageprimeriskprime(t)\right]}{\sum_{r^\prime \in \riskgroups} \effectiveNlocageprimeriskprimetime} \\[1.5em]
\lambda_{a,r}^{(\ell), k \rightarrow \ell}(t) &= \psi_a \cdot \multipliertravel \cdot \proptravelktoell \cdot \sum\limits_{a^\prime \in \agegroups} c_{a^\prime} \cdot \phi^{(\ell)}_{a, a^\prime}(t) \frac{\sum_{r^\prime \in \riskgroups}\left[\multipliersymptom IS\locationk\ageprimeriskprime(t) + r_{IP} IP\locationk\ageprimeriskprime(t) + r_{IA} IA\locationk\ageprimeriskprime(t)\right]}{\sum_{r^\prime \in \riskgroups} \effectiveNlocageprimeriskprimetime} \\[1.5em]
\lambda_{a,r}^{(\ell), \ell \rightarrow k}(t) &= \psi_a \cdot \multipliertravel \cdot \proptravelelltok  \cdot c_a \cdot \sum\limits_{a^\prime \in \agegroups} \phi^{(\ell)}_{a, a^\prime}(t) \frac{\sum_{r^\prime \in \riskgroups} \left[IS\locationk\ageprimeriskprime(t) + r_{IP} IP\locationk\ageprimeriskprime(t) + r_{IA} IA\locationk\ageprimeriskprime(t)\right]}{\sum_{r^\prime \in \riskgroups} \tilde{N}^{(k)}_{a^\prime, r^\prime} (t)}
\end{align*}

where

\begin{align*}
\effectiveNlocagerisktime &= \Nlocagerisk + c_a \cdot \sum_{k \in \mathcal L \setminus \{\ell\}} \proptravelktoell \cdot (N^{(k)}_{a, r} - (1-\multipliersymptom) IS^{(k)}_{a,r}(t) - H^{(k)}_{a,r}(t)) \\ &\quad\quad\quad - c_a \cdot \sum_{k \in \mathcal L \setminus \{\ell\}} \proptravelelltok  \cdot (N\locagerisk - (1-\multipliersymptom) IS\locagerisktime - H^{(\ell)}_{a,r}(t))
\end{align*}

is the effective population in location $\ell \in \mathcal L$ and age-risk group $a \in \agegroups$, $r \in \riskgroups$ at time $t$, and where

- $\phi_{a, a^\prime}^{(\ell)}(t)$: the number of contacts that individuals in age group $a \in \agegroups$ residing in location $\ell \in \mathcal L$ have with other individuals (regardless of location) in age group $a^\prime \in \agegroups$. 
- $\psi_a \in [0, 1]$: relative susceptibility of individuals in age group $a \in \agegroups$.
- $\proptravelktoell$: proportion of population in location $k \in \mathcal L$ that travels to location $\ell \in \mathcal L$.
- $c_a$: proportion of time individuals in age group $a \in \agegroups$ spend away from home region when traveling. 
- $\multipliertravel \in [0,1]$: multiplier to reduce contact rate of traveling individuals. 
- $\multipliersymptom \in [0,1]$: multiplier to reduce contact rate of symptomatic individuals.

Note that the decompositions model the following phenomenon:

- $\lambda^{(\ell), k \rightarrow \ell}\agerisktime$: rate at which individuals in location $\ell$ get exposed to infected people who live in location $k$ but travel to location $\ell$ (note: this contact occurs in location $\ell$).
- $\lambda^{(\ell), \ell \rightarrow k}\agerisktime$: rate at which individuals in location $\ell$ get exposed to infected people who live in location $k$, due to individuals who live in location $\ell$ traveling to location $k$ (note: this contact occurs in location $k$).

## Flu model: discretized stochastic implementation

To actually implement/simulate this compartmental model, we discretize the deterministic differential equations and treat transitions between compartments as stochastic to model uncertainty. We extend the notation from the deterministic differential equations to capture the stochastic elements.

Let **$\boldsymbol{\mathcal X}(t) = \left\{\boldsymbol{S}(t), \boldsymbol{E}(t), \boldsymbol{I}(t), \boldsymbol{H}(t), \boldsymbol{R}(t), \boldsymbol{D}(t), \boldsymbol{M}^I(t), \boldsymbol{M}^H(t), q(t), \boldsymbol{\phi}(t), V^{(\ell)}(t)\right\}$** be the "simulate state" at time $t$. **$\boldsymbol{\mathcal X}(t)$** is a set of matrices. 

Let **$\boldsymbol{\Theta} = \left\{\boldsymbol{O}, \boldsymbol{N}, \boldsymbol{g}^I, \boldsymbol{w}^I, \boldsymbol{g}^H, \boldsymbol{w^H}, \beta_0, \gamma, \rateHtoR, \rateEtoI, \boldsymbol{\propH}, \rateIStoH, \boldsymbol{\propD}, \rateHtoD, \rateRtoS \right\}$** be the set of fixed parameters. We define notation $\boldsymbol{g}^I = [g^I, g^I_{H3}, g^I_V]$,  $\boldsymbol{w}^I = [w^I, w^I_{H3}, w^I_V]$, $\boldsymbol{g}^H = [g^H, g^H_{H3}, g^H_V]$, and $\boldsymbol{w}^H = [w^H, w^H_{H3}, w^H_V]$. 

Then given initial state $\boldsymbol{\mathcal X}_0 = \boldsymbol{\mathcal X}(0)$, we can formulate our discretized stochastic implementation as

$$
\boldsymbol{\mathcal X}(t + \Delta t) = \boldsymbol{\mathcal X}(t) + f\left(\boldsymbol{\mathcal X}(t), \Delta t, \omega; \boldsymbol{\Theta}\right) \quad \text{for} \quad t \ge 0,
$$

where $f$ is parametrized by $\boldsymbol{\Theta}$, and depends on the step size of discretization $\Delta t$ and a sample path $\omega$. We assume that each sample path $\omega$ is realized from a random process that does not depend on $\boldsymbol{\mathcal X}(t)$ or $\Delta t$ for each $t$. When we are discussing a single model with a fixed set of parameters $\boldsymbol{\Theta}$, we drop the $\boldsymbol{\Theta}$ notation for simplicity.  

Now we formulate how we implement discretized stochastic transitions. We assume that $q(t)$, **$\boldsymbol{\phi}(t)$**, and $V^{(\ell)}(t)$  are updated deterministically according to some "schedule."  

We model stochastic transitions between compartments using "transition variables." Transition variables correspond to incoming and outgoing flows of epidemiological compartments (see the compartment equations above). 

Below we formulate the discretized stochastic transitions. Note that the population-level immunity variables behave as aggregate epidemiological metrics. They are deterministic functions of the simulation state and transitions between compartments.

> **IMPORTANT NOTE_: all $y$ and $y^*$-variables depend on $\left(\boldsymbol{\mathcal X}(t), \Delta t, \omega\right)$. For notation simplicity, we define $\simstate := \left(\boldsymbol{\mathcal X}(t), \Delta t, \omega\right)$ and write $y$ and $y^*$-variables as functions of $\simstate$.**

For each $\ell \in \mathcal L$, $a \in \agegroups$, and $r \in \riskgroups$:

\begin{align}
M_{a, r}^{(\ell)}(t + \Delta t) &= M_{a, r, H1}^{(\ell), I}(t) + \frac{dM\agerisktime^{(\ell)}}{dt} \cdot \Delta t \\
MV_{a,r}^{(\ell)}(t + \Delta t) &= M_{a,r,V}^{(\ell), I}(t) + \frac{dMV\agerisktime^{(\ell)}}{dt} \cdot \Delta t \\
S\locagerisk(t + \Delta t) &= S_{a, r}^{(\ell)}(t) + \underbrace{\tvarloc_{R\rightarrow S, a, r}(\Xi_t)}_{\text{$R$ to $S$}} - \underbrace{\tvarloc_{S\rightarrow E, a, r}(\Xi_t)}_{\text{$S$ to $E$}} \\
E\locagerisk(t + \Delta t) &= E_{a, r}^{(\ell)}(t) + \underbrace{\tvarloc_{S\rightarrow E, a, r}(\Xi_t)}_{\text{$S$ to $E$}} - \underbrace{\jointtvarloc_{E\rightarrow IP, a, r}(\Xi_t)}_{\text{$E$ to $IP$}} - \underbrace{\jointtvarloc_{E\rightarrow IA, a, r}(\Xi_t)}_{\text{$E$ to $IA$}} \\
IP\locagerisk(t + \Delta t) &= IP\locagerisktime + \underbrace{\jointtvarloc_{E\rightarrow IP, a, r}(\Xi_t)}_{\text{$E$ to $IP$}} - \underbrace{\tvarloc_{IP \rightarrow IS, a, r}(\Xi_t)}_{\text{$IP$ to $IS$}} \\
IA_{a, r}^{(\ell)}(t + \Delta t) &= IA_{a, r}^{(\ell)}(t) + \underbrace{\jointtvarloc_{E\rightarrow IA, a, r}(\Xi_t)}_{\text{$E$ to $IA$}} - \underbrace{\tvarloc_{IA \rightarrow R, a, r}(\Xi_t)}_{\text{$IA$ to $R$}} \\
IS\locagerisk(t + \Delta t) &= IS_{a, r}^{(\ell)}(t) + \underbrace{\tvarloc_{E \rightarrow IS, a, r}(\Xi_t)}_{\text{$E$ to $IP$}} - \underbrace{\jointtvarloc_{IS \rightarrow R, a, r}(\Xi_t)}_{\text{$IS$ to $R$}} - \underbrace{\jointtvarloc_{IS \rightarrow H, a, r}(\Xi_t)}_{\text{$IS$ to $H$}} \\
H\locagerisk(t + \Delta t) &= H_{a, r}^{(\ell)}(t) + \underbrace{\jointtvarloc_{IS \rightarrow H, a, r}(\Xi_t)}_{\text{$IS$ to $H$}} - \underbrace{\jointtvarloc_{H\rightarrow R, a, r}(\Xi_t)}_{\text{$H$ to $R$}} - \underbrace{\jointtvarloc_{H\rightarrow D, a, r}(\Xi_t)}_{\text{$H$ to $D$}} \\
R\locagerisk(t + \Delta t) &= R_{a, r}^{(\ell)}(t) + \underbrace{\jointtvarloc_{IS \rightarrow R, a, r}(\Xi_t)}_{\text{$IS$ to $R$}} + \underbrace{\jointtvarloc_{H\rightarrow R, a, r}(\Xi_t)}_{\text{$H$ to $R$}} - \underbrace{\tvarloc_{R\rightarrow S, a, r}(\Xi_t)}_{\text{$R$ to $S$}} \\
D\locagerisk(t + \Delta t) &= D_{a, r}^{(\ell)}(t) + \underbrace{\tvarloc_{H\rightarrow D, a, r}(\Xi_t)}_{\text{$H$ to $D$}}. 
\end{align}


> **IMPORTANT NOTE: the "$*$" superscript indicates that the transition variable has a joint distribution with another transition variable. In general, if a compartment has more than one outgoing transition variable, these transition variables must be modeled jointly.** 

Consider the two transitions out of the infected symptomatic compartment, for example. Given that a patient is infected and symptomatic, exactly one outcome occurs: they recover (from home) or they go to the hospital. Since one and only one of these outcomes must occur, we must model these two transition variables jointly. Joint distribution derivation details are provided in the next section on transition types.

Each transition variable depends on a "base count" and a "rate" (which both depend on the current state of the system). This decomposition is displayed in the table below. Note that these transitions are for a given location $\ell \in \mathcal L$.

| Transition            | Transition variable                                                      | Base count               | Rate                                                                                                                                                                                           |
|-----------------------|-------------------------------------------------------------------------|--------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| $R$ to $S$            | $\tvarloc_{R \rightarrow S, a, r}(\simstate)$                         | $R_{a, r}^{(\ell)}(t)$    | $\rateRtoS$                                                                                                                                                                                           |
| $S$ to $E$            | $\tvarloc_{S \rightarrow E, a, r}(\simstate)$                         | $S_{a, r}^{(\ell)}(t)$    | $\frac{\beta(t) \totalforceofinfection}{1 + \LambdaIlocagerisktime}$ |
| $E$ to $IP$           | $\jointtvarloc_{E \rightarrow IP, a, r}(\simstate)$                      | $E_{a, r}^{(\ell)}(t)$    | $\rateEtoI (1 - \propIA)$                                                                                                                                                                                |
| $E$ to $IA$           | $\jointtvarloc_{E \rightarrow IA, a, r}(\simstate)$                      | $E_{a, r}^{(\ell)}(t)$    | $\rateEtoI \propIA$                                                                                                                                                                                     |
| $IP$ to $IS$          | $\tvarloc_{IP \rightarrow IS, a, r}(\simstate)$                       | $IP_{a, r}^{(\ell)}(t)$   | $\rateIPtoIS$                                                                                                                                                                                           |
| $IS$ to $R$           | $\jointtvarloc_{IS \rightarrow R, a, r}(\simstate)$                      | $IS_{a, r}^{(\ell)}(t)$   | $\left(1-\frac{\adjustedpropH_{a,r}}{1 + \LambdaHlocagerisktime}\right)\gamma^{IS\rightarrow R}$                                                                                                                                                                    |
| $IS$ to $H$           | $\jointtvarloc_{IS \rightarrow H, a, r}(\simstate)$                      | $IS_{a, r}^{(\ell)}(t)$   | $\frac{\rateIStoH \adjustedpropH_{a,r} IS\locagerisktime}{1 + \LambdaHlocagerisktime}$                                                                                                            |
| $IA$ to $R$           | $\tvarloc_{IA \rightarrow R, a, r}(\simstate)$                        | $IA_{a, r}^{(\ell)}(t)$   | $\rateIAtoR IA\locagerisktime$                                                                                                                                                              |
| $H$ to $R$            | $\jointtvarloc_{H \rightarrow R, a, r}(\simstate)$                       | $H_{a, r}^{(\ell)}(t)$    | $\left(1-\frac{\adjustedpropD_{a,r}}{1 + \LambdaDlocagerisktime}\right)\rateHtoR$                                                                                                                                                                 |
| $H$ to $D$            | $\jointtvarloc_{H \rightarrow D, a, r}(\simstate)$                       | $H_{a, r}^{(\ell)}(t)$    | $\frac{\rateHtoD \adjustedpropD_{a,r} H\locagerisktime}{1 + \LambdaHlocagerisktime}$                                                                                                              |


The base count and rate of a transition variable parameterize the distribution that defines its realization. 

See [this page](math_transitions.md) for mathematical formulations of marginal and joint stochastic transitions between compartments. 

## General model: discretized stochastic implementation

We make the important note that the flu model's discretized stochastic implementation can be generalized to models with different structures. More broadly, we let $\boldsymbol{\mathcal C}(t)$ be a model's set of epidemiological compartments, $\boldsymbol{\mathcal M}(t)$ its set of aggregate epidemiological metrics, and $\boldsymbol{S (t)}$ its set of schedule-dependent (time-dependent) deterministic values. Then the above formulation still holds.

In fact, in our code, we model $\boldsymbol{\mathcal C(t)}$ using an `Compartment` class, $\boldsymbol{\mathcal M}(t)$ using an `EpiMetric` class, and $\boldsymbol{\mathcal S(t)}$ using a `Schedule` class. We handle stochastic transitions using `TransitionVariable` and `TransitionVariableGroup` classes. These classes form some of the building blocks of the base model code. 