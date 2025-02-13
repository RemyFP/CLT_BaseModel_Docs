# Flu Model Demo

> **_Written by LP, updated 02/13/2025 (work in progress)_** 

In this tutorial, we work through ```flu_demo.py,``` which is a toy implementation of the flu model mathematically formulated [here](math_flu_components.md) and coded in the `flu_model` module. Inputs are given by the contents of ```flu_demo_input_files.```

First, we import necessary packages and modules. Recall that the `clt_base` module has the building blocks for creating compartmental models. The `flu_model` module has subclasses of `clt_base` classes, customized to create a flu model with a specific functional form. To work within the given functional form but edit values such as initial population values and fixed parameters (among other details), we only need to edit input files, which we will do here. 

```python
from pathlib import Path
import numpy as np
import pandas as pd

# Import city-level transmission base components module
import clt_base as clt

# Import flu model module, which contains customized subclasses
import flu_model as flu
```

Next, we obtain our filepaths. Note that all our input files we want to use here are located in ```flu_demo_input_files.```

```python
# Obtain path to folder with JSON input files
base_path = Path(__file__).parent / "flu_demo_input_files"

# Get filepaths for initial values of compartments and epi metrics, fixed parameters, configuration, and travel proportions
compartments_epi_metrics_init_vals_filepath = base_path / "compartments_epi_metrics_init_vals.json"
params_filepath = base_path / "common_params.json"
config_filepath = base_path / "config.json"
travel_proportions_filepath = base_path / "travel_proportions.json"

# Get filepaths for school-work calendar CSV 
calendar_filepath = base_path / "school_work_calendar.csv"
```

To create a `SubpopModel`, we need 5 inputs: (1) a dictionary of initial values for compartments and epi metrics, which will set up a 
SubpopState` instance, (2) a dictionary of fixed parameters, which will be set up a `SubpopParams` instance, (3) a dictionary of experiment configurations, which will set up a `Config` instance, (4) a `DataFrame` with school-work calendar information, and (5) a `DataFrame` with travel proportions between subpopulations. 

Details on input file requirements are given [here](flu_input_requirements.md).

```python
# Read in files as dictionaries and dataframes
# Note that we can also create these dictionaries directly
#   rather than reading from a predefined input data file
state_dict = clt.load_json_new_dict(compartments_epi_metrics_init_vals_filepath)
params_dict = clt.load_json_new_dict(params_filepath)
config_dict = clt.load_json_new_dict(config_filepath)
travel_proportions = clt.load_json_new_dict(travel_proportions_filepath)

calendar_df = pd.read_csv(calendar_filepath, index_col=0)
```

Note that responsible random number generation means using the proper Generator objects, not using something like `np.random.seed(123)`. For more information, read the official Python `numpy.random` documentation. 

```python 
# Create two independent bit generators
bit_generator = np.random.MT19937(88888)
jumped_bit_generator = bit_generator.jumped(1)
```

Here we show an example with two subpopulation models. Note that we can create as many subpopulation models as we would like. If we wanted the two subpopulation models to have different demographics and initial values, fixed parameters, and school-work calendars, we could use different dictionaries and dataframes when creating each subpopulation model. 

```python
# Create two subpopulation models, one for the north
#   side of the city and one for the south side of the city
# In this case, these two (toy) subpopulations have the
#   same demographics, initial compartment and epi metric values,
#   fixed parameters, and school-work calendar.
# If we wanted the "north" subpopulation and "south"
#   subpopulation to have different aforementioned values,
#   we could read in two separate sets of files -- one
#   for each subpopulation
north = flu.FluSubpopModel(state_dict,
                           params_dict,
                           config_dict,
                           calendar_df,
                           np.random.Generator(bit_generator),
                           name="north")

south = flu.FluSubpopModel(state_dict,
                           params_dict,
                           config_dict,
                           calendar_df,
                           np.random.Generator(jumped_bit_generator),
                           name="south")
```

We can access the `SubpopParams` instance that lives on each `SubpopModel` using `.`-notation (for attribute access). Recall that API documentation on attributes can be found [here](base_components_reference.md) and [here](flu_components_reference.md).

```python
# The structure of the code allows us to access
#   the current state and fixed parameters of each
#   subpopulation model.
# For example, here we print out the fixed parameter
#   value for beta_baseline for the "south" subpopulation
print(south.params.beta_baseline)
# 1
```

If our two subpopulation models have fixed parameters in common but some different fixed parameters, we could initialize each subpopulation model from the same dictionary of (common) parameters, then make adjustments to the subpopulation model after the fact, like below. Similar logic applies to demographics and initial values, fixed parameters, and school-work calendars. 

```python
# We can also manually change a fixed parameter value
#   after we have created a SubpopModel -- like so...
# Note that this is quite a large and unrealistic value of
#   beta_baseline, but we'll use this to create
#   a dramatic difference between the two subpopulations
south.params.beta_baseline = 10
```

Recall that `MetapopModel` instances are made up of the `SubpopModel` instances that comprise the travel model. `FluMetapopModel` is a subclass of the base `MetapopModel`. `FluSubpopModel` is a subclass of the base `SubpopModel`, customized for a specific functional form (with pre-specified) state variables and transition variables. Below, we create a metapopulation model that is comprised of our two previously defined subpopulation models.

To start, we create a `FluInterSubpopRepo`, a subclass of the base `InterSubpopRepo`, which manages inter-subpopulation interactions. For our flu metapopulation model, we model travel between subpopulations. `FluInterSubpopRepo` allows easy querying of subpopulation states and has functions to compute complicated quantities used to model travel.

```python
# Create FluInterSubpopRepo instance that manages the subpopulation models
#   and the travel dynamics that link them together
flu_inter_subpop_repo = flu.FluInterSubpopRepo({"north": north, "south": south},
                                               travel_proportions["subpop_names_mapping"],
                                               travel_proportions["travel_proportions_array"])

# Combine two subpopulations into one metapopulation model (travel model)
flu_demo_model = flu.FluMetapopModel(flu_inter_subpop_repo)
```

It is vital to check that our model inputs are sensible. Below, we display our model and also run model checks. 

```python
# Display written forms of both subpopulation models
# Check that model inputs are properly formatted and sensible
flu_demo_model.display()
flu_demo_model.run_model_checks()
```

Simulating the flu model is simple. The following command simulates the model from the current simulation day to the end day specified. 

```python
# Simulate for 50 days
flu_demo_model.simulate_until_time_period(50)
```

Here's how we access the current simulation day and current real date: 

```python
# Get the current real date of the simulation and the
#   current simulation day
print(flu_demo_model.current_simulation_day)
print(flu_demo_model.current_real_date)
# 50
# 2022-11-16
```

We can start and stop the `MetapopModel` simulation at any time. 
```python
# Simulate for another 50 days, from where we last left off
flu_demo_model.simulate_until_time_period(100)
```

Keeping track of the many moving parts in the model is straightforward. We can combine and process these values after we simulate to create our own plots and own analysis. 

```python
# We can "unpack" our flu model and access the current state
#   of each subpopulation -- here's an example with the "north"
#   subpopulation -- this is the state after we have simulated
#   our 100 days
print(flu_demo_model.subpop_models.north.state)
# FluSubpopState(S=array([[ 72151.],
#       [130084.]]), E=array([[227819.],
#       [211647.]]), IP=array([[29671.],
#       [27533.]]), IS=array([[90178.],
#       [84027.]]), IA=array([[33146.],
#       [30962.]]), H=array([[67980.],
#       [64266.]]), R=array([[439039.],
#       [414192.]]), D=array([[40016.],
#       [37289.]]), pop_immunity_hosp=array([[0.53893985],
#       [0.53592074]]), pop_immunity_inf=array([[0.46572628],
#       [0.46331838]]), absolute_humidity=7.28333, flu_contact_matrix=array([[4. , 1.5],
#       [2. , 2. ]]), beta_reduct=0.0, wastewater=None, infection_force=array([[0.71292875],
#       [0.38677235]]))
```

```python
# Remember that we can easily access the objects that
#   make up our subpopulation model -- here's an 
#   example of accessing the "north" subpopulation's
#   compartments
# See API references for more attribute access syntax
print(flu_demo_model.subpop_models.north.compartments)
#0. 'S':  <clt_base.base_components.Compartment object at 0x17333d0d0>
#1. 'E':  <clt_base.base_components.Compartment object at 0x17333c910>
#2. 'IP': <clt_base.base_components.Compartment object at 0x17333cfd0>
#3. 'IS': <clt_base.base_components.Compartment object at 0x173387f90>
#4. 'IA': <clt_base.base_components.Compartment object at 0x173387f50>
#5. 'H':  <clt_base.base_components.Compartment object at 0x173387f10>
#6. 'R':  <clt_base.base_components.Compartment object at 0x173387ed0>
#7. 'D':  <clt_base.base_components.Compartment object at 0x173387e90>
```

Finally, we can generate different types of plots for the flu model after we have simulated it. Here's a simple compartment history plot. Note that the "south" subpopulation has a higher exposed curve and a larger number of infections, which makes sense since we jacked up its baseline transmission rate `beta_baseline` to $10$. 
```python
# Generate simple compartment history plot for flu model
clt.plot_metapop_basic_compartment_history(flu_demo_model, "basic_compartment_history.png")
```

![basic_compartment_history_png](figs/basic_compartment_history.png)

We can reset the simulation and run another replication. 

```python
# Reset the simulation
# Note -- does NOT reset the RNG! Only clears each object's
#   history, resets the simulation day/date to the starting
#   day/date, and returns state variables to their initial values.
flu_demo_model.reset_simulation()
```

Note that the previous compartment history graphs had some interesting periodicity. This is because of the school contact matrix and the work contact matrix, and the school-work calendar. To demonstrate, we after resetting the simulation model, we set the school and work contact matrices to be zero matrices for both subpopulations. After re-running the simulation for 100 days and plotting, we see that the periodicity is gone. 

```python
# Set school and work contact matrices to zero matrices for both
#   subpopulations and demonstrate that this removes the calendar-induced
#   periodicity
num_age_groups = flu_demo_model.subpop_models.north.params.num_age_groups

for subpop_model in flu_demo_model.subpop_models.values():
    subpop_model.params.school_contact_matrix = np.zeros((num_age_groups, num_age_groups))
    subpop_model.params.work_contact_matrix = np.zeros((num_age_groups, num_age_groups))

flu_demo_model.simulate_until_time_period(100)

clt.plot_metapop_basic_compartment_history(flu_demo_model,
                                           "basic_compartment_history_no_periodicity.png")
```

![basic_compartment_history_no_periodicity_png](figs/basic_compartment_history_no_periodicity.png)