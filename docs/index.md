# City-level Transmission (CLT) Toolbox

> ***The CLT Toolbox is a mathematical framework and modular Python codebase for scalable compartmental models of respiratory virus transmission in a city. This model is written by the [Meyers Lab](http://www.bio.utexas.edu/research/meyers/) and [epiENGAGE center](https://www.cdc.gov/insight-net/php/implementers/index.html).***

Please visit [this website](https://LP-relaxation.github.io/CLT_BaseModel_Docs/) for mathematical formulations, code documentation including tutorials, and code API references. 

The mathematical framework is inspired by the immunoSEIRS model of the Meyers Lab (see [Bi and Bandekar et al. 2023](https://www.researchgate.net/publication/375193467_Scenario_Projections_for_SARS-CoV-2_Influenza_and_RSV_Burden_in_the_US_2023-2024), [Bi et al. 2022](https://repositories.lib.utexas.edu/server/api/core/bitstreams/da7bb152-3343-4135-86e3-040546d6e5b5/content) and [Bouchnita et al. 2021](https://repositories.lib.utexas.edu/items/e8d50517-6e78-488b-8c95-8c1138d90c28) for some related recent publications).

Documentation is created by Linda Pei ("LP"). Toolkit code is created by LP in collaboration with Shuotao "Sonny" Diao, Remy Pasco, and Emily Javan, and with supervision from Dave Morton and Lauren Meyers. Special thanks to Cary Murray for generous guidance on software engineering design. Special credit to Sonny for his ideas about updating compartments analogously to pushing flow on a graph and creating simulation logic that works for arbitrarily many compartments and transition variables. 

## Setup

The CLT Toolkit is written in Python 3.11.0.

To download the latest code release, run the following in Terminal:
```
git clone https://github.com/LP-relaxation/CLT_BaseModel.git
```

Packages used
```
numpy==1.24.3
pandas==1.5.3
pytest==8.3.3
```

## Codebase Overview

Below is an overview of the files in the CLT base model code.

    base_components.py      # Base classes used to create a compartmental model.

    flu_components.py       # Subclasses for MetroFluSim, a specific 
                              S-E-IA-IP-IS-H-R-D + immunity model for influenza.

    flu_tests.py            # Suite of pytest tests for flu_components.py.

    flu_demo.py             # Demo implementation of flu_components.py
                              flu model for tutorial and testing purposes.  

    flu_demo_input_files    # Collection of .json and .csv files used as 
                              input values for flu_demo.py. These values are
                              "toy" predefined values for demo purposes only. 
                              Realistic applications will use estimation 
                              and calibration from real-world data. 

To execute the simple flu demo model, run the following in Terminal (on Mac):
```
python flu_demo.py
```

To execute the flu model tests, run the following in Terminal (on Mac):
```
pytest -v flu_tests.py
```