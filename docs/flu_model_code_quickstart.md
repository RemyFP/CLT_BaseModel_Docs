# Flu Model Code Quickstart

Here we provide a quickstart tutorial to help users immediately start playing with the flu model in ```flu_components.py``` with "toy" inputs given by ```JSON``` files in ```flu_demo_input_files```. Recall that the *structure* (functional form) of the flu model is specified in ```flu_components.py```, and the input files simply specify fixed parameter values and initial values for state variables.  See [this page](math_flu_components.md) for the mathematical formulation of the flu model. For creating their own custom input files, users should refer to tables on [this page](flu_model_code.md) that map code variable names to their respective math variables and specify variable dimensions. 

The code snippet below is from ```flu_demo.py```. The script can be run directly from the terminal using 

```bash
python flu_demo.py
```

Here’s the basic procedure to populate the flu model using input files:

1. Create a ```FluModelConstructor``` instance that takes three file paths as input: the configuration file path, fixed parameter values file path, and initial values file path (all in ```JSON``` format).

2. Use this constructor to create a ```TransmissionModel``` instance whose functional form is specified by ```flu_components.py``` and whose fixed parameter values and initial values are specified in the three aforementioend files.

After this, we can simulate the model and plot its behavior. Below, we simulate 300 days. 

```python
import numpy as np
from pathlib import Path

from flu_components import FluModelConstructor
from plotting import create_basic_compartment_history_plot

# Obtain path to folder with JSON input files
base_path = Path(__file__).parent / "flu_demo_input_files"

# Get filepaths for configuration, fixed parameter values, and
#   initial values of state variables
config_filepath = base_path / "config.json"
fixed_params_filepath = base_path / "fixed_params.json"
state_vars_init_vals_filepath = base_path / "state_variables_init_vals.json"

# Create a constructor using these filepaths
flu_demo_constructor = \
    FluModelConstructor(config_filepath,
                        fixed_params_filepath,
                        state_vars_init_vals_filepath)

# Create TransmissionModel instance from the constructor,
#   using a random number generator with starting seed 888888
#   to generate random variables
flu_demo_model = flu_demo_constructor.create_transmission_model(888888)

# Simulate 300 days
flu_demo_model.simulate_until_time_period(300)

# Plot
create_basic_compartment_history_plot(flu_demo_model,
                                      "flu_demo_model.png")
```

![title](figs/flu_demo_model.png)

The following code snippets and outputs below replicate the experience of working in a Python interactive console.

After simulating 300 days, the ```current_simulation_day``` counter indeed is 300, and the attribute ```current_real_date``` gives us the corresponding real-world date associated with the simulation day counter.

```python
flu_demo_model.current_simulation_day
# 300

flu_demo_model.current_real_date
# datetime.date(2023, 6, 4)
```

```TransmissionModel``` instances have a ```StateVariableManager``` that manages and holds StateVariables (EpiCompartments, EpiMetrics, DynamicVals, and Schedules). Using the command below, we can access the state of the simulation after we simulated the model for 300 days. 

```python
flu_demo_model.state_variable_manager.sim_state
# FluSimState(S=array([[306539.],
#       [261363.]]), E=array([[31501.],
#       [36027.]]), I=array([[105991.],
#       [114542.]]), H=array([[ 98354.],
#       [104663.]]), R=array([[175885.],
#       [186626.]]), D=array([[281730.],
#       [296779.]]), population_immunity_hosp=array([[0.11555325],
#       [0.12078151]]), population_immunity_inf=array([[0.11555325],
#       [0.12078151]]), absolute_humidity=12.31748, 
#		flu_contact_matrix=array([[[[2.], [0.5]]], [[[1.95], [1.4]]]]))
```

We can also use our model's built-in dictionary to look up simulation objects (any StateVariable, as well as any TransitionVariable and TransitionVariableGroup) by name. Here, ```S``` is an ```EpiCompartment``` instance, and we can see its current value at the current day of the simulation. 

```python
flu_demo_model.lookup_by_name["S"]
# <base_components.EpiCompartment object at 0x174a79750>

flu_demo_model.lookup_by_name["S"].current_val
# array([[306539.],
#        [261363.]])
```

Here, we look at the current value (most recent realization) of the TransitionVariable ```new_dead.``` We also look at its ```current_rate``` attribute, which is the rate that generated the most recent realization. 

```python
flu_demo_model.lookup_by_name["new_dead"].current_val
# array([[406.],
#        [435.]])

flu_demo_model.lookup_by_name["new_dead"].current_rate
# array([[0.00823744],
#        [0.00820603]])
```