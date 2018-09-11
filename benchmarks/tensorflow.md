Some TensorFlow Test Results
===

### Overview

Unfortunately TF doesn't support runtime code dispatch according
to CPU capacity. Hence recompiling TF with SIMD enabled is expected
to bring performance boost.

The exception is when TF is built against MKL, MKL is able to
dynamically select code according to the CPU.

### [amd64/CPU] generic code / native code, inception5h

Source, Data, Basic Environment

* Git Repo: https://salsa.debian.org/science-team/tensorflow  
  - Commit: b7252d13d59ebecae7928daa25af3a5ab1289bae  
* Data: https://storage.googleapis.com/download.tensorflow.org/models/inception5h.zip  
* OS: Debian Experimental (2018 Sept)

Benchmark Procedure

1. compile the benchmark program with `sh debian/tests/tf_tool_benchmark_model.sh`
1. download the pretrained model and unzip to the parent directory.
1. `./tf_benchmark_model --graph=../tensorflow_inception_graph.pb`

Results

| CPU | Compiler | Average Timing (Generic `-O2`) (µs) | Average Timing (Opt `march=native`) (µs) | Boost |
| --- | --- | --- | --- | --- |
| I5-7440HQ (4C4T @ 2.8~3.8GHz, AVX2) | GCC (Debian 8.2.0-6) | 66953.4 ± 3017 | 24661.1 ± 1769 | 2.71x |
| I5-7440HQ (4C4T @ 2.8~3.8GHz, AVX2) | Clang 7.0.0-+rc2-1~exp3 (tags/RELEASE_700/rc2) | n/a | n/a | n/a |

### See Also

1. https://github.com/soumith/convnet-benchmarks
2. CPU details: https://ark.intel.com/
