tensorflow test result
===

Unfortunately TF doesn't support runtime code dispatch according
to CPU capacity. Hence recompiling TF with SIMD enabled is expected
to bring performance boost.

The exception is when TF is built against MKL, MKL is able to
dynamically select code according to the CPU.

### [amd64/CPU] generic code / native code, inception5h

Source and data
> https://salsa.debian.org/science-team/tensorflow  
> b7252d13d59ebecae7928daa25af3a5ab1289bae
> https://storage.googleapis.com/download.tensorflow.org/models/inception5h.zip

Benchmark Procedure

1. compile the benchmark program with `sh debian/tests/tf_tool_benchmark_model.sh`
1. download the pretrained model and unzip to the parent directory.
1. `./tf_benchmark_model --graph=../tensorflow_inception_graph.pb`

Results

| CPU | Compiler | Average Timing (Generic `-O2`) (µs) | Average Timing (Opt `march=native`) (µs) | Boost |
| --- | --- | --- | --- | --- |
| I5-7440HQ (4C4T @ 2.8~3.8GHz, AVX2) | GCC-8 (Debian 8.2.0-6) | 66953.4 ± 3017 | 24661.1 ± 1769 | 2.71x |

CPU details: https://ark.intel.com/
