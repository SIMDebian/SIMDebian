tensorflow test result
===

Unfortunately TF doesn't support runtime code dispatch according
to CPU capacity. Hence recompiling TF with SIMD enabled is expected
to bring performance boost.

The exception is when TF is built against MKL, MKL is able to
dynamically select code according to the CPU.

# [ppc64el/CPU] generic code / native code, benchmarking with inceptionv5

Source
> https://salsa.debian.org/science-team/tensorflow
> b7252d13d59ebecae7928daa25af3a5ab1289bae

Machine (osuosl)
> Architecture:          ppc64le
> Byte Order:            Little Endian
> CPU(s):                8
> On-line CPU(s) list:   0-7
> Thread(s) per core:    1
> Core(s) per socket:    1
> Socket(s):             8
> NUMA node(s):          1
> Model:                 2.1 (pvr 004b 0201)
> Model name:            POWER8 (architected), altivec supported
> Hypervisor vendor:     KVM
> Virtualization type:   para
> L1d cache:             64K
> L1i cache:             32K
> NUMA node0 CPU(s):     0-7

Source changes
```
```

Generic result
```
(sid)debian@debian-1:~/tensorflow.pkg/tensorflow$ ./tf_benchmark_model --graph=../tensorflow_inception_graph.pb
2018-09-10 12:36:15.624324: I tensorflow/tools/benchmark/benchmark_model.cc:469] Graph: [../tensorflow_inception_graph.pb]
2018-09-10 12:36:15.624416: I tensorflow/tools/benchmark/benchmark_model.cc:470] Init ops:
2018-09-10 12:36:15.624426: I tensorflow/tools/benchmark/benchmark_model.cc:471] Input layers: [input:0]
2018-09-10 12:36:15.624440: I tensorflow/tools/benchmark/benchmark_model.cc:472] Input shapes: [1,224,224,3]
2018-09-10 12:36:15.624450: I tensorflow/tools/benchmark/benchmark_model.cc:473] Input types: [float]
2018-09-10 12:36:15.624461: I tensorflow/tools/benchmark/benchmark_model.cc:474] Output layers: [output:0]
2018-09-10 12:36:15.624471: I tensorflow/tools/benchmark/benchmark_model.cc:475] Target layers: []
2018-09-10 12:36:15.624487: I tensorflow/tools/benchmark/benchmark_model.cc:476] Num runs: [1000]
2018-09-10 12:36:15.624496: I tensorflow/tools/benchmark/benchmark_model.cc:477] Inter-inference delay (seconds): [-1.0]
2018-09-10 12:36:15.624506: I tensorflow/tools/benchmark/benchmark_model.cc:478] Inter-benchmark delay (seconds): [-1.0]
2018-09-10 12:36:15.624515: I tensorflow/tools/benchmark/benchmark_model.cc:480] Num threads: [-1]
2018-09-10 12:36:15.624525: I tensorflow/tools/benchmark/benchmark_model.cc:481] Benchmark name: []
2018-09-10 12:36:15.624537: I tensorflow/tools/benchmark/benchmark_model.cc:482] Output prefix: []
2018-09-10 12:36:15.624547: I tensorflow/tools/benchmark/benchmark_model.cc:483] Show sizes: [0]
2018-09-10 12:36:15.624556: I tensorflow/tools/benchmark/benchmark_model.cc:484] Warmup runs: [1]
2018-09-10 12:36:15.624567: I tensorflow/tools/benchmark/benchmark_model.cc:251] Loading TensorFlow.
2018-09-10 12:36:15.624595: I tensorflow/tools/benchmark/benchmark_model.cc:258] Got config, 0 devices
2018-09-10 12:36:15.716408: I tensorflow/tools/benchmark/benchmark_model.cc:496] Initialized session in 0.091787s
2018-09-10 12:36:15.716535: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1 iterations, max -1 seconds without detailed stat logging, with -1s sleep between inferences
2018-09-10 12:36:16.052434: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=1 curr=335337

2018-09-10 12:36:16.052583: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1000 iterations, max 10 seconds without detailed stat logging, with -1s sleep between inferences
2018-09-10 12:36:26.109025: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=424 first=27363 curr=21886 min=20873 max=29838 avg=23601.1 std=1660

2018-09-10 12:36:26.109144: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1000 iterations, max 10 seconds with detailed stat logging, with -1s sleep between inferences
2018-09-10 12:36:37.571102: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=405 first=27236 curr=23132 min=21351 max=37195 avg=24724.8 std=1940

2018-09-10 12:36:37.571200: I tensorflow/tools/benchmark/benchmark_model.cc:600] Average inference timings in us: Warmup: 335337, no stats: 23601, with stats: 24724
2018-09-10 12:36:37.574153: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Run Order ==============================
2018-09-10 12:36:37.574169: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 12:36:37.574184: I tensorflow/core/util/stat_summarizer.cc:85] 	                    NoOp	    0.000	    0.011	    0.011	  0.000%	  0.000%	     0.000	        0	_SOURCE
2018-09-10 12:36:37.574198: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.055	    0.030	    0.030	  0.000%	  0.000%	     0.000	        0	mixed4a_1x1_w
2018-09-10 12:36:37.574212: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.088	    0.005	    0.005	  0.000%	  0.000%	     0.000	        0	mixed4a_1x1_b
2018-09-10 12:36:37.574233: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.095	    0.007	    0.007	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_bottleneck_w
2018-09-10 12:36:37.574245: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.104	    0.004	    0.004	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_bottleneck_b
2018-09-10 12:36:37.574259: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.110	    0.006	    0.006	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_w
2018-09-10 12:36:37.574273: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.118	    0.004	    0.004	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_b
2018-09-10 12:36:37.574285: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.124	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed4a_5x5_bottleneck_w
2018-09-10 12:36:37.574299: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.130	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed4a_5x5_bottleneck_b
2018-09-10 12:36:37.574313: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.136	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed4a_5x5_w
2018-09-10 12:36:37.574327: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.143	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed4a_5x5_b
2018-09-10 12:36:37.574339: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.149	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed3b_1x1_w
2018-09-10 12:36:37.574353: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.156	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3b_1x1_b
2018-09-10 12:36:37.574366: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.162	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed3b_3x3_bottleneck_w
2018-09-10 12:36:37.574377: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.168	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3b_3x3_bottleneck_b
2018-09-10 12:36:37.574391: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.174	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed3b_3x3_w
2018-09-10 12:36:37.574404: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.180	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3b_3x3_b
2018-09-10 12:36:37.574418: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.186	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed3b_5x5_bottleneck_w
2018-09-10 12:36:37.574430: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.193	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3b_5x5_bottleneck_b
2018-09-10 12:36:37.574444: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.198	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed3b_5x5_w
2018-09-10 12:36:37.574457: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.205	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3b_5x5_b
2018-09-10 12:36:37.574471: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.211	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed4a/concat_dim
2018-09-10 12:36:37.574484: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.216	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed3a_1x1_w
2018-09-10 12:36:37.574498: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.223	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3a_1x1_b
2018-09-10 12:36:37.574511: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.229	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3a_3x3_bottleneck_w
2018-09-10 12:36:37.574525: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.235	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3a_3x3_bottleneck_b
2018-09-10 12:36:37.574539: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.241	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed3a_3x3_w
2018-09-10 12:36:37.574553: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.248	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3a_3x3_b
2018-09-10 12:36:37.574566: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.254	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed3a_5x5_bottleneck_w
2018-09-10 12:36:37.574580: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.260	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3a_5x5_bottleneck_b
2018-09-10 12:36:37.574592: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.266	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed3a_5x5_w
2018-09-10 12:36:37.574606: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.273	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	mixed3a_5x5_b
2018-09-10 12:36:37.574619: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.279	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	conv2d0_w
2018-09-10 12:36:37.574633: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.285	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	conv2d0_b
2018-09-10 12:36:37.574647: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.291	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	conv2d1_w
2018-09-10 12:36:37.574660: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.298	    0.003	    0.003	  0.000%	  0.001%	     0.000	        0	conv2d1_b
2018-09-10 12:36:37.574674: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.303	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	conv2d2_w
2018-09-10 12:36:37.574687: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.309	    0.004	    0.004	  0.000%	  0.001%	     0.000	        0	conv2d2_b
2018-09-10 12:36:37.574702: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.315	    0.005	    0.005	  0.000%	  0.001%	     0.000	        0	mixed3a_pool_reduce_w
2018-09-10 12:36:37.574716: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.321	    0.004	    0.004	  0.000%	  0.002%	     0.000	        0	mixed3a_pool_reduce_b
2018-09-10 12:36:37.574729: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.327	    0.005	    0.005	  0.000%	  0.002%	     0.000	        0	mixed3b_pool_reduce_w
2018-09-10 12:36:37.574743: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.333	    0.005	    0.005	  0.000%	  0.002%	     0.000	        0	mixed3b_pool_reduce_b
2018-09-10 12:36:37.574757: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.339	    0.005	    0.005	  0.000%	  0.002%	     0.000	        0	mixed4a_pool_reduce_w
2018-09-10 12:36:37.574771: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.346	    0.004	    0.004	  0.000%	  0.002%	     0.000	        0	mixed4a_pool_reduce_b
2018-09-10 12:36:37.574785: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.352	    0.004	    0.004	  0.000%	  0.002%	     0.000	        0	head0_bottleneck_w
2018-09-10 12:36:37.574799: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.358	    0.004	    0.004	  0.000%	  0.002%	     0.000	        0	head0_bottleneck_b
2018-09-10 12:36:37.574817: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.364	    0.004	    0.004	  0.000%	  0.002%	     0.000	        0	head0_bottleneck/reshape/shape
2018-09-10 12:36:37.574831: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.370	    0.004	    0.004	  0.000%	  0.002%	     0.000	        0	nn0_w
2018-09-10 12:36:37.574845: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.375	    0.005	    0.005	  0.000%	  0.002%	     0.000	        0	nn0_b
2018-09-10 12:36:37.574859: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.381	    0.005	    0.005	  0.000%	  0.002%	     0.000	        0	softmax0_w
2018-09-10 12:36:37.574873: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.387	    0.005	    0.005	  0.000%	  0.002%	     0.000	        0	softmax0_b
2018-09-10 12:36:37.574887: I tensorflow/core/util/stat_summarizer.cc:85] 	                    _Arg	    0.393	    0.006	    0.006	  0.000%	  0.002%	     0.000	        0	_arg_input_0_0
2018-09-10 12:36:37.574899: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.401	    3.169	    3.169	  0.023%	  0.025%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 12:36:37.574912: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	    3.590	    0.168	    0.168	  0.001%	  0.026%	     0.000	        0	conv2d0_pre_relu
2018-09-10 12:36:37.574926: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	    3.761	    0.150	    0.150	  0.001%	  0.027%	     0.000	        0	conv2d0
2018-09-10 12:36:37.574940: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	    3.913	    1.243	    1.243	  0.009%	  0.036%	   802.816	        0	maxpool0
2018-09-10 12:36:37.574953: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	    5.162	    0.424	    0.424	  0.003%	  0.039%	   802.816	        0	localresponsenorm0
2018-09-10 12:36:37.574967: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    5.590	    0.252	    0.252	  0.002%	  0.041%	   802.816	        0	conv2d1_pre_relu/conv
2018-09-10 12:36:37.574981: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	    5.847	    0.104	    0.104	  0.001%	  0.041%	     0.000	        0	conv2d1_pre_relu
2018-09-10 12:36:37.574995: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	    5.954	    0.090	    0.090	  0.001%	  0.042%	     0.000	        0	conv2d1
2018-09-10 12:36:37.575007: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    6.047	    5.722	    5.722	  0.041%	  0.083%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 12:36:37.575020: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   11.775	    0.157	    0.157	  0.001%	  0.084%	     0.000	        0	conv2d2_pre_relu
2018-09-10 12:36:37.575034: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   11.935	    0.119	    0.119	  0.001%	  0.085%	     0.000	        0	conv2d2
2018-09-10 12:36:37.575048: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   12.057	    1.754	    1.754	  0.013%	  0.098%	  2408.448	        0	localresponsenorm1
2018-09-10 12:36:37.575061: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   13.815	    0.652	    0.652	  0.005%	  0.102%	   602.112	        0	maxpool1
2018-09-10 12:36:37.575075: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   14.512	    0.469	    0.469	  0.003%	  0.106%	    50.176	        0	mixed3a_5x5_bottleneck_pre_relu/conv
2018-09-10 12:36:37.575089: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   14.508	    0.477	    0.477	  0.003%	  0.109%	   200.704	        0	mixed3a_1x1_pre_relu/conv
2018-09-10 12:36:37.575103: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   14.996	    0.027	    0.027	  0.000%	  0.109%	     0.000	        0	mixed3a_5x5_bottleneck_pre_relu
2018-09-10 12:36:37.575116: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   15.027	    0.015	    0.015	  0.000%	  0.109%	     0.000	        0	mixed3a_5x5_bottleneck
2018-09-10 12:36:37.575131: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   15.001	    0.056	    0.056	  0.000%	  0.110%	     0.000	        0	mixed3a_1x1_pre_relu
2018-09-10 12:36:37.575145: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   14.490	    0.589	    0.589	  0.004%	  0.114%	   602.112	        0	mixed3a_pool
2018-09-10 12:36:37.575158: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   15.060	    0.037	    0.037	  0.000%	  0.114%	     0.000	        0	mixed3a_1x1
2018-09-10 12:36:37.575172: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   14.507	    0.678	    0.678	  0.005%	  0.119%	   301.056	        0	mixed3a_3x3_bottleneck_pre_relu/conv
2018-09-10 12:36:37.575186: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   15.201	    0.088	    0.088	  0.001%	  0.120%	     0.000	        0	mixed3a_3x3_bottleneck_pre_relu
2018-09-10 12:36:37.575200: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   15.293	    0.053	    0.053	  0.000%	  0.120%	     0.000	        0	mixed3a_3x3_bottleneck
2018-09-10 12:36:37.575214: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   15.045	    0.385	    0.385	  0.003%	  0.123%	   100.352	        0	mixed3a_5x5_pre_relu/conv
2018-09-10 12:36:37.575227: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   15.085	    0.378	    0.378	  0.003%	  0.126%	   100.352	        0	mixed3a_pool_reduce_pre_relu/conv
2018-09-10 12:36:37.575241: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   15.444	    0.036	    0.036	  0.000%	  0.126%	     0.000	        0	mixed3a_5x5_pre_relu
2018-09-10 12:36:37.575255: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   15.466	    0.034	    0.034	  0.000%	  0.126%	     0.000	        0	mixed3a_pool_reduce_pre_relu
2018-09-10 12:36:37.575270: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   15.483	    0.020	    0.020	  0.000%	  0.126%	     0.000	        0	mixed3a_5x5
2018-09-10 12:36:37.575283: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   15.503	    0.021	    0.021	  0.000%	  0.126%	     0.000	        0	mixed3a_pool_reduce
2018-09-10 12:36:37.575297: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   15.349	    1.548	    1.548	  0.011%	  0.138%	   401.408	        0	mixed3a_3x3_pre_relu/conv
2018-09-10 12:36:37.575311: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   16.903	    0.110	    0.110	  0.001%	  0.138%	     0.000	        0	mixed3a_3x3_pre_relu
2018-09-10 12:36:37.575324: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   17.016	    0.088	    0.088	  0.001%	  0.139%	     0.000	        0	mixed3a_3x3
2018-09-10 12:36:37.575338: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   17.107	    0.116	    0.116	  0.001%	  0.140%	   802.816	        0	mixed3a
2018-09-10 12:36:37.575351: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   17.243	    0.709	    0.709	  0.005%	  0.145%	   802.816	        0	mixed3b_pool
2018-09-10 12:36:37.575365: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   17.263	    0.799	    0.799	  0.006%	  0.151%	   100.352	        0	mixed3b_5x5_bottleneck_pre_relu/conv
2018-09-10 12:36:37.575379: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   18.066	    0.031	    0.031	  0.000%	  0.151%	     0.000	        0	mixed3b_5x5_bottleneck_pre_relu
2018-09-10 12:36:37.575394: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   18.100	    0.021	    0.021	  0.000%	  0.151%	     0.000	        0	mixed3b_5x5_bottleneck
2018-09-10 12:36:37.575408: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   17.252	    1.202	    1.202	  0.009%	  0.160%	   401.408	        0	mixed3b_1x1_pre_relu/conv
2018-09-10 12:36:37.575421: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   17.260	    1.202	    1.202	  0.009%	  0.168%	   401.408	        0	mixed3b_3x3_bottleneck_pre_relu/conv
2018-09-10 12:36:37.575435: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   17.957	    0.568	    0.568	  0.004%	  0.172%	   200.704	        0	mixed3b_pool_reduce_pre_relu/conv
2018-09-10 12:36:37.575449: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   18.531	    0.058	    0.058	  0.000%	  0.173%	     0.000	        0	mixed3b_pool_reduce_pre_relu
2018-09-10 12:36:37.575463: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   18.468	    0.126	    0.126	  0.001%	  0.174%	     0.000	        0	mixed3b_3x3_bottleneck_pre_relu
2018-09-10 12:36:37.575477: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   18.464	    0.156	    0.156	  0.001%	  0.175%	     0.000	        0	mixed3b_1x1_pre_relu
2018-09-10 12:36:37.575490: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   18.592	    0.036	    0.036	  0.000%	  0.175%	     0.000	        0	mixed3b_pool_reduce
2018-09-10 12:36:37.575504: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   18.624	    0.087	    0.087	  0.001%	  0.176%	     0.000	        0	mixed3b_1x1
2018-09-10 12:36:37.575518: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   18.597	    0.139	    0.139	  0.001%	  0.177%	     0.000	        0	mixed3b_3x3_bottleneck
2018-09-10 12:36:37.575532: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   18.124	    3.472	    3.472	  0.025%	  0.202%	   301.056	        0	mixed3b_5x5_pre_relu/conv
2018-09-10 12:36:37.575545: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   21.604	    0.083	    0.083	  0.001%	  0.202%	     0.000	        0	mixed3b_5x5_pre_relu
2018-09-10 12:36:37.575559: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   21.690	    0.051	    0.051	  0.000%	  0.203%	     0.000	        0	mixed3b_5x5
2018-09-10 12:36:37.575573: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   18.739	    4.069	    4.069	  0.029%	  0.232%	   602.112	        0	mixed3b_3x3_pre_relu/conv
2018-09-10 12:36:37.575586: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   22.823	    0.115	    0.115	  0.001%	  0.233%	     0.000	        0	mixed3b_3x3_pre_relu
2018-09-10 12:36:37.575599: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   22.941	    0.379	    0.379	  0.003%	  0.235%	     0.000	        0	mixed3b_3x3
2018-09-10 12:36:37.575613: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   23.325	    0.187	    0.187	  0.001%	  0.237%	  1505.280	        0	mixed3b
2018-09-10 12:36:37.575626: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   23.518	    0.378	    0.378	  0.003%	  0.239%	   376.320	        0	maxpool4
2018-09-10 12:36:37.575640: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   23.931	    0.217	    0.217	  0.002%	  0.241%	    12.544	        0	mixed4a_5x5_bottleneck_pre_relu/conv
2018-09-10 12:36:37.575654: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   24.152	    0.009	    0.009	  0.000%	  0.241%	     0.000	        0	mixed4a_5x5_bottleneck_pre_relu
2018-09-10 12:36:37.575667: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   24.164	    0.006	    0.006	  0.000%	  0.241%	     0.000	        0	mixed4a_5x5_bottleneck
2018-09-10 12:36:37.575681: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   23.916	    0.306	    0.306	  0.002%	  0.243%	   376.320	        0	mixed4a_pool
2018-09-10 12:36:37.575696: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   23.932	    0.464	    0.464	  0.003%	  0.247%	    75.264	        0	mixed4a_3x3_bottleneck_pre_relu/conv
2018-09-10 12:36:37.575708: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   24.400	    0.028	    0.028	  0.000%	  0.247%	     0.000	        0	mixed4a_3x3_bottleneck_pre_relu
2018-09-10 12:36:37.575722: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   24.431	    0.017	    0.017	  0.000%	  0.247%	     0.000	        0	mixed4a_3x3_bottleneck
2018-09-10 12:36:37.575736: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   23.927	    0.563	    0.563	  0.004%	  0.251%	   150.528	        0	mixed4a_1x1_pre_relu/conv
2018-09-10 12:36:37.575749: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   24.172	    0.329	    0.329	  0.002%	  0.253%	    37.632	        0	mixed4a_5x5_pre_relu/conv
2018-09-10 12:36:37.575763: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   24.505	    0.016	    0.016	  0.000%	  0.253%	     0.000	        0	mixed4a_5x5_pre_relu
2018-09-10 12:36:37.575777: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   24.523	    0.015	    0.015	  0.000%	  0.253%	     0.000	        0	mixed4a_5x5
2018-09-10 12:36:37.575790: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   24.495	    0.046	    0.046	  0.000%	  0.254%	     0.000	        0	mixed4a_1x1_pre_relu
2018-09-10 12:36:37.575802: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   24.544	    0.029	    0.029	  0.000%	  0.254%	     0.000	        0	mixed4a_1x1
2018-09-10 12:36:37.575816: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   24.226	    0.397	    0.397	  0.003%	  0.257%	    50.176	        0	mixed4a_pool_reduce_pre_relu/conv
2018-09-10 12:36:37.575830: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   24.627	    0.020	    0.020	  0.000%	  0.257%	     0.000	        0	mixed4a_pool_reduce_pre_relu
2018-09-10 12:36:37.575844: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   24.649	    0.013	    0.013	  0.000%	  0.257%	     0.000	        0	mixed4a_pool_reduce
2018-09-10 12:36:37.575858: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   24.450	    0.701	    0.701	  0.005%	  0.262%	   159.936	        0	mixed4a_3x3_pre_relu/conv
2018-09-10 12:36:37.575871: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   25.162	    0.044	    0.044	  0.000%	  0.262%	     0.000	        0	mixed4a_3x3_pre_relu
2018-09-10 12:36:37.575885: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   25.209	    0.029	    0.029	  0.000%	  0.263%	     0.000	        0	mixed4a_3x3
2018-09-10 12:36:37.575898: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   25.240	    0.102	    0.102	  0.001%	  0.263%	   398.272	        0	mixed4a
2018-09-10 12:36:37.575911: I tensorflow/core/util/stat_summarizer.cc:85] 	                 AvgPool	   25.347	    0.124	    0.124	  0.001%	  0.264%	    32.512	        0	head0_pool
2018-09-10 12:36:37.575924: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   25.474	    0.135	    0.135	  0.001%	  0.265%	     8.192	        0	head0_bottleneck_pre_relu/conv
2018-09-10 12:36:37.575938: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   25.612	    0.008	    0.008	  0.000%	  0.265%	     0.000	        0	head0_bottleneck_pre_relu
2018-09-10 12:36:37.575952: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   25.625	    0.005	    0.005	  0.000%	  0.265%	     0.000	        0	head0_bottleneck
2018-09-10 12:36:37.575966: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Reshape	   25.633	    0.006	    0.006	  0.000%	  0.265%	     0.000	        0	head0_bottleneck/reshape
2018-09-10 12:36:37.575979: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	   25.641	    0.754	    0.754	  0.005%	  0.271%	     4.096	        0	nn0_pre_relu/matmul
2018-09-10 12:36:37.575994: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   26.404	    0.018	    0.018	  0.000%	  0.271%	     0.000	        0	nn0_pre_relu
2018-09-10 12:36:37.576007: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   26.424	    0.005	    0.005	  0.000%	  0.271%	     0.000	        0	nn0
2018-09-10 12:36:37.576021: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	   26.431	    0.391	    0.391	  0.003%	  0.274%	     4.032	        0	softmax0_pre_activation/matmul
2018-09-10 12:36:37.576035: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   26.825	    0.007	    0.007	  0.000%	  0.274%	     0.000	        0	softmax0_pre_activation
2018-09-10 12:36:37.576049: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Softmax	   26.834	    0.027	    0.027	  0.000%	  0.274%	     0.000	        0	softmax0
2018-09-10 12:36:37.576062: I tensorflow/core/util/stat_summarizer.cc:85] 	                Identity	   26.863	    0.005	    0.005	  0.000%	  0.274%	     0.000	        0	output
2018-09-10 12:36:37.576075: I tensorflow/core/util/stat_summarizer.cc:85] 	                 _Retval	   26.870	    0.011	    0.011	  0.000%	  0.274%	     0.000	        0	_retval_output_0_0
2018-09-10 12:36:37.576088: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 12:36:37.576099: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Top by Computation Time ==============================
2018-09-10 12:36:37.576111: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 12:36:37.576127: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    6.047	    5.722	    5.722	  0.041%	  0.041%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 12:36:37.576141: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   18.739	    4.069	    4.069	  0.029%	  0.070%	   602.112	        0	mixed3b_3x3_pre_relu/conv
2018-09-10 12:36:37.576160: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   18.124	    3.472	    3.472	  0.025%	  0.095%	   301.056	        0	mixed3b_5x5_pre_relu/conv
2018-09-10 12:36:37.576174: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.401	    3.169	    3.169	  0.023%	  0.118%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 12:36:37.576188: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   12.057	    1.754	    1.754	  0.013%	  0.130%	  2408.448	        0	localresponsenorm1
2018-09-10 12:36:37.576202: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   15.349	    1.548	    1.548	  0.011%	  0.142%	   401.408	        0	mixed3a_3x3_pre_relu/conv
2018-09-10 12:36:37.576216: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	    3.913	    1.243	    1.243	  0.009%	  0.150%	   802.816	        0	maxpool0
2018-09-10 12:36:37.576229: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   17.260	    1.202	    1.202	  0.009%	  0.159%	   401.408	        0	mixed3b_3x3_bottleneck_pre_relu/conv
2018-09-10 12:36:37.576243: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   17.252	    1.202	    1.202	  0.009%	  0.168%	   401.408	        0	mixed3b_1x1_pre_relu/conv
2018-09-10 12:36:37.576258: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   17.263	    0.799	    0.799	  0.006%	  0.173%	   100.352	        0	mixed3b_5x5_bottleneck_pre_relu/conv
2018-09-10 12:36:37.576271: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 12:36:37.576282: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Top by Memory Use ==============================
2018-09-10 12:36:37.576328: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 12:36:37.576343: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.401	    3.169	    3.169	  0.023%	  0.023%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 12:36:37.576357: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    6.047	    5.722	    5.722	  0.041%	  0.064%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 12:36:37.576371: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   12.057	    1.754	    1.754	  0.013%	  0.076%	  2408.448	        0	localresponsenorm1
2018-09-10 12:36:37.576385: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   23.325	    0.187	    0.187	  0.001%	  0.078%	  1505.280	        0	mixed3b
2018-09-10 12:36:37.576398: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   17.243	    0.709	    0.709	  0.005%	  0.083%	   802.816	        0	mixed3b_pool
2018-09-10 12:36:37.576412: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   17.107	    0.116	    0.116	  0.001%	  0.084%	   802.816	        0	mixed3a
2018-09-10 12:36:37.576426: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    5.590	    0.252	    0.252	  0.002%	  0.085%	   802.816	        0	conv2d1_pre_relu/conv
2018-09-10 12:36:37.576440: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	    5.162	    0.424	    0.424	  0.003%	  0.088%	   802.816	        0	localresponsenorm0
2018-09-10 12:36:37.576459: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	    3.913	    1.243	    1.243	  0.009%	  0.097%	   802.816	        0	maxpool0
2018-09-10 12:36:37.576473: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   13.815	    0.652	    0.652	  0.005%	  0.102%	   602.112	        0	maxpool1
2018-09-10 12:36:37.576487: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 12:36:37.576497: I tensorflow/core/util/stat_summarizer.cc:85] Number of nodes executed: 139
2018-09-10 12:36:37.576507: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Summary by node type ==============================
2018-09-10 12:36:37.576518: I tensorflow/core/util/stat_summarizer.cc:85] 	             [Node type]	  [count]	  [avg ms]	    [avg %]	    [cdf %]	  [mem KB]	[times called]
2018-09-10 12:36:37.576531: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	       22	     0.054	    81.818%	    81.818%	 10077.888	        0
2018-09-10 12:36:37.576545: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	        6	     0.006	     9.091%	    90.909%	  3562.496	        0
2018-09-10 12:36:37.576558: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	        2	     0.005	     7.576%	    98.485%	  3211.264	        0
2018-09-10 12:36:37.576571: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	        2	     0.001	     1.515%	   100.000%	     8.128	        0
2018-09-10 12:36:37.576584: I tensorflow/core/util/stat_summarizer.cc:85] 	                 _Retval	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 12:36:37.576598: I tensorflow/core/util/stat_summarizer.cc:85] 	                    _Arg	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 12:36:37.576611: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Softmax	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 12:36:37.576624: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Reshape	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 12:36:37.576637: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	       23	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 12:36:37.576650: I tensorflow/core/util/stat_summarizer.cc:85] 	                    NoOp	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 12:36:37.576663: I tensorflow/core/util/stat_summarizer.cc:85] 	                Identity	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 12:36:37.576677: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	       50	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 12:36:37.576690: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	        3	     0.000	     0.000%	   100.000%	  2706.368	        0
2018-09-10 12:36:37.576704: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	       24	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 12:36:37.576717: I tensorflow/core/util/stat_summarizer.cc:85] 	                 AvgPool	        1	     0.000	     0.000%	   100.000%	    32.512	        0
2018-09-10 12:36:37.576728: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 12:36:37.576738: I tensorflow/core/util/stat_summarizer.cc:85] Timings (microseconds): count=405 first=38212 curr=31864 min=29642 max=45766 avg=34421.5 std=2685
2018-09-10 12:36:37.576750: I tensorflow/core/util/stat_summarizer.cc:85] Memory (bytes): count=405 curr=19598656(all same)
2018-09-10 12:36:37.576760: I tensorflow/core/util/stat_summarizer.cc:85] 139 nodes observed
2018-09-10 12:36:37.576770: I tensorflow/core/util/stat_summarizer.cc:85] 
```

# [amd64/CPU] generic code / native code, benchmarking with inceptionv5

Source
> Same as above

Machine
> Architecture:        x86_64
> CPU op-mode(s):      32-bit, 64-bit
> Byte Order:          Little Endian
> CPU(s):              4
> On-line CPU(s) list: 0-3
> Thread(s) per core:  1
> Core(s) per socket:  4
> Socket(s):           1
> NUMA node(s):        1
> Vendor ID:           GenuineIntel
> CPU family:          6
> Model:               158
> Model name:          Intel(R) Core(TM) i5-7440HQ CPU @ 2.80GHz
> Stepping:            9
> CPU MHz:             935.219
> CPU max MHz:         3800.0000
> CPU min MHz:         800.0000
> BogoMIPS:            5616.00
> Virtualization:      VT-x
> L1d cache:           32K
> L1i cache:           32K
> L2 cache:            256K
> L3 cache:            6144K
> NUMA node0 CPU(s):   0-3
> Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf tsc_known_freq pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb invpcid_single pti ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm mpx rdseed adx smap clflushopt intel_pt xsaveopt xsavec xgetbv1 xsaves dtherm ida arat pln pts hwp hwp_notify hwp_act_window hwp_epp flush_l1d
> 

Source changes
```
```
