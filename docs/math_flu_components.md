# Flu Model Mathematical Form

> **_Updated 11/8/2024 (work in progress)_** 

## General mathematical structure: deterministic differential equations
- $t \in \mathbb N$: current simulation day
- $a$: age group, $A$: set of all age groups
- $\ell$: risk group, $L$: set of all risk groups
- $i$: type of immunity-inducing event, $\mathcal{I} := \left\{\text{H1}, \text{H3}, \text{V}\right\}$: the set of all types of immunity-inducing events: infection by H1N1, infection by H3N2, and vaccination, respectively.
- $\boldsymbol{O}$: $\lvert A \rvert \times \lvert L \rvert \times \lvert \mathcal{I} \rvert$ matrix, where the $(a, \ell, i)$th element is the positive constant modeling the saturation of antibody production in individuals in age group $a$ and risk group $\ell$ who had immunity-inducing event $i$.

**Population-level immunity against _infection_ (derived from H1N1 infections, H3N2 infections, and vaccinations respectively)**

$$
\frac{dM^I_{a,\ell,H1}(t)}{dt} = \frac{g^I_{H1} p_{H1}(t) \eta(t) R_{a,\ell}(t)}{N_{a,\ell} \left(1 + \sum_{i \in \mathcal I} O_{a,\ell, i} M^I_{a,\ell, i}(t)\right)} - w^I_{H1} M^I_{a,\ell,H1}(t)
$$

$$
\frac{dM^I_{a,\ell,H3}(t)}{dt} = \frac{g^I_{H3} p_{H3}(t) \eta(t) R_{a,\ell}(t)}{N_{a,\ell} \left(1 + \sum_{i \in \mathcal I} O_{a,\ell, i} M^I_{a,\ell, i}(t)\right)} - w^I_{H3} M^I_{a,\ell,H3}(t)
$$

$$
\frac{dM^I_{a,\ell,V}(t)}{dt} = g^I_V V(t - \delta) - w^I_V M^I_{a,\ell,V}(t)
$$

where

- $g^I_{H1}$: factor by which population-level immunity against infection grows after each H1N1 case that recovers.
- $g^I_{H3}$: factor by which population-level immunity against infection grows after each H3N2 case that recovers.
- $g^I_V$: factor by which population-level immunity against infection grows after each vaccination.
- $N_{a, \ell}$: total population of age group $a$ and risk group $\ell$.
- $p_{H1}(t)$, $p_{H3}(t)$: prevalence of H1N1, H3N2 respectively.
- $w^I_{H1}$: rate at which H1N1 infection-induced immunity against infection wanes.
- $w^I_{H3}$: rate at which H3N2 infection-induced immunity against infection wanes.
- $w^I_V$: rate at which vaccine-induced immunity against infection wanes.
- $V(t)$: number of vaccine doses administered at time $t$.
- $\delta$: number of days after dose for vaccine to become effective.

**Population-level immunity against _hospitalization_ (derived from H1N1 infections, H3N2 infections, and vaccinations respectively)**

$$
\frac{dM^H_{a,\ell,H1}(t)}{dt} = \frac{g^H_{H1} p_{H1}(t) \eta(t) R_{a,\ell}(t)}{N_{a,\ell} \left(1 + \sum_{i \in \mathcal{I}} O_{a,\ell, i} M^H_{a,\ell, i}(t)\right)} - w^H_{H1} M^H_{a,\ell,H1}(t)
$$

$$
\frac{dM^H_{a,\ell,H3}(t)}{dt} = \frac{g^H_{H3} p_{H3}(t) \eta(t) R_{a,\ell}(t)}{N_{a,\ell} \left(1 + \sum_{i \in \mathcal{I}} O_{a,\ell, i} M^H_{a,\ell, i}(t)\right)} - w^H_{H3} M^H_{a,\ell,H3}(t)
$$

$$
\frac{dM^H_{a,\ell,V}(t)}{dt} = g^H_V V(t) - w^H_V M^H_{a,\ell,V}(t)
$$

where

- $p_{H1}(t)$, $p_{H3}(t)$, $V(t)$, $\delta$: see above.
- $g^H_{H1}$: factor by which population-level immunity against hospitalization grows after each H1N1 case that recovers.
- $g^H_{H3}$: factor by which population-level immunity against hospitalization grows after each H3N2 case that recovers.
- $g^H_V$: factor by which population-level immunity against hospitalization grows after each vaccination.
- $w^H_{H1}$: rate at which H1N1 infection-induced immunity against hospitalization wanes.
- $w^H_{H3}$: rate at which H3N2 infection-induced immunity against hospitalization wanes.
- $w^H_V$: rate at which vaccine-induced immunity against hospitalization wanes.

**SEIHRD equations**

$$
\frac{dS_{a,\ell}(t)}{dt} = -\underbrace{S_{a,\ell}(t) \cdot \sum_{a^\prime \in A, \ell^\prime \in L} \frac{\beta(t) \phi_{a, \ell, a^\prime, \ell^\prime}(t) I_{a^\prime, \ell^\prime}(t)}{N_{a^\prime, \ell^\prime} (1 + \boldsymbol{K_{a,\ell}^I(p)}^T \boldsymbol{M_{a,\ell}^I})}}_{\text{new exposed}} + \underbrace{\eta R_{a,\ell}(t)}_{\text{new susceptible}}
$$

$$
\frac{dE_{a,\ell}(t)}{dt} = \underbrace{S_{a,\ell}(t) \cdot \sum_{a^\prime \in A, \ell^\prime \in L} \frac{\beta(t) \phi_{a, \ell, a^\prime, \ell^\prime}(t) I_{a^\prime, \ell^\prime}(t)}{N_{a^\prime, \ell^\prime} (1 + \boldsymbol{K_{a,\ell}^I(p)}^T \boldsymbol{M_{a,\ell}^I})}}_{\text{new exposed}} - \underbrace{\sigma E_{a,\ell}(t)}_{\text{new infected}}
$$

$$
\frac{dI_{a,\ell}(t)}{dt} = \underbrace{\sigma E_{a,\ell}(t)}_{\text{new infected}} - \underbrace{(1-\tilde{\mu}_{a,\ell})\gamma I_{a,\ell}(t)}_{\text{new recovered from home}} - \underbrace{\frac{\zeta \tilde{\mu}_{a,\ell} I_{a,\ell}(t)}{1 + \boldsymbol{K_{a,\ell}^H(p)}^T \boldsymbol{M_{a,\ell}^H}}}_{\text{new hospitalized}}
$$

$$
\frac{dH_{a,\ell}(t)}{dt} = \underbrace{\frac{\zeta \tilde{\mu}_{a,\ell} I_{a,\ell}(t)}{1 + \boldsymbol{K_{a,\ell}^H(p)}^T \boldsymbol{M_{a,\ell}^H}}}_{\text{new hospitalized}} - \underbrace{(1-\tilde{\nu}_{a,\ell})\gamma_H H_{a,\ell}(t)}_{\text{new recovered from hospital}} - \underbrace{\frac{\pi \tilde{\nu}_{a,\ell} H_{a,\ell}(t)}{1 + \boldsymbol{K_{a,\ell}^D(p)}^T\boldsymbol{M_{a,\ell}^H}}}_{\text{new dead}}
$$

$$
\frac{dR_{a,\ell}(t)}{dt} = \underbrace{(1-\tilde{\mu}_{a,\ell}) \gamma I_{a,\ell}(t)}_{\text{new recovered from home}} + \underbrace{(1-\tilde{\nu}_{a,\ell})\gamma_H H_{a,\ell}(t)}_{\text{new recovered from hospital}} - \underbrace{\eta R_{a,\ell}(t)}_{\text{new susceptible}}
$$

$$
\frac{dD_{a,\ell}(t)}{dt} = \underbrace{\frac{\pi \tilde{\nu}_{a,\ell} H_{a,\ell}(t)}{1 + \boldsymbol{K_{a,\ell}^D(p)}^T\boldsymbol{M_{a,\ell}^H}}}_{\text{new dead}}
$$


where

- $\beta(t) = \beta_0 (1 + q(t))$: time-dependent transmission rate per day.
- $q(t)$: seasonality parameter based on absolute humidity.
- $\phi_{a, \ell, a^\prime, \ell^\prime}(t)$: mixing rates between age-risk group $a, \ell$ and $a^\prime, \ell^\prime$. Specifically, the interpretation is: for an individual in age-risk group $a, \ell$, the number of contacts they have in age-risk group $a^\prime, ell^\prime$ per day.
- $\gamma, \gamma_H$: recovery rates for infected and hospital compartments respectively, so that $1/\gamma$ is the average number of days it takes for an infected person not in the hospital to recover, and $1/\gamma_H$ is analogous, but for an infected person in the hospital. 
- $\sigma$: infection rate (exposed to infected transition rate), so that $1/\sigma$ is the average number of days after exposure before a person becomes infectious.
- $[\tilde{\mu}_{a, \ell}]$, where $\tilde{\mu}_{a, \ell} = \frac{\mu_{a, \ell}\gamma}{\zeta - \mu_{a, \ell}(\zeta-\gamma)}$: adjusted hospitalization rate vector (as in, proportion hospitalized based on age-risk group $a, \ell$ group) actually used in model -- this adjustment is necessary to ensure actual proportion hospitalized recapitulates $[\mu_{a, \ell}]$.
- $[\mu_{a, \ell}]$: hospitalization rate vector (proportion hospitalized based on age-risk group $a, \ell$).
- $\zeta$: hospitalization rate (infected to hospital transition rate), so that $1/\zeta$ is the average number of days a person is infected before going to the hospital.
- $[\tilde{\nu}_{a, \ell}]$, where $\tilde{\nu}_{a, \ell} = \frac{\nu_{a, \ell}\gamma_H}{\pi - \nu_{a, \ell}(\zeta-\gamma_H)}$: adjusted hospitalization rate vector (as in, proportion hospitalized based on age group) actually used in model -- this adjustment is necessary to ensure actual proportion hospitalized recapitulates $[\nu_{a, \ell}]$.
- $[\nu_{a, \ell}]$: in-hospital mortality rate vector (proportion who die based on age-risk group $a, \ell$).
- $\pi$: death rate from hospital, so that $1/\pi$ is the average number of days a person spends in the hospital before dying.
- $\eta$: rate at which recovered individuals become susceptible, so that $1/\eta$ is the average number of days a person is totally immune from reinfection until being susceptible again.

The following are all $\lvert A \rvert \times \lvert L \rvert \times \lvert \mathcal I \rvert$ matrices:

- **$\boldsymbol{K^I(p)} = [\boldsymbol{K^I}_{H1}(p_{H1}), \boldsymbol{K^I}_{H3}(p_{H3}), \boldsymbol{K^I}_{V}]$**: reduction in infection risk from given immunity-inducing event.  
- **$\boldsymbol{K^H(p)} = [\boldsymbol{K^H}_{H1}(p_{H1}), \boldsymbol{K^H}_{H3}(p_{H3}), \boldsymbol{K^H}_{V}]$**: reduction in hospitalization risk from given immunity-inducing event.  
- **$\boldsymbol{K^D(p)} = [\boldsymbol{K^D}_{H1}(p_{H1}), \boldsymbol{K^D}_{H3}(p_{H3}), \boldsymbol{K^D}_{V}]$**: reduction in death risk from given immunity-inducing event.  
- **$\boldsymbol{M^I} = \boldsymbol{M^I}(t) = [\boldsymbol{M^I}_{H1}(t), \boldsymbol{M^I}_{H3}(t), \boldsymbol{M^I}_{V}(t)]$**: population-level immunity from infection (induced by H1 infection, H3 infection, vaccination respectively).  
- **$\boldsymbol{M^H} = \boldsymbol{M^H}(t) = [\boldsymbol{M^H}_{H1}(t), \boldsymbol{M^H}_{H3}(t), \boldsymbol{M^H}_{V}(t)]$**: population-level immunity from hospitalization (induced by H1 infection, H3 infection, vaccination respectively).


