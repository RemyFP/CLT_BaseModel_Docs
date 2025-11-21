# Python Resources for Modelers

> **_Written by LP, updated 08/25/2025 (work in progress)_** 

Here we provide a list of fundamental concepts for epidemiologists to know to responsibly create, collaborate on, and interact with responsible and reproducible Python code. We have outlined this somewhat like a syllabus for self-directed learning, with links to resources to get started. The reader should make sure they understand all key concepts in the checklist, and read more otherwise. 

Note: we particularly like [Real Python](https://realpython.com), which has many free and thorough tutorials on various Python subjects!

## Coding correctly

Clean code matters for academic research with high standards.

Recommended textbooks (PDF links are unaffiliated with epi-ENGAGE):

- ***MUST READ: ["The Art of Readable Code" by Boswell & Foucher](https://mcusoft.wordpress.com/wp-content/uploads/2015/04/the-art-of-readable-code.pdf)
- ["A Philosophy of Software Design" by Ousterhout](https://milkov.tech/assets/psd.pdf)
- ["Refactoring: Improving the Design of Existing Code" by Fowler & Beck](https://dl.ebooksworld.ir/motoman/Refactoring.Improving.the.Design.of.Existing.Code.2nd.edition.www.EBooksWorld.ir.pdf)
- ["Clean Code: A Handbook of Agile Software Craftsmanship" by Martin](https://github.com/martinmurciego/good-books/blob/master/Clean%20Code_%20A%20Handbook%20of%20Agile%20Software%20Craftsmanship%20-%20Robert%20C.%20Martin.pdf)

Key concepts

- "The Fundamental Theorem of Readability: Code should be written to minimize the time it would take for someone else to understand it." (Boswell & Foucher)
- Function and variable names should be specific and precise -- ChatGPT is great at helping brainstorm names!
- DOT -- DO ONE THING -- "Functions should do one thing. They should do it well. They should do it only." And "functions should do something, or answer something, but not both." (Martin) -- nice articles [here](https://medium.com/codex/should-functions-be-small-e76b45aa93f) and [here](https://ashishmd.medium.com/summary-of-clean-code-by-robert-c-martin-part-3-functions-72b3115472e).
- DRY -- DON'T REPEAT YOURSELF (Hunt & Thomas) -- example resources [here](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) and [here](https://thevaluable.dev/dry-principle-cost-benefit-example/).
- Tactical instead of strategic programming leads to technical debt -- "Complexity comes from an accumulation of dependencies and obscurities. As complexity increases, it leads to change amplification, a high cognitive load, and unknown unknowns." (Ousterhout) 

Advanced concepts

- For classes -- "Single Responsibility Principle" (Martin) -- example resources [here](https://en.wikipedia.org/wiki/Single-responsibility_principle) and [here](https://www.sicpers.info/2023/10/ive-vastly-misunderstood-the-single-responsibility-principle/).

## Test, test, test!

Key concepts

- Any time a user changes code or adds code, they should add new tests and also make sure that all the old tests still pass. 
- Unit testing is critical to ensuring code behaves as intended and ensuring that changes/updates do not add new bugs or break existing functionality. If we haven't tested it, we shouldn't trust it. We should ***prove*** our code works! Check out this great MIT lecture on testing [here](https://web.mit.edu/6.005/www/fa16/classes/03-testing/#reading_3_testing).
- Testing should have good coverage and handle different edge cases -- bugs often occur "on the boundaries" of inputs. 
- The CLT Base Model code uses `pytest` for easy management of unit testing -- [here's](https://realpython.com/pytest-python-testing/) an in-depth tutorial. 
- We must also implement higher-level tests such as [integration tests](https://www.geeksforgeeks.org/software-engineering-integration-testing/#) and [acceptance tests](https://testsigma.com/guides/acceptance-testing/), to make sure the whole shebang works.
- One crucial reason to write modular code (and functions that do one thing only) is to allow for testing of specific modules and functions (Boswell & Foucher). If the code is not easy to test, we cannot easily guarantee its quality.

Bonus articles on testing

- Check out these nice articles on unit testing [here](https://canvascodebw.medium.com/unit-testing-pros-and-cons-understanding-why-testing-code-is-a-good-practice-47a81d67ccde) and [here](https://medium.com/simform-engineering/importance-of-unit-testing-in-software-development-3f47ef326be1). 
And [here](https://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html) is a nice overview of TDD -- Test-Driven Development (Martin).

## Python modules and imports, IDEs, and running Python scripts

Key concepts

- Jupyter notebooks are for exploration, NOT for collaborative software development. Nature wrote a blog post warning about Jupyter notebooks: "computational notebooks can also be confusing and foster poor coding practices. And they are difficult to share, collaborate on and reproduce. A 2019 study found that just 24% of 863,878 publicly available Jupyter notebooks on GitHub could be successfully re-executed, and only 4% produced the same results" -- blog post link [here](https://archive.is/s7EbT#selection-1109.4-1121.1) and conference paper [here](https://archive.is/o/s7EbT/https://doi.org/10.1109/MSR.2019.00077). 
- As programs grow in size, they should be split into smaller files and organized -- modules and importing are key here. Here's a [quick tutorial](https://www.datacamp.com/tutorial/modules-in-python) and a more [in-depth tutorial](https://realpython.com/python-modules-packages/).
- Interactive Developer Environments (IDEs) are ideal for working with Python modules. We recommend Pycharm -- the community edition [here](https://www.jetbrains.com/pycharm/) is free to download.
- Python can be run in many different ways -- users should understand the difference between running a script in an IDE Console and from the command line -- here's a [guide](https://realpython.com/run-python-scripts/).
- We recommend [pip](https://pypi.org/project/pip/) or [conda](https://docs.conda.io/projects/conda/en/latest/user-guide/getting-started.html) for Python package management. There can sometimes be some issues with [mixing pip and conda](https://discuss.python.org/t/pip-conda-compatibility/24375), so it may be helpful to consistently only use one or the other. 
- Users should be comfortable reading API documentation such as the [CLT Base Model Code's references](base_components_reference.md) or [numpy's references](https://numpy.org/doc/2.2/) -- there is no need to try to memorize syntax. Users should be comfortable parsing the technical details of classes and methods, customizing default arguments, and playtesting parts of the package for themselves. 

More reading

- Jupyter notebooks have issues with hidden states -- read more [here](https://ploomber.io/blog/nbs-myths/).

## Documentation and version control

Key concepts

- Users should be familiar with git. We strongly recommend this nice guide on github collaboration [here](https://medium.com/@jonathanmines/the-ultimate-github-collaboration-guide-df816e98fb67).

	- Specifically, users should know the following concepts in git: cloning a repo, local and remote repos, committing and pushing, pull requests, pulling, merging.

- Collaborators should never push to main -- they should create a pull request (after adding tests and checking all tests) to request the main branch incorporate any new changes.

- Users should STOP the common practice of saving versions like `simulation_v3.2_final_final_01302025` -- this is ***horrible*** for many reasons. Users should instead use git version control and intentional git commit messages to keep track of their code versions.

- Users should add `.txt` and `.md` files documenting any mathematical parameters to their repo and make this a part of their version control setup. This way, users can keep track of WHY and WHEN certain parameter values were changed. 

- The CLT Base Model Code documentation on this website is generated using `mkdocs`, and automatically populates code references hosted on this website from the code. The code docstrings must follow a specific format, outlined [here](https://realpython.com/python-project-documentation-with-mkdocs/).
- Users should always write proper function signatures in their code -- specifying data input and return types is very important. [This article here](https://thepythoncodingbook.com/2022/12/27/type-hints-in-python-functions/) is a starting point to learn more.  


## Other important points...

Key concepts

- The CLT Base Model code uses object-oriented programming (OOP), which may be new to some modelers. The coding structure is similar to [StarSim](https://github.com/starsimhub/starsim) and [TACC's Pandemic Simulator](https://github.com/TACC/pandemicexercisesimulator) -- it is a tried and tested strategy. We strongly recommend [this tutorial](https://realpython.com/inheritance-composition-python/#mixing-features-with-mixin-classes) for those new to OOP.
	- Specifically, users should know the following concepts: inheritance, attributes, abstract base classes, abstract methods.
- We need to be ***responsible*** with random number generation. ***Please, stop using*** `np.random.seed`! Use generators instead! Here is a [superb article](https://blog.scientific-python.org/numpy/numpy-rng/) outlining some dangers with misusing `numpy` random number generation, with links to the relevant numpy documentation. 
- For-loops are horrible for efficiency and vectorized operations are better. Often, when possible, mathematical computations should be written in matrix multiplication form instead of for-loops. [Here](https://www.pythonlikeyoumeanit.com/module_3.html) is a tutorial on the essentials of `numpy` and vectorized operations on `numpy` arrays.
- The `dataclasses` module makes storing data very easy -- here's a [quick tutorial](https://www.dataquest.io/blog/how-to-use-python-data-classes/).
- Users should understand interfaces and duck typing in Python -- here's a [short overview](https://www.tutorialspoint.com/python/python_interfaces.htm).