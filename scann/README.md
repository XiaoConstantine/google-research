# ScaNN

ScaNN (Scalable Nearest Neighbors) is a method for efficient vector similarity
search at scale. This code release implements [1], which includes search space
pruning and quantization for Maximum Inner Product Search and also supports
other distance functions such as Euclidean distance. The implementation is
designed for x86 processors with AVX2 support. ScaNN achieves state-of-the-art
performance on [ann-benchmarks.com](http://ann-benchmarks.com) as shown on the
glove-100-angular dataset below:

![glove-100-angular](https://github.com/google-research/google-research/raw/master/scann/docs/glove_bench.png)

ScaNN can be configured to fit datasets with different sizes and distributions.
It has both TensorFlow and Python APIs. The library shows strong performance
with large datasets [1]. The code is released for research purposes. For more
details on the academic description of algorithms, please see [1].

Reference [1]:
```
@inproceedings{avq_2020,
  title={Accelerating Large-Scale Inference with Anisotropic Vector Quantization},
  author={Guo, Ruiqi and Sun, Philip and Lindgren, Erik and Geng, Quan and Simcha, David and Chern, Felix and Kumar, Sanjiv},
  booktitle={International Conference on Machine Learning},
  year={2020},
  URL={https://arxiv.org/abs/1908.10396}
}
```
## Installation

`manylinux_2_27`-compatible wheels are available on PyPI:

```
pip install scann
```

ScaNN supports Linux environments running Python versions 3.7-3.10. See
[docs/releases.md](docs/releases.md) for release notes; the page also contains
download links for ScaNN wheels prior to version 1.1.0, which were not released
on PyPI.

In accordance with the
[`manylinux_2_27` specification](https://peps.python.org/pep-0600/), ScaNN
requires `libstdc++` version 3.4.23 or above from the operating system. See
[here](https://stackoverflow.com/questions/10354636) for an example of how
to find your system's `libstdc++` version; it can generally be upgraded by
installing a newer version of `g++`.

### Integration with TensorFlow Serving

We provide custom Docker images of
[TF Serving](https://github.com/tensorflow/serving) that are linked to the ScaNN
TF ops. See the [`tf_serving` directory](tf_serving/README.md) for further
information.

## Building from source

To build ScaNN from source, first install the build tool
[bazel](https://bazel.build), Clang 8, and libstdc++ headers for C++17 (which
are provided with GCC 9). Additionally, ScaNN requires a modern version of
Python (3.7.x or later) and Tensorflow 2.10 installed on that version of Python.
Once these prerequisites are satisfied, run the following command in the root
directory of the repository:

```
python configure.py
CC=clang-8 bazel build -c opt --features=thin_lto --copt=-mavx2 --copt=-mfma --cxxopt="-std=c++17" --copt=-fsized-deallocation --copt=-w :build_pip_pkg
./bazel-bin/build_pip_pkg
```

A .whl file should appear in the root of the repository upon successful
completion of these commands. This .whl can be installed via pip.


### Build on MacOS

There are couple of changes made to enable build on MacOS (Big Sur 11.6) successful

1. Correct tensorflow dylib for MacOS
2. hash_set header check
3. Pybind11 Python Enviroment vars


* **Make sure you have newer version of clang (greater than 11.0)**

* **Bazel version < 5.x is recommended** (there's issues with protobuf)

* **Export following flag to have g++ flavored toolchain**
```bash
export BAZEL_USE_CPP_ONLY_TOOLCHAIN=1

CC=clang bazel build -c opt --macos_sdk_version=<Your sdk version> --features=thin_lto --copt=-mavx2 --copt=-mfma --cxxopt="-D_GLIBCXX_USE_CXX11_ABI=0" --cxxopt=-std=c++17 --copt=-fsized-deallocation --copt=-w :build_pip_pkg

./bazel-bin/build_pip_pkg

```

## Usage

See the example in [docs/example.ipynb](docs/example.ipynb). For a more in-depth
explanation of ScaNN techniques, see [docs/algorithms.md](docs/algorithms.md).
