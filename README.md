# Optax

![CI status](https://github.com/google-deepmind/optax/actions/workflows/tests.yml/badge.svg?branch=main)
[![Documentation Status](https://readthedocs.org/projects/optax/badge/?version=latest)](http://optax.readthedocs.io)
![pypi](https://img.shields.io/pypi/v/optax)

## Introduction

Optax is a gradient processing and optimization library for JAX.

Optax is designed to facilitate research by providing building blocks
that can be easily recombined in custom ways.

Our goals are to

*   Provide simple, well-tested, efficient implementations of core components.
*   Improve research productivity by enabling to easily combine low-level
    ingredients into custom optimisers (or other gradient processing components).
*   Accelerate adoption of new ideas by making it easy for anyone to contribute.

We favour focusing on small composable building blocks that can be effectively
combined into custom solutions. Others may build upon these basic components
in more complicated abstractions. Whenever reasonable, implementations prioritise
readability and structuring code to match standard equations, over code reuse.

An initial prototype of this library was made available in JAX's experimental
folder as `jax.experimental.optix`. Given the wide adoption across DeepMind
of `optix`, and after a few iterations on the API, `optix` was eventually moved
out of `experimental` as a standalone open-source library, and renamed `optax`.

Documentation on Optax can be found at [optax.readthedocs.io](https://optax.readthedocs.io/).

## Installation

You can install the latest released version of Optax from PyPI via:

```sh
pip install optax
```

or you can install the latest development version from GitHub:

```sh
pip install git+https://github.com/google-deepmind/optax.git
```

## Quickstart

Optax contains implementations of [many popular optimizers](https://optax.readthedocs.io/en/latest/api/optimizers.html) and
[loss functions](https://optax.readthedocs.io/en/latest/api/losses.html).
For example, the following code snippet uses the Adam optimizer from `optax.adam`
and the mean squared error from `optax.l2_loss`. We initialize the optimizer
state using the `init` function and `params` of the model.

```python
optimizer = optax.adam(learning_rate)
# Obtain the `opt_state` that contains statistics for the optimizer.
params = {'w': jnp.ones((num_weights,))}
opt_state = optimizer.init(params)
```

To write the update loop we need a loss function that can be differentiated by
Jax (with `jax.grad` in this
example) to obtain the gradients.

```python
compute_loss = lambda params, x, y: optax.l2_loss(params['w'].dot(x), y)
grads = jax.grad(compute_loss)(params, xs, ys)
```

The gradients are then converted via `optimizer.update` to obtain the updates
that should be applied to the current parameters to obtain the new ones.
`optax.apply_updates` is a convenience utility to do this.

```python
updates, opt_state = optimizer.update(grads, opt_state)
params = optax.apply_updates(params, updates)
```

You can continue the quick start in [the Optax quickstart notebook.](https://github.com/google-deepmind/optax/blob/main/examples/quick_start.ipynb)

## Citing Optax

This repository is part of the DeepMind JAX Ecosystem, to cite Optax
please use the citation:

```bibtex
@software{deepmind2020jax,
  title = {The {D}eep{M}ind {JAX} {E}cosystem},
  author = {DeepMind and Babuschkin, Igor and Baumli, Kate and Bell, Alison and Bhupatiraju, Surya and Bruce, Jake and Buchlovsky, Peter and Budden, David and Cai, Trevor and Clark, Aidan and Danihelka, Ivo and Dedieu, Antoine and Fantacci, Claudio and Godwin, Jonathan and Jones, Chris and Hemsley, Ross and Hennigan, Tom and Hessel, Matteo and Hou, Shaobo and Kapturowski, Steven and Keck, Thomas and Kemaev, Iurii and King, Michael and Kunesch, Markus and Martens, Lena and Merzic, Hamza and Mikulik, Vladimir and Norman, Tamara and Papamakarios, George and Quan, John and Ring, Roman and Ruiz, Francisco and Sanchez, Alvaro and Sartran, Laurent and Schneider, Rosalia and Sezener, Eren and Spencer, Stephen and Srinivasan, Srivatsan and Stanojevi\'{c}, Milo\v{s} and Stokowiec, Wojciech and Wang, Luyu and Zhou, Guangyao and Viola, Fabio},
  url = {http://github.com/google-deepmind},
  year = {2020},
}
```
