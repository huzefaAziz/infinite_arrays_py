# InfiniteArrays

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com/yourusername/infinite-arrays)
[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

A Python package for representing arrays with infinite dimension sizes, designed to work with NumPy and other array types. Infinite arrays are by necessity lazy, and so this package provides lazy evaluation for efficient computation.

This package is inspired by and converted from [InfiniteArrays.jl](https://github.com/JuliaArrays/InfiniteArrays.jl).

## Features

- **Infinite Arrays**: Create arrays with infinite dimensions
- **Lazy Evaluation**: Values are computed on-demand for efficiency
- **NumPy Integration**: Works seamlessly with NumPy arrays
- **Broadcasting Support**: Supports element-wise operations and broadcasting
- **Flexible Indexing**: Support for various infinite range types
- **Mutable Caching**: Convert lazy arrays to cached (mutable) versions
- **Infinite-dimensional QR Algorithm**: Compute spectra of infinite operators with convergence control

## Installation

```bash
pip install infinite-arrays
```

Or install from source:

```bash
git clone https://github.com/yourusername/infinite-arrays.git
cd infinite-arrays
pip install -e .
```

## Quick Start

### Basic Usage

```python
import numpy as np
from infinite_arrays import ∞, Ones, InfiniteDiagonal, cache
import math

# Create an infinite vector of ones
x = Ones(∞)
print(x)  # Shows first 12 elements

# Access elements (0-based indexing)
print(x[0])  # 1.0
print(x[5])  # 1.0

# Create infinite diagonal matrix
D = InfiniteDiagonal(range(1, 10000))  # Diagonal with 1, 2, 3, ...
print(D[0, 0])  # 1.0 (diagonal element)
print(D[0, 1])  # 0.0 (off-diagonal element)

# Broadcasting operations
result = np.exp(-np.arange(1, 100)) + 2
print(result[:5])  # First 5 elements

# Create cached (mutable) array
C = cache(Ones(∞))
C[0] = 3.0
print(C[0])  # 3.0
print(C[1])  # 1.0 (unchanged)
```

### Examples

#### Infinite Vector of Ones

```python
from infinite_arrays import ∞, Ones

x = Ones(∞)
print(x)
# Output:
# Ones<dtype('float64'),1,with indices OneToInf()>:
#   1.0
#   1.0
#   1.0
#   ...
#   ⋮
```

#### Infinite Diagonal Matrix

```python
from infinite_arrays import InfiniteDiagonal

D = InfiniteDiagonal(range(1, 10000))
print(D[0, 0])   # 1.0
print(D[1, 1])   # 2.0
print(D[0, 1])   # 0.0 (off-diagonal)
```

#### Broadcasting Operations

```python
import numpy as np
from infinite_arrays import ∞, Ones, BroadcastArray

x = Ones(∞)

# Element-wise operations
y = x + 2  # Add 2 to each element
z = x * 3  # Multiply each element by 3

# Custom broadcasting
def compute_value(i):
    return math.exp(-i) + 2

result = BroadcastArray(compute_value, (∞,))
print(result[0])  # Computed on-demand
```

#### Cached Arrays (Mutable)

```python
from infinite_arrays import ∞, Ones, cache

# Create a cached version
C = cache(Ones(∞))
C[0] = 3.0
print(C[0])  # 3.0
print(C[1])  # 1.0
```

## API Reference

### Core Classes

#### `Ones(shape=∞, dtype=None)`
Create an infinite array filled with ones.

#### `Zeros(shape=∞, dtype=None)`
Create an infinite array filled with zeros.

#### `Fill(value, shape=∞, dtype=None)`
Create an infinite array filled with a constant value.

#### `InfiniteDiagonal(values, dtype=None)`
Create an infinite diagonal matrix with values from a sequence.

#### `BroadcastArray(func, shape, dtype=None)`
Create a lazy broadcasted array that computes values using a function.

#### `CachedArray(array, dtype=None)`
A cached (mutable) version of an infinite array.

### Utility Functions

#### `cache(array)`
Convert an infinite array to a cached (mutable) version.

### Range Types

#### `OneToInf()`
Infinite range starting from 1: 1, 2, 3, ...

#### `InfUnitRange(start=1, step=1)`
Infinite unit range starting from a given value.

#### `InfStepRange(start, step)`
Infinite step range with specified start and step.

### Constants

#### `∞`
Infinity constant for specifying infinite dimensions.

### Infinite-dimensional QR Algorithm

The package includes an implementation of the infinite-dimensional QR (IQR) algorithm for computing spectra of infinite-dimensional operators, as described in:

> Colbrook, M.J. & Hansen, A.C. "On the infinite-dimensional QR algorithm"  
> Numer. Math. 143, 17-83 (2019). https://doi.org/10.1007/s00211-019-01047-5

#### Basic Usage

```python
from infinite_arrays.iqr import (
    InfiniteOperator,
    iqr_algorithm,
    create_diagonal_operator,
    create_tridiagonal_operator,
)

# Create a diagonal operator
diag_op = create_diagonal_operator(lambda i: float(i + 1))

# Compute spectrum
result = iqr_algorithm(diag_op, n=50, max_iter=1000, tol=1e-10)
print("Eigenvalues:", result['eigenvalues'])
print("Converged:", result['converged'])
```

#### Custom Operators

```python
# Define a custom operator via matrix function
def matrix_func(i, j):
    return 1.0 / (1.0 + abs(i - j))

operator = InfiniteOperator(matrix_func)
result = iqr_algorithm(operator, n=30, compute_eigenvectors=True)
```

#### Adaptive Spectrum Computation

```python
from infinite_arrays.iqr import iqr_spectrum

# Compute spectrum with multiple truncation sizes
spectrum = iqr_spectrum(
    operator,
    n_range=[20, 50, 100],
    adaptive=True
)
```

See `examples/iqr_example.py` for more detailed examples.

## Limitations

- Infinite arrays cannot be converted to NumPy arrays directly
- Length operations (`len()`) are not supported for infinite arrays
- Some operations may raise errors when attempting to materialize infinite arrays

## Development

To contribute to this project:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run tests: `pytest`
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

This package is converted from [InfiniteArrays.jl](https://github.com/JuliaArrays/InfiniteArrays.jl), a Julia package by the JuliaArrays organization.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

