# MetroFluSim Code Overview

> ***Written by LP, updated 1/23/2025 (work in progress)***

## Overview & Assumptions

The module `flu_components.py` extends the base classes of `base_components.py` to implement **MetroFluSim**, a compartmental model with a specific structure. This model is given by [this mathematical formulation](math_flu_components.md) with a few simplifying assumptions. The script `flu_demo.py` runs the flu model with input data given in `flu_demo_input_files`. Note that this input data gives "toy" values. For a realistic model, we would estimate or calibrate these values using real-world historical data. 

The simplifying assumptions of `flu_components.py` include:

- We model *only one* immunity inducing event (say, H1N1).
	- As a result, we model only one strain (and thus disregard prevalence) and we do not model vaccination. 
	- Matrices that would otherwise have $4$ indices for subpopulation, age, risk, and immunity-inducing event (e.g. $\boldsymbol{M}_{a, r, H1}^{(\ell), H(t)}$ because they are $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert \times \lvert \mathcal I \rvert$) only have $3$ indices in the code (for subpopulation and age-risk group). In other words, for a given subpopulation, these matrices are now only $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ and we drop the immunity-inducing event dimension entirely.
	- Population-level immunity against infection and population-level immunity against hospitalization is modeled only for H1N1, and not for H3N2 or vaccination. 

A more comprehensive model relaxing these simplifying assumptions is coming soon!

## Documentation

Follow the links below for code readme's, tutorials, and other resources.

| Name 															| Explanation													|
| ------------------------------------------------------------- | ------------------------------------------------------------- |
| [Flu Model Demo](flu_model_demo.md)		| Must-read. Explanation of `flu_demo.py` and basic simulation functionality. Helps user get started with a concrete MetroFluSim simulation right away. |
| [Flu Input Requirements](flu_input_requirements.md)	| Must-read. Explanation of `flu_demo_input_files` and the required specifications and formats for customizing MetroFluSim model *values.* |
| [Flu Components Walkthrough](flu_components_walkthrough.md) | Advanced. Explains how `flu_components.py` subclasses are created to build the MetroFluSim model. Must-read for users who want to create a custom model *structure.* Users can use this walkthrough as reference to create custom subclasses for a customized model with a different functional form than MetroFluSim. |