# Flu Model Code Overview

> ***Written by LP, updated 1/23/2025 (work in progress)***

## Overview & Assumptions

The module `flu_components.py` extends the base classes of `base_components.py` to implement a compartmental model with a specific structure. This model is given by the [mathematical formulation of the flu model](math_flu_components.md) with a few simplifying assumptions. The script `flu_demo.py` runs the flu model with input data given in `flu_demo_input_files`. Note that this input data gives "toy" values. For a realistic model, we would estimate or calibrate these values using real-world historical data. 

The simplifying assumptions of `flu_components.py` include:

- We model *only one* immunity inducing event (say, H1N1).
	- As a result, we model only one strain (and thus disregard prevalence) and we do not model vaccination. 
	- Matrices that would otherwise have $3$ indices for age, risk, and immunity-inducing event (e.g. $\boldsymbol{M}_{a, \ell, H1}^H(t)$ because they are $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert \times \lvert \mathcal I \rvert$) only have $2$ indices in the code (for age-risk group). In other words, these matrices are now only $\lvert \mathcal A \rvert \times \lvert \mathcal R \rvert$ and we drop the immunity-inducing event dimension entirely.
	- Population-level immunity against infection and population-level immunity against hospitalization is modeled only for H1N1, and not for H3N2 or vaccination. 

A more comprehensive model relaxing these simplifying assumptions is coming soon!

## Documentation

Follow the links below for code readme's, tutorials, and other resources.

| Name 															| Explanation													|
| ------------------------------------------------------------- | ------------------------------------------------------------- |
| [Flu Model Demo](flu_code_demo.md)		| Must-read. Explanation of `flu_demo.py` and basic simulation functionality. Helps user get started with a concrete simulation right away. |
| [Flu Input Files Overview](flu_input_requirements.md)	| Must-read. Explanation of `flu_demo_input_files` and the required `JSON` specifications and formats for customizing flu model *values.* |
| [Flu Components Walkthrough](flu_components_walkthrough.md) | Advanced. Must-read for users who want to create a custom model *structure.* Explains `flu_components.py` subclasses to demonstrate how to create custom subclasses for a customized flu model or another model. |