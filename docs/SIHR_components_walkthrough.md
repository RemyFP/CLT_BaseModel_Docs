# A Simple Custom SIHR Model: Components Walkthrough

> **_Written by LP, updated 02/27/2025 (work in progress)_** 

In this tutorial, we work through `SIHR_components.py` in the folder `SIHR_model` to demonstrate how to inherit from the `clt_base` package and create a customized model with a customized functional form. 

This code demonstrates how to inherit from clt_base to create a customized model for one subpopulation -- this is intended as an intermediate tutorial for code users (and as somewhat of an introductory tutorial to inheritance and abstract methods).

The S-I-H-R model we demonstrate has the following structure:

```python
#  S -> I -> H -> R
#       I ------> R
```

So people in the I (infected) compartment either move to H or R (go to the hospital or recover).

## Imports

- `numpy` is our standard array manipulation package.
- `sciris` is a nice package that StarSim uses -- we will use it for "object dictionaries" -- to access values in dictionaries using dot
  notation, which is very convenient.
- `dataclasses` provide favorable ways to store data.
- `typing` (specifically Optional) gives us the ability to allow optional arguments for instantiating dataclasses.
- And finally, don't forget to import the `clt_base` package -- we'll need this to create our model!

```python
import numpy as np
import sciris as sc
from pathlib import Path

from dataclasses import dataclass
from typing import Optional

import clt_base as clt
```

## Required Subclasses  
When creating a custom model, we need to define the following subclasses:  

- Exactly 1 subclass of `clt.SubpopParams` -- holds the model's fixed parameters.  
- Exactly 1 subclass of `clt.SubpopState` -- holds the model's simulation state.  
- Potentially multiple subclasses of `clt.TransitionVariable` -- one for each type of transition variable, managing transitions between epidemiological compartments.  
- Potentially 0 or multiple subclasses of `clt.StateVariable`, specifically:

    - `clt.EpiMetric`, `clt.DynamicVal`, and `clt.Schedule` for any epidemiological metrics, dynamic values, or schedules in the model.  
    - Note: We generally do **not** need subclasses of `clt.Compartment` unless we require special compartments with advanced functionality beyond what `clt.Compartment` already provides.  

- Exactly 1 subclass of `clt.SubpopModel` -- wraps everything together in the simulation model.  

To keep this S-I-H-R model and demo simple, we do not include epidemiological metrics, dynamic values, or schedules, so we will not need to create associated subclasses.  

## Step Zero: Planning the Model  

Before coding, we must:  

1. Write down the model structure and any mathematical formulas.  
2. Define the names (as strings) for all key elements in the model.  
3. Ensure our variable names in the code align with these planned names.  

### Compartments

The model consists of 4 compartments: 

- `"S"` (Susceptible)  
- `"I"` (Infected)  
- `"H"` (Hospitalized)  
- `"R"` (Recovered)  

We will create four `clt.Compartment` instances with these names.  

### Transition Variables 

We will define 4 transition variables, each as a `clt.TransitionVariable` subclass:  

- `"SusceptibleToInfected"` (for transitions from `"S"` to `"I"`)  
- `"InfectedToHospitalized"` (for transitions from `"I"` to `"H"`)  
- `"HospitalizedToRecovered"` (for transitions from `"H"` to `"R"`)  
- `"InfectedToRecovered"` (for transitions from `"I"` to `"R"`)  

### Fixed/Constant Parameters  

In addition to `"num_age_groups"` and `"num_risk_groups"`, our model includes the following parameters:  

- `"total_pop_age_risk"`  
- `"beta"`  
- `"I_to_H_rate"`  
- `"I_to_R_rate"`  
- `"H_to_R_rate"`  
- `"I_to_H_prop"`  

### Mathematical Formulas for Transitions  
The transition rates are defined as follows:  

- `"S"` to `"I"` transition rate:  

  $$ I \times \frac{\beta}{\text{total\_pop\_age\_risk}} $$
- `"I"` to `"H"` transition rate:  
  $$ \text{I\_to\_H\_rate} \times \text{I\_to\_H\_prop} $$
- `"I"` to `"R"` transition rate:  
  $$ \text{I\_to\_R\_rate} \times \text{(1 - I\_to\_H\_prop)} $$
- `"H"` to `"R"` transition rate:  
  $$ \text{H\_to\_R\_rate} $$

## Creating a `SubpopParams` Subclass

First, we create our `clt.SubpopParams` subclass.  

- We use the `@dataclass` decorator to enable convenient data storage functionality.  
- Each field must have a unique and descriptive name for the fixed parameters in our model.  
- Including `"num_age_groups"` and `"num_risk_groups"` is recommended to simplify computations, as many arrays in the model will have a shape of (`num_age_groups` × `num_risk_groups`).  
- Each field must have a specified data type.  
- The `Optional` type allows setting default values for parameters if they are not explicitly defined.  

For more details on `dataclasses`, refer to the official Python documentation.  

```python
@dataclass
class SIHRSubpopParams(clt.SubpopParams):
    """
    Data container for pre-specified and fixed epidemiological
    parameters in SIHR model.

    Each field of datatype np.ndarray must be A x L,
    where A is the number of age groups and L is the number of
    risk groups. Note: this means all arrays should be 2D.

    Attributes:
        num_age_groups (positive int):
            number of age groups.
        num_risk_groups (positive int):
            number of risk groups.
        total_pop_age_risk (np.ndarray of positive ints):
            total number in population, summed across all
            age-risk groups.
        beta (positive float): transmission rate.
        I_to_H_rate (positive float):
            rate at which people in I move to H --
            units of people per day.
        I_to_R_rate (positive float):
            rate at which people in I move to R --
            units of people per day.
        H_to_R_rate (positive float):
            rate at which people in H move to R --
            units of people per day.
        I_to_H_prop (np.ndarray):
            contains values in [0,1] corresponding to
            probability of going to hospital given
            infection, for a specific age-risk group
            (age is given by row index, risk is
            given by column index).
    """

    num_age_groups: Optional[int] = None
    num_risk_groups: Optional[int] = None
    total_pop_age_risk: Optional[np.ndarray] = None
    beta: Optional[float] = None
    I_to_H_rate: Optional[float] = None
    I_to_R_rate: Optional[float] = None
    H_to_R_rate: Optional[float] = None
    I_to_H_prop: Optional[np.ndarray] = None
```

## Creating a `SubpopState` subclass

Next, we create our `clt.SubpopState` subclass. We also need the `@dataclass` decorator here and we also need to specify the datatype of each field.

```python
@dataclass
class SIHRSubpopState(clt.SubpopState):
    """
    Data container for pre-specified and fixed set of
    Compartment initial values and EpiMetric initial values
    in SIHR model.

    Each field below should be A x L np.ndarray, where
    A is the number of age groups and L is the number of risk groups.
    Note: this means all arrays should be 2D. Even if there is
    1 age group and 1 risk group (no group stratification),
    each array should be 1x1, which is two-dimensional.
    For example, np.array([[100]]) is correct --
    np.array([100]) is wrong.

    Attributes:
        S (np.ndarray of positive floats):
            susceptible compartment for age-risk groups --
            (holds current_val of Compartment "S").
        I (np.ndarray of positive floats):
            infected for age-risk groups
            (holds current_val of Compartment "I").
        H (np.ndarray of positive floats):
            hospitalized compartment for age-risk groups
            (holds current_val of Compartment "H").
        R (np.ndarray of positive floats):
            recovered compartment for age-risk groups
            (holds current_val of Compartment "R").
    """

    S: Optional[np.ndarray] = None
    I: Optional[np.ndarray] = None
    H: Optional[np.ndarray] = None
    R: Optional[np.ndarray] = None
```

## Creating `TransitionVariable` Subclasses

For each transition variable, we create a subclass of `clt.TransitionVariable`.  

`clt.TransitionVariable` is an **abstract base class** and includes an **abstract method** `get_current_rate`, which we must implement in our subclass.  

### Implementing `get_current_rate`  

- The `get_current_rate` method takes in two arguments:  
  - `state`: An instance of `SubpopState` (specifically, `SIHRSubpopState`).  
  - `params`: An instance of `SubpopParams` (specifically, `SIHRSubpopParams`).  
- These arguments provide **easy access** to parameters and the simulation state.  

For each transition variable subclass, we must implement `get_current_rate` to return the **actual current rate**, which depends on the parameters and simulation state. The implementation follows the **mathematical formulas** we previously defined.  

```python
class SusceptibleToInfected(clt.TransitionVariable):

    def get_current_rate(self,
                         state: SIHRSubpopState,
                         params: SIHRSubpopParams) -> np.ndarray:

        return state.I * params.beta / params.total_pop_age_risk


class InfectedToHospitalized(clt.TransitionVariable):

    def get_current_rate(self,
                         state: SIHRSubpopState,
                         params: SIHRSubpopParams) -> np.ndarray:
        return params.I_to_H_rate * params.I_to_H_prop


class HospitalizedToRecovered(clt.TransitionVariable):

    def get_current_rate(self,
                         state: SIHRSubpopState,
                         params: SIHRSubpopParams) -> np.ndarray:
        return params.H_to_R_rate


class InfectedToRecovered(clt.TransitionVariable):

    def get_current_rate(self,
                         state: SIHRSubpopState,
                         params: SIHRSubpopParams) -> np.ndarray:

        return params.I_to_R_rate * (1 - params.I_to_H_prop)
```

## Creating a `SubpopModel`

Finally, we put everything together in a `SubpopModel` :)

We create a subclass of `clt.SubpopModel` -- here we named our subclass `SIHRSubpopModel.` We will go through each function step-by-step.

### Customizing the `__init__` Method in `clt.SubpopModel`  

If we examine the `clt.SubpopModel` base class, we see that its `__init__` function requires the following arguments:  

- `state`: A `SubpopState` instance  
- `params`: A `SubpopParams` instance  
- `config`: A `Config` instance  
- `RNG`: An `np.random.Generator` instance  

### Adding Custom Functionality  

For our custom model, we extend `__init__` to allow users to specify dictionaries containing model information. These dictionaries are then used to generate the necessary `SubpopState`, `SubpopParams`, and `Config` instances.  

### Is Customizing `__init__` Required?  

Customizing `__init__` is **completely optional**!  
- If a subclass does not define its own `__init__`, it **inherits** the `__init__` from its parent class.  

### Implementation Steps  

1. Define an `__init__` function in our subclass.  
2. Use the helper function `clt.make_dataclass_from_dict` to convert dictionaries into actual base model objects.  
3. Call `super().__init__` to execute the original initialization from the parent class.  

This approach ensures all components are properly initialized and brought together into a functional model.  

```python
class SIHRSubpopModel(clt.SubpopModel):

    def __init__(self,
                 compartments_epi_metrics_dict: dict,
                 params_dict: dict,
                 config_dict: dict,
                 RNG: np.random.Generator,
                 name: str = "",
                 wastewater_enabled: bool = False):
        """
        Args:
            compartments_epi_metrics_dict (dict):
                holds current simulation state information,
                such as current values of epidemiological compartments
                and epi metrics -- keys and values respectively
                must match field names and format of FluSubpopState.
            params_dict (dict):
                holds epidemiological parameter values -- keys and
                values respectively must match field names and
                format of FluSubpopParams.
            config_dict (dict):
                holds configuration values -- keys and values
                respectively must match field names and format of
                Config.
            RNG (np.random.Generator):
                numpy random generator object used to obtain
                random numbers.
            name (str):
                name.
            wastewater_enabled (bool):
                if True, includes "wastewater" EpiMetric. Otherwise,
                excludes it.
        """

        # Assign config, params, and state to model-specific
        # types of dataclasses that have epidemiological parameter information
        # and sim state information

        self.wastewater_enabled = wastewater_enabled

        state = clt.make_dataclass_from_dict(SIHRSubpopState, compartments_epi_metrics_dict)
        params = clt.make_dataclass_from_dict(SIHRSubpopParams, params_dict)
        config = clt.make_dataclass_from_dict(clt.Config, config_dict)

        # IMPORTANT NOTE: as always, we must be careful with mutable objects
        #   and generally use deep copies to avoid modification of the same
        #   object. But in this function call, using deep copies is unnecessary
        #   (redundant) because the parent class SubpopModel's __init__()
        #   creates deep copies.
        super().__init__(state, params, config, RNG, name)
```

Note: all following code snippets (unless stated otherwise) are continued from the `SIHRSubpopModel` class code.

## Implementing Abstract Methods in `clt.SubpopModel`  

`clt.SubpopModel` is an abstract base class and includes multiple abstract methods that we must implement in our subclass:  

- `create_interaction_terms`  
- `create_dynamic_vals`  
- `create_schedules`  
- `create_epi_metrics`  
- `create_compartments`  
- `create_transition_variables`  
- `create_transition_variable_groups`  

### Handling `InteractionTerm` Instances  

We do not use `InteractionTerm` instances because these are designed for `MetapopModel`s that consist of multiple `SubpopModel`s. However, we still need to implement `create_interaction_terms`.  
- Since our model does not require interaction terms, we return an empty `sc.objdict`.  

### Handling Other Unused Methods

Similarly, since our model does not include dynamic values, schedules, or epidemiological metrics, we return an empty `sc.objdict` for the following methods:  

- `create_dynamic_vals`  
- `create_schedules`  
- `create_epi_metrics`  

By implementing these methods in this way, we satisfy the requirements of `clt.SubpopModel` while keeping our simple model clean and functional.  

```python
    def create_interaction_terms(self) -> sc.objdict[str, clt.InteractionTerm]:

        return sc.objdict()

    def create_dynamic_vals(self) -> sc.objdict[str, clt.DynamicVal]:

        dynamic_vals = sc.objdict()

        return dynamic_vals

    def create_schedules(self) -> sc.objdict[str, clt.Schedule]:

        schedules = sc.objdict()

        return schedules

    def create_epi_metrics(
            self,
            transition_variables: sc.objdict[str, clt.TransitionVariable]) \
            -> sc.objdict[str, clt.EpiMetric]:

        epi_metrics = sc.objdict()

        return epi_metrics
```

Here we create a `Compartment` instance for each compartment ("S", "I", "H", "R"). Store each instance in an `sc.objdict`, where the keys are the names (strings) of each compartment, and the values are the compartment instances themselves. Return the dictionary.

```python
    def create_compartments(self) -> sc.objdict[str, clt.Compartment]:

        compartments = sc.objdict()

        for name in ("S", "I", "H", "R"):
            compartments[name] = clt.Compartment(getattr(self.state, name))

        return compartments
```

### Creating Transition Variable Instances  

For each transition (we have 4 total), create an instance of the associated `clt.TransitionVariable` subclass. To initialize each `TransitionVariable`, we need to specify the origin `Compartment`, the destination `Compartment`, and the transition type.  

For example, for the transition between the `"S"` and `"I"` compartments, create an instance of `SusceptibleToInfected` (which we created in the code above). We need to pass the corresponding `Compartment` instances to the `origin` and `destination` arguments, in addition to specifying the `transition_type`.  

```python
    def create_transition_variables(
            self,
            compartments: sc.objdict[str, clt.Compartment] = None) -> sc.objdict[str, clt.TransitionVariable]:

        # Grab the `transition_type` specified in `Config`
        type = self.config.transition_type

        transition_variables = sc.objdict()

        S = compartments.S
        I = compartments.I
        H = compartments.H
        R = compartments.R

        transition_variables.S_to_I = SusceptibleToInfected(origin=S, destination=I, transition_type=type)
        transition_variables.I_to_R = InfectedToRecovered(origin=I, destination=R, transition_type=type)
        transition_variables.I_to_H = InfectedToHospitalized(origin=I, destination=H, transition_type=type)
        transition_variables.H_to_R = HospitalizedToRecovered(origin=H, destination=R, transition_type=type)

        return transition_variables
```

### Creating a `TransitionVariableGroup`  

When there are multiple transitions out of a single compartment, we need a `TransitionVariableGroup` to handle the jointly distributed transition logic properly.  

In our model, the `"I"` compartment has two outgoing arcs: one to `"H"` and one to `"R"`.  

We create a `TransitionVariableGroup` saved as `"I_out"` in the transition variable group dictionary. We need to specify:  

- The `Compartment` instance corresponding to `origin`  
- The `transition_type`  
- A tuple (or list) of `TransitionVariable` instances that make up this joint distribution  

```python
    def create_transition_variable_groups(
            self,
            compartments: sc.objdict[str, clt.Compartment] = None,
            transition_variables: sc.objdict[str, clt.TransitionVariable] = None)\
            -> sc.objdict[str, clt.TransitionVariableGroup]:

        transition_type = self.config.transition_type

        transition_variable_groups = sc.objdict()

        transition_variable_groups.I_out = clt.TransitionVariableGroup(origin=compartments.I,
                                                                       transition_type=transition_type,
                                                                       transition_variables=
                                                                       (transition_variables.I_to_R,
                                                                        transition_variables.I_to_H))

        return transition_variable_groups
```

We just created a customized model! Try running `SIHR_demo.py` and playing with the results.