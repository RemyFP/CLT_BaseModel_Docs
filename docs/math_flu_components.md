# Flu Model Mathematical Formulation

> **_Written by LP, updated 01/07/2025 (work in progress)_** 

> **_Important note: we are updating this page to include wastewater viral load and a metapopulation travel model -- check back soon._**

## Flu model: diagram

![flu_model_diagram](figs/flu_model_diagram.png)

## Flu model: deterministic differential equations
- $t \in \mathbb N$: current simulation day
- $\ell$: location, i.e. subpopulation, $\mathcal L$: set of all locations/subpopulations
- $a$: age group, $\mathcal A$: set of all age groups
- $r$: risk group, $\mathcal R$: set of all risk groups
- $i$: type of immunity-inducing event, $\mathcal{I} := \left\{\text{H1}, \text{H3}, \text{V}\right\}$: the set of all types of immunity-inducing events: infection by H1N1, infection by H3N2, and vaccination, respectively.
- $\boldsymbol{O}$: $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert \times \lvert \mathcal{I} \rvert$ matrix, where the $(a, r, i)$th element is the positive constant modeling the saturation of antibody production in individuals in age group $a$ and risk group $r$ who had immunity-inducing event $i$.

**Population-level immunity against _infection_ (derived from H1N1 infections, H3N2 infections, and vaccinations respectively)**

For each $\ell \in \mathcal L$, $a \in \mathcal A$, $r \in \mathcal R$:

\begin{align}
\frac{dM^{(\ell), I}_{a, r, H1}(t)}{dt} &= \frac{g^I_{H1} p_{H1}(t) \eta(t) R^{(\ell)}_{a, r}(t)}{N^{(\ell)}_{a, r} \left(1 + \sum_{i \in \mathcal I} O_{a, r, i} M^{(\ell), I}_{a, r, i}(t)\right)} - w^I_{H1} M^{(\ell), I}_{a, r, H1}(t) \\
\frac{dM^{(\ell), I}_{a, r, H3}(t)}{dt} &= \frac{g^I_{H3} p_{H3}(t) \eta(t) R^{(\ell)}_{a, r}(t)}{N^{(\ell)}_{a, r} \left(1 + \sum_{i \in \mathcal I} O_{a, r, i} M^{(\ell), I}_{a, r, i}(t)\right)} - w^I_{H3} M^{(\ell), I}_{a, r, H3}(t) \\
\frac{dM^{(\ell), I}_{a, r, V}(t)}{dt} &= g^I_V V(t - \delta) - w^I_V M^{(\ell), I}_{a, r, V}(t).
\end{align}

where

- $\eta$: rate at which recovered individuals become susceptible, so that $1/\eta$ is the average number of days a person is totally immune from reinfection until being susceptible again.
- $\boldsymbol{N}^{(\ell)}$: $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ matrix corresponding to total population in location $\ell \in \mathcal L$, where element $N^{(\ell)}_{a, r}$ is the total population of age group $a$ and risk group $\ell$ in location $\ell$.
- $V^{(\ell)}(t)$: number of vaccine doses administered at time $t$ to individuals residing in location $\ell \in \mathcal L$.
- $\boldsymbol{p} = \boldsymbol{p}(t) = [p_{H1}(t)$, $p_{H3}(t)]$: where elements correspond to prevalence of H1N1, H3N2 respectively.
- $\delta$: number of days after dose for vaccine to become effective.
- $g^I_{H1}$: factor by which population-level immunity against infection grows after each H1N1 case that recovers.
- $g^I_{H3}$: factor by which population-level immunity against infection grows after each H3N2 case that recovers.
- $g^I_V$: factor by which population-level immunity against infection grows after each vaccination.
- $w^I_{H1}$: rate at which H1N1 infection-induced immunity against infection wanes.
- $w^I_{H3}$: rate at which H3N2 infection-induced immunity against infection wanes.
- $w^I_V$: rate at which vaccine-induced immunity against infection wanes.

**Population-level immunity against _hospitalization_ (derived from H1N1 infections, H3N2 infections, and vaccinations respectively)**

For each $\ell \in \mathcal L$, $a \in \mathcal A$, $r \in \mathcal R$:

\begin{align}
\frac{dM^{(\ell), H}_{a, r, H1}(t)}{dt} &= \frac{g^H_{H1} p_{H1}(t) \eta(t) R^{(\ell)}_{a, r}(t)}{N^{(\ell)}_{a, r} \left(1 + \sum_{i \in \mathcal I} O_{a, r, i} M^{(\ell), H}_{a, r, i}(t)\right)} - w^H_{H1} M^{(\ell), H}_{a, r, H1}(t) \\
\frac{dM^{(\ell), H}_{a, r, H3}(t)}{dt} &= \frac{g^H_{H3} p_{H3}(t) \eta(t) R^{(\ell)}_{a, r}(t)}{N^{(\ell)}_{a, r} \left(1 + \sum_{i \in \mathcal I} O_{a, r, i} M^{(\ell), H}_{a, r, i}(t)\right)} - w^H_{H3} M^{(\ell), H}_{a, r, H3}(t) \\
\frac{dM^{(\ell), H}_{a, r, V}(t)}{dt} &= g^H_V V(t - \delta) - w^H_V M^{(\ell), H}_{a, r, V}(t).
\end{align}

where

- $\eta$, $\boldsymbol{N}^{(\ell)}$, $p_{H1}(t)$, $p_{H3}(t)$, $V^{(\ell)}(t)$, $\delta$: see above.
- $g^H_{H1}$: factor by which population-level immunity against hospitalization grows after each H1N1 case that recovers.
- $g^H_{H3}$: factor by which population-level immunity against hospitalization grows after each H3N2 case that recovers.
- $g^H_V$: factor by which population-level immunity against hospitalization grows after each vaccination.
- $w^H_{H1}$: rate at which H1N1 infection-induced immunity against hospitalization wanes.
- $w^H_{H3}$: rate at which H3N2 infection-induced immunity against hospitalization wanes.
- $w^H_V$: rate at which vaccine-induced immunity against hospitalization wanes.

**Compartment equations**

Note: the $\lambda$-terms are location/subpopulation mixing terms that we define in the next section on the travel model. 

For each $\ell \in \mathcal L$, $a \in \mathcal A$, $r \in \mathcal R$:

\begin{align}
\frac{dS^{(\ell)}_{a, r}(t)}{dt} &= \underbrace{\eta R^{(\ell)}_{a, r}(t)}_{\text{$R$ to $S$}} 
-\underbrace{S^{(\ell)}_{a, r}(t) \lambda^{(\ell)}_{a, r}(t)}_{\text{$S$ to $E$}} \\[1em]
\frac{dE^{(\ell)}_{a, r}(t)}{dt} &= \underbrace{S^{(\ell)}_{a, r}(t) \lambda^{(\ell)}_{a, r}(t)}_{\text{$S$ to $E$}} 
- \underbrace{\sigma (1-\tau) E^{(\ell)}_{a, r}(t)}_{\text{$E$ to $IP$}} 
- \underbrace{\sigma \tau E^{(\ell)}_{a, r}(t)}_{\text{$E$ to $IA$}} \\[1em]
\frac{dIP^{(\ell)}_{a, r}(t)}{dt} &= \underbrace{\sigma (1-\tau) E^{(\ell)}_{a, r}(t)}_{\text{$E$ to $IP$}} 
- \underbrace{\rho IP^{(\ell)}_{a, r}(t)}_{\text{$IP$ to $IS$}} \\[1em]
\frac{dIS^{(\ell)}_{a, r}(t)}{dt} &= \underbrace{\rho IP^{(\ell)}_{a, r}(t)}_{\text{$IP$ to $IS$}} 
- \underbrace{(1-\tilde{\mu}_{a, r})\gamma IS^{(\ell)}_{a, r}(t)}_{\text{$IS$ to $R$}} 
- \underbrace{\frac{\zeta \tilde{\mu}_{a, r} IS^{(\ell)}_{a, r}(t)}{1 + \Lambda^{(\ell), H, H}_{a, r}(t)}}_{\text{$IS$ to $H$}} \\[1em]
\frac{dIA^{(\ell)}_{a, r}(t)}{dt} &= \underbrace{\sigma \tau E^{(\ell)}_{a, r}(t)}_{\text{$E$ to $IA$}} 
- \underbrace{\gamma_{IA} IA^{(\ell)}_{a, r}(t)}_{\text{$IA$ to $R$}} \\[1em]
\frac{dH^{(\ell)}_{a, r}(t)}{dt} &= \underbrace{\frac{\zeta \tilde{\mu}_{a, r} IS^{(\ell)}_{a, r}(t)}{1 + \Lambda^{(\ell), H, H}_{a, r}(t)}}_{\text{$IS$ to $H$}} 
- \underbrace{(1-\tilde{\nu}_{a, r})\gamma_H H^{(\ell)}_{a, r}(t)}_{\text{$H$ to $R$}} 
- \underbrace{\frac{\pi \tilde{\nu}_{a, r} H^{(\ell)}_{a, r}(t)}{1 + \Lambda^{(\ell), H, H}_{a, r}(t)}}_{\text{$H$ to $D$}} \\[1em]
\frac{dR^{(\ell)}_{a, r}(t)}{dt} &= \underbrace{(1-\tilde{\mu}_{a, r}) \gamma IS^{(\ell)}_{a, r}(t)}_{\text{$IS$ to $R$}} 
+ \underbrace{\gamma_{IA} IA^{(\ell)}_{a, r}(t)}_{\text{$IA$ to $R$}} 
+ \underbrace{(1-\tilde{\nu}_{a, r})\gamma_H H^{(\ell)}_{a, r}(t)}_{\text{$H$ to $R$}} 
- \underbrace{\eta R^{(\ell)}_{a, r}(t)}_{\text{$R$ to $S$}} \\[1em]
\frac{dD^{(\ell)}_{a, r}(t)}{dt} &= \underbrace{\frac{\pi \tilde{\nu}_{a, r} H^{(\ell)}_{a, r}(t)}{1 + \Lambda^{(\ell), D, H}_{a, r}(t)}}_{\text{$H$ to $D$}}. 
\end{align}


We have the following terms that characterize the effect of population-level immunities:

\begin{align}
\Lambda^{(\ell), I, I}_{a,r}(t) &= \left[K_{a,r}^{I}(p(t))\right]^T M_{a,r}^{(\ell), I}(t) \\
\Lambda^{(\ell), H, H}_{a,r}(t) &= \left[K_{a,r}^{H}(p(t))\right]^T M_{a,r}^{(\ell), H}(t) \\
\Lambda^{(\ell), D, H}_{a,r}(t) &= \left[K_{a,r}^{D}(p(t))\right]^T M_{a,r}^{(\ell), H}(t)
\end{align}

and where

- $\tau$: proportion exposed who are completely asymptomatic when infectious.
- $r_{IP}$, $r_{IA}$: relative infectiousness (compared to infected symptomatic people) of infected presymptomatic and infected asymptomatic people respectively. 
- $\gamma, \gamma_H, \gamma_{IA}$: recovery rates for infected symptomatic ($IS$),  hospital ($H$), and infected asymptomatic ($IA$) compartments respectively, so that $1/\gamma$ is the average number of days it takes for an infected person not in the hospital to recover, and $1/\gamma_H$ is analogous, but for an infected person in the hospital. 
- $\sigma$: infection rate (both exposed to infected presymptomatic transition rate and exposed to infected asymptomatic transition rate), so that $1/\sigma$ is the average number of days after exposure before a person becomes infectious.
- $\rho$: infected presymptomatic to infected symptomatic transition rate, so that $1/\rho$ is the average number of days that an infected person is presymptomatic before becoming symptomatic. 
- $\boldsymbol{\tilde{\mu}}$, where $\tilde{\mu}_{a, r} = \frac{\mu_{a, r}\gamma}{\zeta - \mu_{a, r}(\zeta-\gamma)}$: adjusted hospitalization rate (as in, proportion hospitalized based on age-risk group $a, r$ group) actually used in model -- this adjustment is necessary to ensure actual proportion hospitalized recapitulates $[\mu_{a, r}]$.
- $\boldsymbol{\mu}$: $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ hospitalization rate (proportion hospitalized based on age-risk group $a, r$).
- $\zeta$: hospitalization rate (infected to hospital transition rate), so that $1/\zeta$ is the average number of days a person is infected before going to the hospital.
- $\boldsymbol{\tilde{\nu}}$, where $\tilde{\nu}_{a, r} = \frac{\nu_{a, r}\gamma_H}{\pi - \nu_{a, r}(\zeta-\gamma_H)}$: adjusted in-hospital mortality rate (as in, proportion who die in the hospital based on age group) actually used in model -- this adjustment is necessary to ensure actual proportion who die in the hospital recapitulates $[\nu_{a, r}]$.
- $\boldsymbol{\nu}$: $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ in-hospital mortality rate (proportion who die based on age-risk group $a, r$).
- $\pi$: death rate from hospital, so that $1/\pi$ is the average number of days a person spends in the hospital before dying.

Note that the following are all $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert \times \lvert \mathcal I \rvert$ matrices:

- **$\boldsymbol{K}^I(\boldsymbol{p}) = [\boldsymbol{K}^I_{H1}(p_{H1}), \boldsymbol{K}^I_{H3}(p_{H3}), \boldsymbol{K}^I_{V}]$**: reduction in infection risk from given immunity-inducing event.  
- **$\boldsymbol{K}^{H}(\boldsymbol{p}) = [\boldsymbol{K}^H_{H1}(p_{H1}), \boldsymbol{K}^H_{H3}(p_{H3}), \boldsymbol{K}^H_{V}]$**: reduction in hospitalization risk from given immunity-inducing event.  
- **$\boldsymbol{K}^D(\boldsymbol{p}) = [\boldsymbol{K}^D_{H1}(p_{H1}), \boldsymbol{K^D}_{H3}(p_{H3}), \boldsymbol{K}^D_{V}]$**: reduction in death risk from given immunity-inducing event.  
- **$\boldsymbol{M}^{(\ell), I} = \boldsymbol{M}^{(\ell), I}(t) = [\boldsymbol{M}^{(\ell), I}_{H1}(t), \boldsymbol{M}^{(\ell), I}_{H3}(t), \boldsymbol{M}^{(\ell), I}_{V}(t)]$**: location $\ell \in \mathcal L$ population-level immunity from infection (induced by H1 infection, H3 infection, vaccination respectively).  
- **$\boldsymbol{M}^{(\ell), H} = \boldsymbol{M}^{(\ell), H}(t) = [\boldsymbol{M}^{(\ell), H}_{H1}(t), \boldsymbol{M}^{(\ell), H}_{H3}(t), \boldsymbol{M}^{(\ell), H}_{V}(t)]$**: location $\ell \in \mathcal L$ population-level immunity from hospitalization (induced by H1 infection, H3 infection, vaccination respectively).

Note that prevalence is time-dependent, but we use $\boldsymbol{p} = \boldsymbol{p}(t)$ for notation simplicity. 

## Flu model: travel model

For each $\ell \in \mathcal L$, $a \in \mathcal A$, $r \in \mathcal R$, we have

\begin{align*}
\lambda^{(\ell)}_{a, r}(t) &= \lambda^{\ell \rightarrow \ell}_{a, r}(t) + \sum_{k \in \mathcal L \setminus \{\ell\}} \left(\lambda^{k \rightarrow \ell}_{a, r}(t) + \lambda^{\ell \rightarrow k}_{a, r}(t)\right) \\
\lambda^{\ell \rightarrow \ell}_{a, r}(t) &= \frac{\beta^{(\ell)}(t) \cdot \psi_a}{\left(1 + \Lambda^{(\ell), I, I}_{a, r}(t)\right)} \left(1 - c_a \sum_{k \in \mathcal L \setminus \{\ell\}} v^{\ell \rightarrow k}\right) \\ &\quad\quad\quad \cdot \sum \limits_{a^\prime \in \mathcal A, r^\prime \in \mathcal R} \phi^{(\ell)}_{a,a^\prime}(t) \frac{\left[IS^{(\ell)}_{a^\prime, r^\prime}(t) + r_{IP} IP^{(\ell)}_{a^\prime, r^\prime}(t) + r_{IA} IA^{(\ell)}_{a^\prime, r^\prime}(t)\right]}{\tilde{N}^{(\ell)}_{a, r}(t)} \\[1em]
\lambda_{a,r}^{k \rightarrow \ell}(t) &= \frac{\beta^{(\ell)}(t) \cdot \psi_a \cdot h_{\text{travel}}}{\left(1 + \Lambda^{(\ell), I, I}_{a, r}(t)\right)} \cdot v^{k \rightarrow \ell} \\ &\quad\quad\quad \cdot \sum\limits_{a^\prime \in \mathcal A, r^\prime \in \mathcal R} c_{a^\prime} \cdot \phi^{(\ell)}_{a, a^\prime}(t) \frac{\left[h_{\text{symptom}} IS^{(\ell)}_{a^\prime, r^\prime}(t) + r_{IP} IP^{(\ell)}_{a^\prime, r^\prime}(t) + r_{IA} IA^{(\ell)}_{a^\prime, r^\prime}(t)\right]}{\tilde{N}^{(\ell)}_{a,r} (t)} \\[1em]
\lambda_{a,r}^{\ell \rightarrow k}(t) &= \frac{\beta^{(\ell)}(t) \cdot \psi_a \cdot h_{travel}}{{\left(1 + \Lambda^{(\ell), I, I}_{a, r}(t)\right)}} \cdot  v^{\ell \rightarrow k} \cdot c_a \\ &\quad\quad\quad \cdot \sum\limits_{a^\prime \in \mathcal A, r^\prime \in \mathcal R} \phi^{(\ell)}_{a, a^\prime}(t) \frac{\left[IS^{(\ell)}_{a^\prime, r^\prime}(t) + r_{IP} IP^{(\ell)}_{a^\prime, r^\prime}(t) + r_{IA} IA^{(\ell)}_{a^\prime, r^\prime}(t)\right]}{\tilde{N}^{(k)}_{a^\prime, r^\prime} (t)}
\end{align*}

where

\begin{align*}
\tilde{N}^{(\ell)}_{a, r}(t) &= N^{(\ell)}_{a,r} + c_a \cdot \sum_{k \in \mathcal L \setminus \{\ell\}} v^{k \rightarrow \ell} \cdot (N^{(k)}_{k, a} - (1-c_{\text{symptom}}) I^{(k)}_{a,r}(t) - H^{(k)}_{a,r}(t)) \\ &\quad\quad\quad - c_a \cdot \sum_{k \in \mathcal L \setminus \{\ell\}} v^{\ell \rightarrow k} \cdot (N^{(\ell)}_{a, r} - (1-c_{\text{symptom}}) I^{(\ell)}_{a, r}(t) - H^{(\ell)}_{a,r}(t))
\end{align*}

is the effective population in location $\ell \in \mathcal L$ and age-risk group $a \in \mathcal A$, $r \in \mathcal R$ at time $t$, and where

- $\beta^{(\ell)}(t) = \beta^{(\ell)}_0 (1 + q(t))$: time-dependent transmission rate per day for individuals residing in location $\ell \in \mathcal L$.
- $q(t)$: seasonality parameter based on absolute humidity, where $q(t)$ is a function of historical absolute humidity data times $\xi$, a humidity impact factor.
- $\phi_{a, a^\prime}^{(\ell)}(t)$: the number of contacts that individuals in age group $a \in \mathcal A$ residing in location $\ell \in \mathcal L$ have with other individuals (regardless of location) in age group $a^\prime \in \mathcal A$. 
- $\psi_a \in [0, 1]$: relative susceptibility of individuals in age group $a \in \mathcal A$.
- $\zeta^{k \rightarrow \ell}$: proportion of population in location $k \in \mathcal L$ that travels to location $\ell \in \mathcal L$.
- $c_a$: proportion of time individuals in age group $a \in \mathcal A$ spend away from home region when traveling. 
- $h_{\text{travel}} \in [0,1]$: multiplier to reduce contact rate of traveling individuals. 
- $h_{\text{symptom}} \in [0,1]$: multiplier to reduce contact rate of symptomatic individuals.

## Flu model: discretized stochastic implementation

To actually implement/simulate this compartmental model, we discretize the deterministic differential equations and treat transitions between compartments as stochastic to model uncertainty. We extend the notation from the deterministic differential equations to capture the stochastic elements.

Let **$\boldsymbol{\mathcal X}(t) = \left\{\boldsymbol{S}(t), \boldsymbol{E}(t), \boldsymbol{I}(t), \boldsymbol{H}(t), \boldsymbol{R}(t), \boldsymbol{D}(t), \boldsymbol{M}^I(t), \boldsymbol{M}^H(t), q(t), \boldsymbol{\phi}(t), \boldsymbol{p}(t), V^{(\ell)}(t)\right\}$** be the "simulate state" at time $t$. **$\boldsymbol{\mathcal X}(t)$** is a set of matrices. 

Let **$\boldsymbol{\Theta} = \left\{\boldsymbol{O}, \boldsymbol{N}, \boldsymbol{g}^I, \boldsymbol{w}^I, \boldsymbol{g}^H, \boldsymbol{w^H}, \beta_0, \gamma, \gamma_H, \sigma, \boldsymbol{\mu}, \zeta, \boldsymbol{\nu}, \pi, \eta \right\}$** be the set of fixed parameters. We define notation $\boldsymbol{g}^I = [g^I_{H1}, g^I_{H3}, g^I_V]$,  $\boldsymbol{w}^I = [w^I_{H1}, w^I_{H3}, w^I_V]$, $\boldsymbol{g}^H = [g^H_{H1}, g^H_{H3}, g^H_V]$, and $\boldsymbol{w}^H = [w^H_{H1}, w^H_{H3}, w^H_V]$. 

Then given initial state $\boldsymbol{\mathcal X}_0 = \boldsymbol{\mathcal X}(0)$, we can formulate our discretized stochastic implementation as

$$
\boldsymbol{\mathcal X}(t + \Delta t) = \boldsymbol{\mathcal X}(t) + f\left(\boldsymbol{\mathcal X}(t), \Delta t, \omega; \boldsymbol{\Theta}\right) \quad \text{for} \quad t \ge 0,
$$

where $f$ is parametrized by $\boldsymbol{\Theta}$, and depends on the step size of discretization $\Delta t$ and a sample path $\omega$. We assume that each sample path $\omega$ is realized from a random process that does not depend on $\boldsymbol{\mathcal X}(t)$ or $\Delta t$ for each $t$. When we are discussing a single model with a fixed set of parameters $\boldsymbol{\Theta}$, we drop the $\boldsymbol{\Theta}$ notation for simplicity.  

Now we formulate how we implement discretized stochastic transitions. We assume that $q(t)$, **$\boldsymbol{\phi}(t)$**, **$\boldsymbol{p}(t)$**, and $V^{(\ell)}(t)$  are updated deterministically according to some "schedule."  

We model stochastic transitions between compartments using "transition variables." Transition variables correspond to incoming and outgoing flows of epidemiological compartments (see the compartment equations above). 

Below we formulate the discretized stochastic transitions. For brevity, we omit the update formulas for $\boldsymbol{M}^H(t)$ since it is analogous to the discretized update for $\boldsymbol{M}^I(t)$. Note that the population-level immunity variables behave as aggregate epidemiological metrics. They are deterministic functions of the simulation state and transitions between compartments.

> **_IMPORTANT NOTE_: all $y$ and $y^*$-variables depend on $\left(\boldsymbol{\mathcal X}(t), \Delta t, \omega\right)$. For notation simplicity, we define $\boldsymbol{\Xi}_t := \left(\boldsymbol{\mathcal X}(t), \Delta t, \omega\right)$ and write $y$ and $y^*$-variables as functions of $\boldsymbol{\Xi}_t$._**

For each $\ell \in \mathcal L$, $a \in \mathcal A$, and $r \in \mathcal R$:

\begin{align}
M_{a, r, H1}^{(\ell), I}(t + \Delta t) &= M_{a, r, H1}^{(\ell), I}(t) + \left[\frac{g^I_{H1} p_{H1}(t) \cdot \overbrace{y^{(\ell)}{R\rightarrow S, a, r}(\Xi_t)}^{\text{$R$ to $S$}}}{N_{a,r} \left(1 + \sum_{i \in \mathcal I} O_{a,r, i} M^I_{a,r, i}(t)\right)} - w^I_{H1} M^I_{a,r,H1}(t)\right] \Delta t \\
M_{a, r, H3}^{(\ell), I}(t + \Delta t) &= M_{a, r, H3}^{(\ell), I}(t) + \left[\frac{g^I_{H3} p_{H3}(t) \cdot \overbrace{y^{(\ell)}{R\rightarrow S, a, r}(\Xi_t)}^{\text{$R$ to $S$}}}{N_{a,r} \left(1 + \sum_{i \in \mathcal I} O_{a,r, i} M^I_{a,r, i}(t)\right)} - w^I_{H3} M^I_{a,r,H3}(t)\right] \Delta t \\
M_{a,r,V}^{(\ell), I}(t + \Delta t) &= M_{a,r,V}^{(\ell), I}(t) + \left[g^I_V V(t - \delta) - w^I_V M^I_{a,r,V}(t)\right] \Delta t
\end{align}

$$
\vdots
$$

\begin{align}
S_{a,r}^{(\ell)}(t + \Delta t) &= S_{a, r}^{(\ell)}(t) + \underbrace{y^{(\ell)}_{R\rightarrow S, a, r}(\Xi_t)}_{\text{$R$ to $S$}} - \underbrace{y^{(\ell)}_{S\rightarrow E, a, r}(\Xi_t)}_{\text{$S$ to $E$}} \\
E_{a,r}^{(\ell)}(t + \Delta t) &= E_{a, r}^{(\ell)}(t) + \underbrace{y^{(\ell)}_{S\rightarrow E, a, r}(\Xi_t)}_{\text{$S$ to $E$}} - \underbrace{y^{(\ell)*}_{E\rightarrow IP, a, r}(\Xi_t)}_{\text{$E$ to $IP$}} - \underbrace{y^{(\ell)*}_{E\rightarrow IA, a, r}(\Xi_t)}_{\text{$E$ to $IA$}} \\
IP_{a,r}^{(\ell)}(t + \Delta t) &= IP_{a,r}^{(\ell)}(t) + \underbrace{y^{(\ell)*}_{E\rightarrow IP, a, r}(\Xi_t)}_{\text{$E$ to $IP$}} - \underbrace{y^{(\ell)}_{IP \rightarrow IS, a, r}(\Xi_t)}_{\text{$IP$ to $IS$}} \\
IA_{a, r}^{(\ell)}(t + \Delta t) &= IA_{a, r}^{(\ell)}(t) + \underbrace{y^{(\ell)*}_{E\rightarrow IA, a, r}(\Xi_t)}_{\text{$E$ to $IA$}} - \underbrace{y^{(\ell)}_{IA \rightarrow R, a, r}(\Xi_t)}_{\text{$IA$ to $R$}} \\
IS_{a,r}^{(\ell)}(t + \Delta t) &= IS_{a, r}^{(\ell)}(t) + \underbrace{y^{(\ell)}_{E \rightarrow IS, a, r}(\Xi_t)}_{\text{$E$ to $IP$}} - \underbrace{y^{(\ell)*}_{IS \rightarrow R, a, r}(\Xi_t)}_{\text{$IS$ to $R$}} - \underbrace{y^{(\ell)*}_{IS \rightarrow H, a, r}(\Xi_t)}_{\text{$IS$ to $H$}} \\
H_{a,r}^{(\ell)}(t + \Delta t) &= H_{a, r}^{(\ell)}(t) + \underbrace{y^{(\ell)*}_{IS \rightarrow H, a, r}(\Xi_t)}_{\text{$IS$ to $H$}} - \underbrace{y^{(\ell)*}_{H\rightarrow R, a, r}(\Xi_t)}_{\text{$H$ to $R$}} - \underbrace{y^{(\ell)*}_{H\rightarrow D, a, r}(\Xi_t)}_{\text{$H$ to $D$}} \\
R_{a,r}^{(\ell)}(t + \Delta t) &= R_{a, r}^{(\ell)}(t) + \underbrace{y^{(\ell)*}_{IS \rightarrow R, a, r}(\Xi_t)}_{\text{$IS$ to $R$}} + \underbrace{y^{(\ell)*}_{H\rightarrow R, a, r}(\Xi_t)}_{\text{$H$ to $R$}} - \underbrace{y^{(\ell)}_{R\rightarrow S, a, r}(\Xi_t)}_{\text{$R$ to $S$}} \\
D_{a,r}^{(\ell)}(t + \Delta t) &= D_{a, r}^{(\ell)}(t) + \underbrace{y^{(\ell)}_{H\rightarrow D, a, r}(\Xi_t)}_{\text{$H$ to $D$}}. 
\end{align}


> **_IMPORTANT NOTE: the "$*$" superscript indicates that the transition variable has a joint distribution with another transition variable. In general, if a compartment has more than one outgoing transition variable, these transition variables must be modeled jointly._** 

Consider the two transitions out of the infected symptomatic compartment, for example. Given that a patient is infected and symptomatic, exactly one outcome occurs: they recover (from home) or they go to the hospital. Since one and only one of these outcomes must occur, we must model these two transition variables jointly. Joint distribution derivation details are provided in the next section on transition types.

Each transition variable depends on a "base count" and a "rate" (which both depend on the current state of the system). This decomposition is displayed in the table below. Note that these transitions are for a given location $\ell \in \mathcal L$.

| Transition            | Transition variable                                                      | Base count               | Rate                                                                                                                                                                                           |
|-----------------------|-------------------------------------------------------------------------|--------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| $R$ to $S$            | $y_{R \rightarrow S, a, r}(\boldsymbol{\Xi}_t)$                         | $R_{a, r}^{(\ell)}(t)$    | $\eta$                                                                                                                                                                                           |
| $S$ to $E$            | $y_{S \rightarrow E, a, r}(\boldsymbol{\Xi}_t)$                         | $S_{a, r}^{(\ell)}(t)$    | $\lambda^{(\ell)}_{a, r}(t)$ |
| $E$ to $IP$           | $y^*_{E \rightarrow IP, a, r}(\boldsymbol{\Xi}_t)$                      | $E_{a, r}^{(\ell)}(t)$    | $\sigma (1 - \tau)$                                                                                                                                                                                |
| $E$ to $IA$           | $y^*_{E \rightarrow IA, a, r}(\boldsymbol{\Xi}_t)$                      | $E_{a, r}^{(\ell)}(t)$    | $\sigma \tau$                                                                                                                                                                                     |
| $IP$ to $IS$          | $y_{IP \rightarrow IS, a, r}(\boldsymbol{\Xi}_t)$                       | $IP_{a, r}^{(\ell)}(t)$   | $\rho$                                                                                                                                                                                           |
| $IS$ to $R$           | $y^*_{IS \rightarrow R, a, r}(\boldsymbol{\Xi}_t)$                      | $IS_{a, r}^{(\ell)}(t)$   | $(1-\tilde{\mu}_{a,r})\gamma$                                                                                                                                                                    |
| $IS$ to $H$           | $y^*_{IS \rightarrow H, a, r}(\boldsymbol{\Xi}_t)$                      | $IS_{a, r}^{(\ell)}(t)$   | $\frac{\zeta \tilde{\mu}_{a,r} IS_{a,r}^{(\ell)}(t)}{1 + \Lambda^{(\ell), H, H}_{a,r}(t)}$                                                                                                            |
| $IA$ to $R$           | $y_{IA \rightarrow R, a, r}(\boldsymbol{\Xi}_t)$                        | $IA_{a, r}^{(\ell)}(t)$   | $\gamma_{IA} IA_{a,r}^{(\ell)}(t)$                                                                                                                                                              |
| $H$ to $R$            | $y^*_{H \rightarrow R, a, r}(\boldsymbol{\Xi}_t)$                       | $H_{a, r}^{(\ell)}(t)$    | $(1-\tilde{\nu}_{a,r})\gamma_H$                                                                                                                                                                 |
| $H$ to $D$            | $y^*_{H \rightarrow D, a, r}(\boldsymbol{\Xi}_t)$                       | $H_{a, r}^{(\ell)}(t)$    | $\frac{\pi \tilde{\nu}_{a,r} H_{a,r}^{(\ell)}(t)}{1 + \Lambda^{(\ell), H, H}_{a,r}(t)}$                                                                                                              |


The base count and rate of a transition variable parameterize the distribution that defines its realization. 

See [this page](math_transitions.md) for mathematical formulations of marginal and joint stochastic transitions between compartments. 

## General model: discretized stochastic implementation

We make the important note that the flu model's discretized stochastic implementation can be generalized to models with different structures. More broadly, we let $\boldsymbol{\mathcal C}(t)$ be a model's set of epidemiological compartments, $\boldsymbol{\mathcal M}(t)$ its set of aggregate epidemiological metrics, and $\boldsymbol{S (t)}$ its set of schedule-dependent (time-dependent) deterministic values. Then the above formulation still holds.

In fact, in our code, we model $\boldsymbol{\mathcal C(t)}$ using an `Compartment` class, $\boldsymbol{\mathcal M}(t)$ using an `EpiMetric` class, and $\boldsymbol{\mathcal S(t)}$ using a `Schedule` class. We handle stochastic transitions using `TransitionVariable` and `TransitionVariableGroup` classes. These classes form some of the building blocks of the base model code. 