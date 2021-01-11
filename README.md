[![Build Status](https://travis-ci.org/CharJon/GeCO.svg?branch=main)](https://travis-ci.org/CharJon/GeCO) [![codecov](https://codecov.io/gh/CharJon/GeCO/branch/main/graph/badge.svg?token=IRS3LOkoFZ)](https://codecov.io/gh/CharJon/GeCO)

# GeCO
Generators for Combinatorial Optimization

## Installation
The code heavily depends on the python interface of SCIP: [PySCIPOpt](https://github.com/scipopt/PySCIPOpt). 
The easiest way to install it is using conda
```bash
conda install -c scipopt pyscipopt
```
This just might not result in the most up-to-date version, another way is installing the SCIP solver and the pip package of `pyscipopt` following [this](https://github.com/scipopt/PySCIPOpt/blob/master/INSTALL.md) guide.

Then, once you have `pyscipopt` installed, you are ready to install the `geco` package.
```bash
pip install geco
```

That's it, now you are ready to generate some instances!

## Example
Assume you want a knapsack instance like in the Yang et al. [paper](http://www.optimization-online.org/DB_HTML/2020/02/7626.html). 

You start by looking through the knapsack module/package, then searching for a function with the name `FIRSTAUTHOR_instance`. 
In this case we find a [`yang.py`](geco/mips/knapsack/yang.py) file in the `mips/knapsack` package.

To generate an instance with 5 items you would run
```python3
from geco import knapsack

knapsack.yang_instance(5, seed=1)
```
This, as all generators inside the `mips` subpackage, return a `PySCIPOpt` model that makes use of the SCIP mixed integer programming solver, refer to their docs to learn how to set params, solve the instance and a lot more.

### Randomization
As you might have noticed the generator function has a seed parameter, as a matter of fact this is common through out all generators that exhibit random behavior, it is used to preserve the random state, in order to get a random instance each time you can use `seed=None`.

### Multiple instance generation
In case you want to generate more than one instance, we have created some helpful generator functions in the [`generator.py`](geco/generator.py).

To generate n instances you can use the `generate_n` function, an example to generate 10 Yang knapsack instances would be 
```python3
for model in generate_n(lambda seed: knapsack.yang_instance(5, seed), 10):
    model.optimize()
```

There is also another function `generate` which is more flexible, assuming you don't know exactly how many instance you might require, it works the exact same way it just doesn't stop after `n` instances are generated.

### MIPLIB
[MIPLIB](https://miplib.zib.de/) 2017 instances can be loaded into a 
PySCIPOpt model using the following function
```python
from geco.mips.miplib import loader

instance = loader.load_instance('INSTANCE_NAME.mps.gz')
```
To load many instances following a certain filter, you can use the `load_instances()` function.
Assume you want a generator for easy MIPLIB instances, you have to first downlaod the
[csv](https://miplib.zib.de/tag_benchmark.html) file that MIPLIB provides. 
Then, you can run the function with the following params
```python
from geco.mips.miplib import loader

generator = loader.load_instances(
    filters= {"Status  Sta.": "hard"},
    instances_csv="CSV_FILE_PATH"
)
for instance in generator:
    # do whatever you want with the instance
```
## Implemented Generators
All the following instances are implemented following some of the generation techniques found in the literature, please refer to the modules corresponding to the generating function for a reference to where it was introduced. 

### MIPS
- Capacitated Facility Location
- Scheduling
- Knapsack
- Set Packing
- Set Cover
- Production Planning
- Maximum Independent Set 
- Maximum Cut
- Packing
- Graph Coloring

# Contributing
If you want to add some new generator, fix a big or enhance the repository in some way, please refer to our [guide](CONTRIBUTING.md).
