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
diff --git a/debian/rules b/debian/rules
index 53b186ef..130196c6 100755
--- a/debian/rules
+++ b/debian/rules
@@ -1,7 +1,7 @@
 #!/usr/bin/make -f
 export DEB_BUILD_MAINT_OPTIONS = hardening=+all
 export DEB_CFLAGS_MAINT_APPEND =
-export DEB_CXXFLAGS_MAINT_APPEND =
+export DEB_CXXFLAGS_MAINT_APPEND = -mcpu=native
 # XXX: Warning: -Wl,--as-needed will trigger FTBFS!!
 export DEB_LDFLAGS_MAINT_APPEND =
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

mcpu=native
```
(sid)debian@debian-1:~/tensorflow.pkg/tensorflow$ ./tf_benchmark_model --graph=../tensorflow_inception_graph.pb
2018-09-10 13:58:01.996479: I tensorflow/tools/benchmark/benchmark_model.cc:469] Graph: [../tensorflow_inception_graph.pb]
2018-09-10 13:58:01.996594: I tensorflow/tools/benchmark/benchmark_model.cc:470] Init ops:
2018-09-10 13:58:01.996605: I tensorflow/tools/benchmark/benchmark_model.cc:471] Input layers: [input:0]
2018-09-10 13:58:01.996614: I tensorflow/tools/benchmark/benchmark_model.cc:472] Input shapes: [1,224,224,3]
2018-09-10 13:58:01.996623: I tensorflow/tools/benchmark/benchmark_model.cc:473] Input types: [float]
2018-09-10 13:58:01.996632: I tensorflow/tools/benchmark/benchmark_model.cc:474] Output layers: [output:0]
2018-09-10 13:58:01.996641: I tensorflow/tools/benchmark/benchmark_model.cc:475] Target layers: []
2018-09-10 13:58:01.996657: I tensorflow/tools/benchmark/benchmark_model.cc:476] Num runs: [1000]
2018-09-10 13:58:01.996666: I tensorflow/tools/benchmark/benchmark_model.cc:477] Inter-inference delay (seconds): [-1.0]
2018-09-10 13:58:01.996676: I tensorflow/tools/benchmark/benchmark_model.cc:478] Inter-benchmark delay (seconds): [-1.0]
2018-09-10 13:58:01.996685: I tensorflow/tools/benchmark/benchmark_model.cc:480] Num threads: [-1]
2018-09-10 13:58:01.996697: I tensorflow/tools/benchmark/benchmark_model.cc:481] Benchmark name: []
2018-09-10 13:58:01.996706: I tensorflow/tools/benchmark/benchmark_model.cc:482] Output prefix: []
2018-09-10 13:58:01.996717: I tensorflow/tools/benchmark/benchmark_model.cc:483] Show sizes: [0]
2018-09-10 13:58:01.996726: I tensorflow/tools/benchmark/benchmark_model.cc:484] Warmup runs: [1]
2018-09-10 13:58:01.996736: I tensorflow/tools/benchmark/benchmark_model.cc:251] Loading TensorFlow.
2018-09-10 13:58:01.996772: I tensorflow/tools/benchmark/benchmark_model.cc:258] Got config, 0 devices
2018-09-10 13:58:02.112598: I tensorflow/tools/benchmark/benchmark_model.cc:496] Initialized session in 0.11578s
2018-09-10 13:58:02.112822: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1 iterations, max -1 seconds without detailed stat logging, with -1s sleep between inferences
2018-09-10 13:58:02.677115: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=1 curr=563711

2018-09-10 13:58:02.677315: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1000 iterations, max 10 seconds without detailed stat logging, with -1s sleep between inferences
2018-09-10 13:58:12.805872: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=61 first=148120 curr=142851 min=98477 max=460913 avg=165887 std=57460

2018-09-10 13:58:12.806099: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1000 iterations, max 10 seconds with detailed stat logging, with -1s sleep between inferences
2018-09-10 13:58:23.177830: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=73 first=142506 curr=131035 min=76080 max=231098 avg=137022 std=35646

2018-09-10 13:58:23.178023: I tensorflow/tools/benchmark/benchmark_model.cc:600] Average inference timings in us: Warmup: 563711, no stats: 165886, with stats: 137022
2018-09-10 13:58:23.182080: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Run Order ==============================
2018-09-10 13:58:23.182097: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 13:58:23.182111: I tensorflow/core/util/stat_summarizer.cc:85] 	                    NoOp	    0.000	    0.013	    0.013	  0.000%	  0.000%	     0.000	        0	_SOURCE
2018-09-10 13:58:23.182123: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.042	    0.041	    0.041	  0.000%	  0.000%	     0.000	        0	mixed4a_1x1_w
2018-09-10 13:58:23.182135: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.086	    0.008	    0.008	  0.000%	  0.001%	     0.000	        0	mixed4a_1x1_b
2018-09-10 13:58:23.182147: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.096	    0.008	    0.008	  0.000%	  0.001%	     0.000	        0	mixed4a_3x3_bottleneck_w
2018-09-10 13:58:23.182159: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.107	    0.007	    0.007	  0.000%	  0.001%	     0.000	        0	mixed4a_3x3_bottleneck_b
2018-09-10 13:58:23.182171: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.117	    0.008	    0.008	  0.000%	  0.001%	     0.000	        0	mixed4a_3x3_w
2018-09-10 13:58:23.182184: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.127	    0.006	    0.006	  0.000%	  0.001%	     0.000	        0	mixed4a_3x3_b
2018-09-10 13:58:23.182196: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.135	    0.008	    0.008	  0.000%	  0.001%	     0.000	        0	mixed4a_5x5_bottleneck_w
2018-09-10 13:58:23.182207: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.145	    0.007	    0.007	  0.000%	  0.001%	     0.000	        0	mixed4a_5x5_bottleneck_b
2018-09-10 13:58:23.182219: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.154	    0.008	    0.008	  0.000%	  0.001%	     0.000	        0	mixed4a_5x5_w
2018-09-10 13:58:23.182231: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.164	    0.006	    0.006	  0.000%	  0.001%	     0.000	        0	mixed4a_5x5_b
2018-09-10 13:58:23.182243: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.172	    0.008	    0.008	  0.000%	  0.001%	     0.000	        0	mixed3b_1x1_w
2018-09-10 13:58:23.182255: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.182	    0.006	    0.006	  0.000%	  0.001%	     0.000	        0	mixed3b_1x1_b
2018-09-10 13:58:23.182267: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.191	    0.007	    0.007	  0.000%	  0.001%	     0.000	        0	mixed3b_3x3_bottleneck_w
2018-09-10 13:58:23.182278: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.201	    0.006	    0.006	  0.000%	  0.001%	     0.000	        0	mixed3b_3x3_bottleneck_b
2018-09-10 13:58:23.182290: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.209	    0.007	    0.007	  0.000%	  0.001%	     0.000	        0	mixed3b_3x3_w
2018-09-10 13:58:23.182302: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.219	    0.006	    0.006	  0.000%	  0.001%	     0.000	        0	mixed3b_3x3_b
2018-09-10 13:58:23.182314: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.228	    0.007	    0.007	  0.000%	  0.001%	     0.000	        0	mixed3b_5x5_bottleneck_w
2018-09-10 13:58:23.182326: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.237	    0.006	    0.006	  0.000%	  0.001%	     0.000	        0	mixed3b_5x5_bottleneck_b
2018-09-10 13:58:23.182338: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.245	    0.008	    0.008	  0.000%	  0.001%	     0.000	        0	mixed3b_5x5_w
2018-09-10 13:58:23.182350: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.255	    0.007	    0.007	  0.000%	  0.002%	     0.000	        0	mixed3b_5x5_b
2018-09-10 13:58:23.182412: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.264	    0.006	    0.006	  0.000%	  0.002%	     0.000	        0	mixed4a/concat_dim
2018-09-10 13:58:23.182425: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.273	    0.007	    0.007	  0.000%	  0.002%	     0.000	        0	mixed3a_1x1_w
2018-09-10 13:58:23.182437: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.283	    0.006	    0.006	  0.000%	  0.002%	     0.000	        0	mixed3a_1x1_b
2018-09-10 13:58:23.182449: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.292	    0.007	    0.007	  0.000%	  0.002%	     0.000	        0	mixed3a_3x3_bottleneck_w
2018-09-10 13:58:23.182461: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.301	    0.007	    0.007	  0.000%	  0.002%	     0.000	        0	mixed3a_3x3_bottleneck_b
2018-09-10 13:58:23.182473: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.311	    0.007	    0.007	  0.000%	  0.002%	     0.000	        0	mixed3a_3x3_w
2018-09-10 13:58:23.182485: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.320	    0.007	    0.007	  0.000%	  0.002%	     0.000	        0	mixed3a_3x3_b
2018-09-10 13:58:23.182497: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.329	    0.008	    0.008	  0.000%	  0.002%	     0.000	        0	mixed3a_5x5_bottleneck_w
2018-09-10 13:58:23.182509: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.339	    0.006	    0.006	  0.000%	  0.002%	     0.000	        0	mixed3a_5x5_bottleneck_b
2018-09-10 13:58:23.182521: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.348	    0.007	    0.007	  0.000%	  0.002%	     0.000	        0	mixed3a_5x5_w
2018-09-10 13:58:23.182533: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.358	    0.006	    0.006	  0.000%	  0.002%	     0.000	        0	mixed3a_5x5_b
2018-09-10 13:58:23.182545: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.367	    0.006	    0.006	  0.000%	  0.002%	     0.000	        0	conv2d0_w
2018-09-10 13:58:23.182557: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.376	    0.006	    0.006	  0.000%	  0.002%	     0.000	        0	conv2d0_b
2018-09-10 13:58:23.182569: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.385	    0.007	    0.007	  0.000%	  0.002%	     0.000	        0	conv2d1_w
2018-09-10 13:58:23.182581: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.394	    0.006	    0.006	  0.000%	  0.002%	     0.000	        0	conv2d1_b
2018-09-10 13:58:23.182593: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.402	    0.008	    0.008	  0.000%	  0.002%	     0.000	        0	conv2d2_w
2018-09-10 13:58:23.182605: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.412	    0.008	    0.008	  0.000%	  0.002%	     0.000	        0	conv2d2_b
2018-09-10 13:58:23.182622: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.422	    0.008	    0.008	  0.000%	  0.003%	     0.000	        0	mixed3a_pool_reduce_w
2018-09-10 13:58:23.182635: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.433	    0.007	    0.007	  0.000%	  0.003%	     0.000	        0	mixed3a_pool_reduce_b
2018-09-10 13:58:23.182649: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.442	    0.008	    0.008	  0.000%	  0.003%	     0.000	        0	mixed3b_pool_reduce_w
2018-09-10 13:58:23.182663: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.452	    0.006	    0.006	  0.000%	  0.003%	     0.000	        0	mixed3b_pool_reduce_b
2018-09-10 13:58:23.182678: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.461	    0.008	    0.008	  0.000%	  0.003%	     0.000	        0	mixed4a_pool_reduce_w
2018-09-10 13:58:23.182691: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.471	    0.007	    0.007	  0.000%	  0.003%	     0.000	        0	mixed4a_pool_reduce_b
2018-09-10 13:58:23.182707: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.481	    0.007	    0.007	  0.000%	  0.003%	     0.000	        0	head0_bottleneck_w
2018-09-10 13:58:23.182719: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.491	    0.006	    0.006	  0.000%	  0.003%	     0.000	        0	head0_bottleneck_b
2018-09-10 13:58:23.182732: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.499	    0.006	    0.006	  0.000%	  0.003%	     0.000	        0	head0_bottleneck/reshape/shape
2018-09-10 13:58:23.182748: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.507	    0.007	    0.007	  0.000%	  0.003%	     0.000	        0	nn0_w
2018-09-10 13:58:23.182761: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.517	    0.006	    0.006	  0.000%	  0.003%	     0.000	        0	nn0_b
2018-09-10 13:58:23.182777: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.525	    0.006	    0.006	  0.000%	  0.003%	     0.000	        0	softmax0_w
2018-09-10 13:58:23.182789: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.534	    0.006	    0.006	  0.000%	  0.003%	     0.000	        0	softmax0_b
2018-09-10 13:58:23.182803: I tensorflow/core/util/stat_summarizer.cc:85] 	                    _Arg	    0.543	    0.006	    0.006	  0.000%	  0.003%	     0.000	        0	_arg_input_0_0
2018-09-10 13:58:23.182818: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.552	   14.106	   14.106	  0.116%	  0.119%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 13:58:23.182831: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   14.706	    3.862	    3.862	  0.032%	  0.151%	     0.000	        0	conv2d0_pre_relu
2018-09-10 13:58:23.182847: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   18.580	    0.426	    0.426	  0.004%	  0.155%	     0.000	        0	conv2d0
2018-09-10 13:58:23.182859: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   19.010	    2.236	    2.236	  0.018%	  0.173%	   802.816	        0	maxpool0
2018-09-10 13:58:23.182873: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   21.280	    2.148	    2.148	  0.018%	  0.191%	   802.816	        0	localresponsenorm0
2018-09-10 13:58:23.182888: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   23.441	    1.133	    1.133	  0.009%	  0.200%	   802.816	        0	conv2d1_pre_relu/conv
2018-09-10 13:58:23.182901: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   24.583	    0.298	    0.298	  0.002%	  0.203%	     0.000	        0	conv2d1_pre_relu
2018-09-10 13:58:23.182917: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   24.887	    0.148	    0.148	  0.001%	  0.204%	     0.000	        0	conv2d1
2018-09-10 13:58:23.182929: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   25.040	   13.580	   13.580	  0.112%	  0.316%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 13:58:23.182942: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   38.671	    2.030	    2.030	  0.017%	  0.332%	     0.000	        0	conv2d2_pre_relu
2018-09-10 13:58:23.182957: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   40.709	    0.316	    0.316	  0.003%	  0.335%	     0.000	        0	conv2d2
2018-09-10 13:58:23.182969: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   41.029	   15.876	   15.876	  0.131%	  0.466%	  2408.448	        0	localresponsenorm1
2018-09-10 13:58:23.182986: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   56.996	    1.590	    1.590	  0.013%	  0.479%	   602.112	        0	maxpool1
2018-09-10 13:58:23.182997: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   58.655	    1.585	    1.585	  0.013%	  0.492%	   602.112	        0	mixed3a_pool
2018-09-10 13:58:23.183010: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   59.187	    1.129	    1.129	  0.009%	  0.501%	   200.704	        0	mixed3a_1x1_pre_relu/conv
2018-09-10 13:58:23.183026: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   60.334	    0.139	    0.139	  0.001%	  0.502%	     0.000	        0	mixed3a_1x1_pre_relu
2018-09-10 13:58:23.183038: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   60.477	    0.053	    0.053	  0.000%	  0.503%	     0.000	        0	mixed3a_1x1
2018-09-10 13:58:23.183050: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   60.537	    1.109	    1.109	  0.009%	  0.512%	   301.056	        0	mixed3a_3x3_bottleneck_pre_relu/conv
2018-09-10 13:58:23.183066: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   61.654	    0.195	    0.195	  0.002%	  0.513%	     0.000	        0	mixed3a_3x3_bottleneck_pre_relu
2018-09-10 13:58:23.183078: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   61.853	    0.072	    0.072	  0.001%	  0.514%	     0.000	        0	mixed3a_3x3_bottleneck
2018-09-10 13:58:23.183094: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   65.665	    0.691	    0.691	  0.006%	  0.520%	    50.176	        0	mixed3a_5x5_bottleneck_pre_relu/conv
2018-09-10 13:58:23.183107: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   66.377	    0.050	    0.050	  0.000%	  0.520%	     0.000	        0	mixed3a_5x5_bottleneck_pre_relu
2018-09-10 13:58:23.183119: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   66.431	    0.022	    0.022	  0.000%	  0.520%	     0.000	        0	mixed3a_5x5_bottleneck
2018-09-10 13:58:23.183134: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   66.457	    2.014	    2.014	  0.017%	  0.537%	   100.352	        0	mixed3a_5x5_pre_relu/conv
2018-09-10 13:58:23.183146: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   60.250	    8.268	    8.268	  0.068%	  0.605%	   100.352	        0	mixed3a_pool_reduce_pre_relu/conv
2018-09-10 13:58:23.183163: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   68.478	    0.065	    0.065	  0.001%	  0.606%	     0.000	        0	mixed3a_5x5_pre_relu
2018-09-10 13:58:23.183175: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   68.528	    0.035	    0.035	  0.000%	  0.606%	     0.000	        0	mixed3a_pool_reduce_pre_relu
2018-09-10 13:58:23.183188: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   68.548	    0.028	    0.028	  0.000%	  0.606%	     0.000	        0	mixed3a_5x5
2018-09-10 13:58:23.183203: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   68.567	    0.021	    0.021	  0.000%	  0.606%	     0.000	        0	mixed3a_pool_reduce
2018-09-10 13:58:23.183215: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   61.929	   24.205	   24.205	  0.199%	  0.806%	   401.408	        0	mixed3a_3x3_pre_relu/conv
2018-09-10 13:58:23.183227: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   86.175	    0.243	    0.243	  0.002%	  0.808%	     0.000	        0	mixed3a_3x3_pre_relu
2018-09-10 13:58:23.183242: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   86.424	    0.128	    0.128	  0.001%	  0.809%	     0.000	        0	mixed3a_3x3
2018-09-10 13:58:23.183254: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   86.557	    0.271	    0.271	  0.002%	  0.811%	   802.816	        0	mixed3a
2018-09-10 13:58:23.183270: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   86.885	    1.651	    1.651	  0.014%	  0.824%	   802.816	        0	mixed3b_pool
2018-09-10 13:58:23.183282: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   88.545	    1.100	    1.100	  0.009%	  0.834%	   200.704	        0	mixed3b_pool_reduce_pre_relu/conv
2018-09-10 13:58:23.183295: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   89.657	    0.211	    0.211	  0.002%	  0.835%	     0.000	        0	mixed3b_pool_reduce_pre_relu
2018-09-10 13:58:23.183311: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   89.874	    0.051	    0.051	  0.000%	  0.836%	     0.000	        0	mixed3b_pool_reduce
2018-09-10 13:58:23.183323: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   89.643	    1.742	    1.742	  0.014%	  0.850%	   100.352	        0	mixed3b_5x5_bottleneck_pre_relu/conv
2018-09-10 13:58:23.183339: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   89.218	    2.148	    2.148	  0.018%	  0.868%	   401.408	        0	mixed3b_3x3_bottleneck_pre_relu/conv
2018-09-10 13:58:23.183351: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   91.390	    0.067	    0.067	  0.001%	  0.868%	     0.000	        0	mixed3b_5x5_bottleneck_pre_relu
2018-09-10 13:58:23.183366: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   91.463	    0.029	    0.029	  0.000%	  0.869%	     0.000	        0	mixed3b_5x5_bottleneck
2018-09-10 13:58:23.183378: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   91.420	    1.047	    1.047	  0.009%	  0.877%	     0.000	        0	mixed3b_3x3_bottleneck_pre_relu
2018-09-10 13:58:23.183393: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   92.474	    0.977	    0.977	  0.008%	  0.885%	     0.000	        0	mixed3b_3x3_bottleneck
2018-09-10 13:58:23.183405: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   86.883	   16.883	   16.883	  0.139%	  1.024%	   401.408	        0	mixed3b_1x1_pre_relu/conv
2018-09-10 13:58:23.183420: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  103.811	    0.338	    0.338	  0.003%	  1.027%	     0.000	        0	mixed3b_1x1_pre_relu
2018-09-10 13:58:23.183433: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  104.155	    0.117	    0.117	  0.001%	  1.028%	     0.000	        0	mixed3b_1x1
2018-09-10 13:58:23.192703: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   91.495	   13.281	   13.281	  0.109%	  1.137%	   301.056	        0	mixed3b_5x5_pre_relu/conv
2018-09-10 13:58:23.192805: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  104.812	    0.191	    0.191	  0.002%	  1.139%	     0.000	        0	mixed3b_5x5_pre_relu
2018-09-10 13:58:23.192826: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  105.007	    0.073	    0.073	  0.001%	  1.140%	     0.000	        0	mixed3b_5x5
2018-09-10 13:58:23.192846: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   93.456	   13.332	   13.332	  0.110%	  1.249%	   602.112	        0	mixed3b_3x3_pre_relu/conv
2018-09-10 13:58:23.192867: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  106.815	    0.531	    0.531	  0.004%	  1.254%	     0.000	        0	mixed3b_3x3_pre_relu
2018-09-10 13:58:23.192887: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  107.351	    0.163	    0.163	  0.001%	  1.255%	     0.000	        0	mixed3b_3x3
2018-09-10 13:58:23.192907: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	  107.518	    0.246	    0.246	  0.002%	  1.257%	  1505.280	        0	mixed3b
2018-09-10 13:58:23.192927: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	  107.775	    0.856	    0.856	  0.007%	  1.264%	   376.320	        0	maxpool4
2018-09-10 13:58:23.192948: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	  112.280	    0.771	    0.771	  0.006%	  1.270%	   376.320	        0	mixed4a_pool
2018-09-10 13:58:23.192968: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	  113.066	    1.028	    1.028	  0.008%	  1.279%	    50.176	        0	mixed4a_pool_reduce_pre_relu/conv
2018-09-10 13:58:23.192988: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  114.101	    0.049	    0.049	  0.000%	  1.279%	     0.000	        0	mixed4a_pool_reduce_pre_relu
2018-09-10 13:58:23.193009: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  114.156	    0.021	    0.021	  0.000%	  1.279%	     0.000	        0	mixed4a_pool_reduce
2018-09-10 13:58:23.193028: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	  114.183	    0.649	    0.649	  0.005%	  1.285%	    12.544	        0	mixed4a_5x5_bottleneck_pre_relu/conv
2018-09-10 13:58:23.193048: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  114.837	    0.016	    0.016	  0.000%	  1.285%	     0.000	        0	mixed4a_5x5_bottleneck_pre_relu
2018-09-10 13:58:23.193068: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  114.856	    0.009	    0.009	  0.000%	  1.285%	     0.000	        0	mixed4a_5x5_bottleneck
2018-09-10 13:58:23.193087: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	  113.874	    2.021	    2.021	  0.017%	  1.302%	    75.264	        0	mixed4a_3x3_bottleneck_pre_relu/conv
2018-09-10 13:58:23.193107: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  115.915	    0.060	    0.060	  0.000%	  1.302%	     0.000	        0	mixed4a_3x3_bottleneck_pre_relu
2018-09-10 13:58:23.193127: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  115.980	    0.066	    0.066	  0.001%	  1.303%	     0.000	        0	mixed4a_3x3_bottleneck
2018-09-10 13:58:23.193147: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	  114.870	    2.234	    2.234	  0.018%	  1.321%	    37.632	        0	mixed4a_5x5_pre_relu/conv
2018-09-10 13:58:23.193168: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  117.113	    0.031	    0.031	  0.000%	  1.321%	     0.000	        0	mixed4a_5x5_pre_relu
2018-09-10 13:58:23.193188: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  117.148	    0.018	    0.018	  0.000%	  1.322%	     0.000	        0	mixed4a_5x5
2018-09-10 13:58:23.193207: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	  113.680	    4.673	    4.673	  0.038%	  1.360%	   150.528	        0	mixed4a_1x1_pre_relu/conv
2018-09-10 13:58:23.193227: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  118.387	    0.096	    0.096	  0.001%	  1.361%	     0.000	        0	mixed4a_1x1_pre_relu
2018-09-10 13:58:23.202816: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  118.487	    0.040	    0.040	  0.000%	  1.361%	     0.000	        0	mixed4a_1x1
2018-09-10 13:58:23.202860: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	  116.050	    2.612	    2.612	  0.022%	  1.383%	   159.936	        0	mixed4a_3x3_pre_relu/conv
2018-09-10 13:58:23.202880: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  118.672	    0.098	    0.098	  0.001%	  1.383%	     0.000	        0	mixed4a_3x3_pre_relu
2018-09-10 13:58:23.202899: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  118.774	    0.041	    0.041	  0.000%	  1.384%	     0.000	        0	mixed4a_3x3
2018-09-10 13:58:23.202918: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	  118.820	   20.041	   20.041	  0.165%	  1.549%	   398.272	        0	mixed4a
2018-09-10 13:58:23.202937: I tensorflow/core/util/stat_summarizer.cc:85] 	                 AvgPool	  138.925	    0.265	    0.265	  0.002%	  1.551%	    32.512	        0	head0_pool
2018-09-10 13:58:23.202955: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	  139.195	    0.237	    0.237	  0.002%	  1.553%	     8.192	        0	head0_bottleneck_pre_relu/conv
2018-09-10 13:58:23.202974: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  139.437	    0.024	    0.024	  0.000%	  1.553%	     0.000	        0	head0_bottleneck_pre_relu
2018-09-10 13:58:23.202993: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  139.468	    0.011	    0.011	  0.000%	  1.553%	     0.000	        0	head0_bottleneck
2018-09-10 13:58:23.203012: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Reshape	  139.483	    0.014	    0.014	  0.000%	  1.553%	     0.000	        0	head0_bottleneck/reshape
2018-09-10 13:58:23.203031: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	  139.501	    1.206	    1.206	  0.010%	  1.563%	     4.096	        0	nn0_pre_relu/matmul
2018-09-10 13:58:23.203050: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  140.734	    0.032	    0.032	  0.000%	  1.564%	     0.000	        0	nn0_pre_relu
2018-09-10 13:58:23.203069: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	  140.770	    0.011	    0.011	  0.000%	  1.564%	     0.000	        0	nn0
2018-09-10 13:58:23.203088: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	  140.785	    0.689	    0.689	  0.006%	  1.569%	     4.032	        0	softmax0_pre_activation/matmul
2018-09-10 13:58:23.203107: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	  141.484	    0.014	    0.014	  0.000%	  1.569%	     0.000	        0	softmax0_pre_activation
2018-09-10 13:58:23.203126: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Softmax	  141.502	    0.040	    0.040	  0.000%	  1.570%	     0.000	        0	softmax0
2018-09-10 13:58:23.203144: I tensorflow/core/util/stat_summarizer.cc:85] 	                Identity	  141.545	    0.008	    0.008	  0.000%	  1.570%	     0.000	        0	output
2018-09-10 13:58:23.203163: I tensorflow/core/util/stat_summarizer.cc:85] 	                 _Retval	  141.556	    0.015	    0.015	  0.000%	  1.570%	     0.000	        0	_retval_output_0_0
2018-09-10 13:58:23.203181: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 13:58:23.203195: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Top by Computation Time ==============================
2018-09-10 13:58:23.203210: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 13:58:23.222534: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   61.929	   24.205	   24.205	  0.199%	  0.199%	   401.408	        0	mixed3a_3x3_pre_relu/conv
2018-09-10 13:58:23.222648: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	  118.820	   20.041	   20.041	  0.165%	  0.364%	   398.272	        0	mixed4a
2018-09-10 13:58:23.222672: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   86.883	   16.883	   16.883	  0.139%	  0.503%	   401.408	        0	mixed3b_1x1_pre_relu/conv
2018-09-10 13:58:23.222692: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   41.029	   15.876	   15.876	  0.131%	  0.634%	  2408.448	        0	localresponsenorm1
2018-09-10 13:58:23.222716: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.552	   14.106	   14.106	  0.116%	  0.750%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 13:58:23.222735: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   25.040	   13.580	   13.580	  0.112%	  0.862%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 13:58:23.222758: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   93.456	   13.332	   13.332	  0.110%	  0.972%	   602.112	        0	mixed3b_3x3_pre_relu/conv
2018-09-10 13:58:23.222782: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   91.495	   13.281	   13.281	  0.109%	  1.081%	   301.056	        0	mixed3b_5x5_pre_relu/conv
2018-09-10 13:58:23.222801: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   60.250	    8.268	    8.268	  0.068%	  1.149%	   100.352	        0	mixed3a_pool_reduce_pre_relu/conv
2018-09-10 13:58:23.222820: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	  113.680	    4.673	    4.673	  0.038%	  1.188%	   150.528	        0	mixed4a_1x1_pre_relu/conv
2018-09-10 13:58:23.222843: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 13:58:23.222857: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Top by Memory Use ==============================
2018-09-10 13:58:23.222876: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 13:58:23.222895: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.552	   14.106	   14.106	  0.116%	  0.116%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 13:58:23.222919: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   41.029	   15.876	   15.876	  0.131%	  0.247%	  2408.448	        0	localresponsenorm1
2018-09-10 13:58:23.222938: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   25.040	   13.580	   13.580	  0.112%	  0.359%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 13:58:23.222961: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	  107.518	    0.246	    0.246	  0.002%	  0.361%	  1505.280	        0	mixed3b
2018-09-10 13:58:23.222985: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   86.885	    1.651	    1.651	  0.014%	  0.374%	   802.816	        0	mixed3b_pool
2018-09-10 13:58:23.223004: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   86.557	    0.271	    0.271	  0.002%	  0.377%	   802.816	        0	mixed3a
2018-09-10 13:58:23.223027: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   21.280	    2.148	    2.148	  0.018%	  0.394%	   802.816	        0	localresponsenorm0
2018-09-10 13:58:23.223047: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   19.010	    2.236	    2.236	  0.018%	  0.413%	   802.816	        0	maxpool0
2018-09-10 13:58:23.223070: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   23.441	    1.133	    1.133	  0.009%	  0.422%	   802.816	        0	conv2d1_pre_relu/conv
2018-09-10 13:58:23.223094: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   58.655	    1.585	    1.585	  0.013%	  0.435%	   602.112	        0	mixed3a_pool
2018-09-10 13:58:23.223113: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 13:58:23.223127: I tensorflow/core/util/stat_summarizer.cc:85] Number of nodes executed: 139
2018-09-10 13:58:23.223145: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Summary by node type ==============================
2018-09-10 13:58:23.223160: I tensorflow/core/util/stat_summarizer.cc:85] 	             [Node type]	  [count]	  [avg ms]	    [avg %]	    [cdf %]	  [mem KB]	[times called]
2018-09-10 13:58:23.223182: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	       22	     1.746	    68.097%	    68.097%	 10077.888	        0
2018-09-10 13:58:23.223201: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	        3	     0.280	    10.920%	    79.017%	  2706.368	        0
2018-09-10 13:58:23.223224: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	        2	     0.246	     9.594%	    88.612%	  3211.264	        0
2018-09-10 13:58:23.223246: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	       24	     0.120	     4.680%	    93.292%	     0.000	        0
2018-09-10 13:58:23.223264: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	        6	     0.115	     4.485%	    97.777%	  3562.496	        0
2018-09-10 13:58:23.223291: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	       23	     0.029	     1.131%	    98.908%	     0.000	        0
2018-09-10 13:58:23.223309: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	        2	     0.025	     0.975%	    99.883%	     8.128	        0
2018-09-10 13:58:23.223331: I tensorflow/core/util/stat_summarizer.cc:85] 	                 AvgPool	        1	     0.003	     0.117%	   100.000%	    32.512	        0
2018-09-10 13:58:23.223353: I tensorflow/core/util/stat_summarizer.cc:85] 	                 _Retval	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:58:23.223376: I tensorflow/core/util/stat_summarizer.cc:85] 	                    _Arg	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:58:23.223394: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Softmax	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:58:23.223418: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Reshape	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:58:23.223439: I tensorflow/core/util/stat_summarizer.cc:85] 	                    NoOp	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:58:23.223462: I tensorflow/core/util/stat_summarizer.cc:85] 	                Identity	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:58:23.223484: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	       50	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:58:23.223501: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 13:58:23.223517: I tensorflow/core/util/stat_summarizer.cc:85] Timings (microseconds): count=73 first=190643 curr=169194 min=100704 max=288910 avg=166348 std=43610
2018-09-10 13:58:23.223536: I tensorflow/core/util/stat_summarizer.cc:85] Memory (bytes): count=73 curr=19598656(all same)
2018-09-10 13:58:23.223551: I tensorflow/core/util/stat_summarizer.cc:85] 139 nodes observed
2018-09-10 13:58:23.223568: I tensorflow/core/util/stat_summarizer.cc:85] 
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

Generic result
```
~/p/t/tensorflow ❯❯❯ ./tf_benchmark_model --graph=../tensorflow_inception_graph.pb
2018-09-10 13:35:00.566391: I tensorflow/tools/benchmark/benchmark_model.cc:469] Graph: [../tensorflow_inception_graph.pb]
2018-09-10 13:35:00.566443: I tensorflow/tools/benchmark/benchmark_model.cc:470] Init ops:
2018-09-10 13:35:00.566450: I tensorflow/tools/benchmark/benchmark_model.cc:471] Input layers: [input:0]
2018-09-10 13:35:00.566473: I tensorflow/tools/benchmark/benchmark_model.cc:472] Input shapes: [1,224,224,3]
2018-09-10 13:35:00.566477: I tensorflow/tools/benchmark/benchmark_model.cc:473] Input types: [float]
2018-09-10 13:35:00.566501: I tensorflow/tools/benchmark/benchmark_model.cc:474] Output layers: [output:0]
2018-09-10 13:35:00.566505: I tensorflow/tools/benchmark/benchmark_model.cc:475] Target layers: []
2018-09-10 13:35:00.566526: I tensorflow/tools/benchmark/benchmark_model.cc:476] Num runs: [1000]
2018-09-10 13:35:00.566530: I tensorflow/tools/benchmark/benchmark_model.cc:477] Inter-inference delay (seconds): [-1.0]
2018-09-10 13:35:00.566533: I tensorflow/tools/benchmark/benchmark_model.cc:478] Inter-benchmark delay (seconds): [-1.0]
2018-09-10 13:35:00.566537: I tensorflow/tools/benchmark/benchmark_model.cc:480] Num threads: [-1]
2018-09-10 13:35:00.566552: I tensorflow/tools/benchmark/benchmark_model.cc:481] Benchmark name: []
2018-09-10 13:35:00.566556: I tensorflow/tools/benchmark/benchmark_model.cc:482] Output prefix: []
2018-09-10 13:35:00.566559: I tensorflow/tools/benchmark/benchmark_model.cc:483] Show sizes: [0]
2018-09-10 13:35:00.566583: I tensorflow/tools/benchmark/benchmark_model.cc:484] Warmup runs: [1]
2018-09-10 13:35:00.566588: I tensorflow/tools/benchmark/benchmark_model.cc:251] Loading TensorFlow.
2018-09-10 13:35:00.566634: I tensorflow/tools/benchmark/benchmark_model.cc:258] Got config, 0 devices
2018-09-10 13:35:00.566701: I tensorflow/core/platform/cpu_feature_guard.cc:141] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE3 SSE4.1 SSE4.2 AVX AVX2 FMA
2018-09-10 13:35:00.851509: I tensorflow/tools/benchmark/benchmark_model.cc:496] Initialized session in 0.284906s
2018-09-10 13:35:00.851590: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1 iterations, max -1 seconds without detailed stat logging, with -1s sleep between inferences
2018-09-10 13:35:01.144975: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=1 curr=293039

2018-09-10 13:35:01.145026: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1000 iterations, max 10 seconds without detailed stat logging, with -1s sleep between inferences
2018-09-10 13:35:11.172809: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=231 first=41242 curr=43227 min=38544 max=49124 avg=43366.3 std=1377

2018-09-10 13:35:11.172841: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1000 iterations, max 10 seconds with detailed stat logging, with -1s sleep between inferences
2018-09-10 13:35:21.429339: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=221 first=47383 curr=45277 min=43205 max=49968 avg=45429.5 std=991

2018-09-10 13:35:21.429370: I tensorflow/tools/benchmark/benchmark_model.cc:600] Average inference timings in us: Warmup: 293039, no stats: 43366, with stats: 45429
2018-09-10 13:35:21.430305: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Run Order ==============================
2018-09-10 13:35:21.430314: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 13:35:21.430320: I tensorflow/core/util/stat_summarizer.cc:85] 	                    NoOp	    0.000	    0.006	    0.006	  0.000%	  0.000%	     0.000	        0	_SOURCE
2018-09-10 13:35:21.430325: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.013	    0.012	    0.012	  0.000%	  0.000%	     0.000	        0	mixed4a_1x1_w
2018-09-10 13:35:21.430330: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.026	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_1x1_b
2018-09-10 13:35:21.430335: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.029	    0.003	    0.003	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_bottleneck_w
2018-09-10 13:35:21.430339: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.033	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_bottleneck_b
2018-09-10 13:35:21.430344: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.035	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_w
2018-09-10 13:35:21.430349: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.038	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_b
2018-09-10 13:35:21.430357: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.040	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_5x5_bottleneck_w
2018-09-10 13:35:21.430363: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.043	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed4a_5x5_bottleneck_b
2018-09-10 13:35:21.430368: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.045	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_5x5_w
2018-09-10 13:35:21.430374: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.048	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed4a_5x5_b
2018-09-10 13:35:21.430406: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.050	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_1x1_w
2018-09-10 13:35:21.430413: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.053	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_1x1_b
2018-09-10 13:35:21.430419: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.056	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3b_3x3_bottleneck_w
2018-09-10 13:35:21.430426: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.058	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3b_3x3_bottleneck_b
2018-09-10 13:35:21.430444: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.060	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_3x3_w
2018-09-10 13:35:21.430468: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.063	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3b_3x3_b
2018-09-10 13:35:21.430475: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.065	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3b_5x5_bottleneck_w
2018-09-10 13:35:21.430494: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.067	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3b_5x5_bottleneck_b
2018-09-10 13:35:21.430519: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.069	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_5x5_w
2018-09-10 13:35:21.430526: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.072	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3b_5x5_b
2018-09-10 13:35:21.430533: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.074	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a/concat_dim
2018-09-10 13:35:21.430552: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.077	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3a_1x1_w
2018-09-10 13:35:21.430577: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.079	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3a_1x1_b
2018-09-10 13:35:21.430583: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.081	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3a_3x3_bottleneck_w
2018-09-10 13:35:21.430592: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.083	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3a_3x3_bottleneck_b
2018-09-10 13:35:21.430601: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.085	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3a_3x3_w
2018-09-10 13:35:21.430609: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.088	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3a_3x3_b
2018-09-10 13:35:21.430617: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.090	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3a_5x5_bottleneck_w
2018-09-10 13:35:21.430624: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.092	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3a_5x5_bottleneck_b
2018-09-10 13:35:21.430630: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.095	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3a_5x5_w
2018-09-10 13:35:21.430638: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.097	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3a_5x5_b
2018-09-10 13:35:21.430646: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.099	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	conv2d0_w
2018-09-10 13:35:21.430654: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.101	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	conv2d0_b
2018-09-10 13:35:21.430662: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.103	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	conv2d1_w
2018-09-10 13:35:21.430670: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.105	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	conv2d1_b
2018-09-10 13:35:21.430678: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.107	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	conv2d2_w
2018-09-10 13:35:21.430686: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.109	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	conv2d2_b
2018-09-10 13:35:21.430693: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.111	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed3a_pool_reduce_w
2018-09-10 13:35:21.430704: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.114	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	mixed3a_pool_reduce_b
2018-09-10 13:35:21.430712: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.116	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed3b_pool_reduce_w
2018-09-10 13:35:21.430720: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.118	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed3b_pool_reduce_b
2018-09-10 13:35:21.430730: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.120	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed4a_pool_reduce_w
2018-09-10 13:35:21.430740: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.123	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	mixed4a_pool_reduce_b
2018-09-10 13:35:21.430747: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.125	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	head0_bottleneck_w
2018-09-10 13:35:21.430755: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.127	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	head0_bottleneck_b
2018-09-10 13:35:21.430763: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.129	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	head0_bottleneck/reshape/shape
2018-09-10 13:35:21.430773: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.131	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	nn0_w
2018-09-10 13:35:21.430783: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.134	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	nn0_b
2018-09-10 13:35:21.430793: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.136	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	softmax0_w
2018-09-10 13:35:21.430800: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.138	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	softmax0_b
2018-09-10 13:35:21.430809: I tensorflow/core/util/stat_summarizer.cc:85] 	                    _Arg	    0.140	    0.003	    0.003	  0.000%	  0.001%	     0.000	        0	_arg_input_0_0
2018-09-10 13:35:21.430818: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.144	    5.922	    5.922	  0.040%	  0.041%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 13:35:21.430825: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	    6.077	    0.281	    0.281	  0.002%	  0.043%	     0.000	        0	conv2d0_pre_relu
2018-09-10 13:35:21.430835: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	    6.363	    0.088	    0.088	  0.001%	  0.043%	     0.000	        0	conv2d0
2018-09-10 13:35:21.430842: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	    6.454	    0.746	    0.746	  0.005%	  0.048%	   802.816	        0	maxpool0
2018-09-10 13:35:21.430850: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	    7.207	    0.734	    0.734	  0.005%	  0.053%	   802.816	        0	localresponsenorm0
2018-09-10 13:35:21.430857: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    7.944	    0.581	    0.581	  0.004%	  0.057%	   802.816	        0	conv2d1_pre_relu/conv
2018-09-10 13:35:21.430867: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	    8.528	    0.117	    0.117	  0.001%	  0.058%	     0.000	        0	conv2d1_pre_relu
2018-09-10 13:35:21.430874: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	    8.647	    0.034	    0.034	  0.000%	  0.058%	     0.000	        0	conv2d1
2018-09-10 13:35:21.430882: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    8.682	   12.030	   12.030	  0.081%	  0.139%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 13:35:21.430889: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   20.728	    0.210	    0.210	  0.001%	  0.141%	     0.000	        0	conv2d2_pre_relu
2018-09-10 13:35:21.430897: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   20.940	    0.073	    0.073	  0.000%	  0.141%	     0.000	        0	conv2d2
2018-09-10 13:35:21.430904: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   21.015	    4.092	    4.092	  0.028%	  0.169%	  2408.448	        0	localresponsenorm1
2018-09-10 13:35:21.430913: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   25.119	    0.413	    0.413	  0.003%	  0.172%	   602.112	        0	maxpool1
2018-09-10 13:35:21.430922: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   25.545	    0.353	    0.353	  0.002%	  0.174%	   602.112	        0	mixed3a_pool
2018-09-10 13:35:21.430929: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   25.547	    0.877	    0.877	  0.006%	  0.180%	   200.704	        0	mixed3a_1x1_pre_relu/conv
2018-09-10 13:35:21.430937: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   26.428	    0.052	    0.052	  0.000%	  0.180%	     0.000	        0	mixed3a_1x1_pre_relu
2018-09-10 13:35:21.430952: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   26.482	    0.014	    0.014	  0.000%	  0.181%	     0.000	        0	mixed3a_1x1
2018-09-10 13:35:21.430960: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   25.552	    1.144	    1.144	  0.008%	  0.188%	    50.176	        0	mixed3a_5x5_bottleneck_pre_relu/conv
2018-09-10 13:35:21.430971: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   26.700	    0.015	    0.015	  0.000%	  0.188%	     0.000	        0	mixed3a_5x5_bottleneck_pre_relu
2018-09-10 13:35:21.430980: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   26.717	    0.006	    0.006	  0.000%	  0.188%	     0.000	        0	mixed3a_5x5_bottleneck
2018-09-10 13:35:21.430988: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   25.902	    0.972	    0.972	  0.007%	  0.195%	   100.352	        0	mixed3a_pool_reduce_pre_relu/conv
2018-09-10 13:35:21.430996: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   26.879	    0.028	    0.028	  0.000%	  0.195%	     0.000	        0	mixed3a_pool_reduce_pre_relu
2018-09-10 13:35:21.431004: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   26.909	    0.008	    0.008	  0.000%	  0.195%	     0.000	        0	mixed3a_pool_reduce
2018-09-10 13:35:21.431012: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   25.549	    1.557	    1.557	  0.011%	  0.206%	   301.056	        0	mixed3a_3x3_bottleneck_pre_relu/conv
2018-09-10 13:35:21.431021: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   27.112	    0.074	    0.074	  0.001%	  0.206%	     0.000	        0	mixed3a_3x3_bottleneck_pre_relu
2018-09-10 13:35:21.431030: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   27.188	    0.018	    0.018	  0.000%	  0.206%	     0.000	        0	mixed3a_3x3_bottleneck
2018-09-10 13:35:21.431038: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   26.724	    2.810	    2.810	  0.019%	  0.225%	   100.352	        0	mixed3a_5x5_pre_relu/conv
2018-09-10 13:35:21.431047: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   29.544	    0.035	    0.035	  0.000%	  0.226%	     0.000	        0	mixed3a_5x5_pre_relu
2018-09-10 13:35:21.431055: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   29.580	    0.010	    0.010	  0.000%	  0.226%	     0.000	        0	mixed3a_5x5
2018-09-10 13:35:21.431062: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   27.207	    3.818	    3.818	  0.026%	  0.251%	   401.408	        0	mixed3a_3x3_pre_relu/conv
2018-09-10 13:35:21.431070: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   31.041	    0.051	    0.051	  0.000%	  0.252%	     0.000	        0	mixed3a_3x3_pre_relu
2018-09-10 13:35:21.431079: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   31.094	    0.021	    0.021	  0.000%	  0.252%	     0.000	        0	mixed3a_3x3
2018-09-10 13:35:21.431086: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   31.116	    0.103	    0.103	  0.001%	  0.253%	   802.816	        0	mixed3a
2018-09-10 13:35:21.431095: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   31.232	    0.481	    0.481	  0.003%	  0.256%	   802.816	        0	mixed3b_pool
2018-09-10 13:35:21.431102: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   31.236	    1.390	    1.390	  0.009%	  0.265%	   401.408	        0	mixed3b_3x3_bottleneck_pre_relu/conv
2018-09-10 13:35:21.431110: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   31.733	    1.520	    1.520	  0.010%	  0.276%	   100.352	        0	mixed3b_5x5_bottleneck_pre_relu/conv
2018-09-10 13:35:21.431118: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   33.258	    0.046	    0.046	  0.000%	  0.276%	     0.000	        0	mixed3b_5x5_bottleneck_pre_relu
2018-09-10 13:35:21.431125: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   33.305	    0.011	    0.011	  0.000%	  0.276%	     0.000	        0	mixed3b_5x5_bottleneck
2018-09-10 13:35:21.431133: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   32.635	    0.770	    0.770	  0.005%	  0.281%	     0.000	        0	mixed3b_3x3_bottleneck_pre_relu
2018-09-10 13:35:21.431141: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   33.407	    0.613	    0.613	  0.004%	  0.285%	     0.000	        0	mixed3b_3x3_bottleneck
2018-09-10 13:35:21.431149: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   31.717	    2.379	    2.379	  0.016%	  0.301%	   200.704	        0	mixed3b_pool_reduce_pre_relu/conv
2018-09-10 13:35:21.431156: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   34.110	    0.053	    0.053	  0.000%	  0.302%	     0.000	        0	mixed3b_pool_reduce_pre_relu
2018-09-10 13:35:21.431164: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   34.165	    0.015	    0.015	  0.000%	  0.302%	     0.000	        0	mixed3b_pool_reduce
2018-09-10 13:35:21.431173: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   31.231	    3.585	    3.585	  0.024%	  0.326%	   401.408	        0	mixed3b_1x1_pre_relu/conv
2018-09-10 13:35:21.431182: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   34.823	    0.109	    0.109	  0.001%	  0.327%	     0.000	        0	mixed3b_1x1_pre_relu
2018-09-10 13:35:21.431188: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   34.933	    0.566	    0.566	  0.004%	  0.331%	     0.000	        0	mixed3b_1x1
2018-09-10 13:35:21.431196: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   33.318	    7.903	    7.903	  0.053%	  0.384%	   301.056	        0	mixed3b_5x5_pre_relu/conv
2018-09-10 13:35:21.431204: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   41.237	    0.102	    0.102	  0.001%	  0.385%	     0.000	        0	mixed3b_5x5_pre_relu
2018-09-10 13:35:21.431212: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   41.342	    0.027	    0.027	  0.000%	  0.385%	     0.000	        0	mixed3b_5x5
2018-09-10 13:35:21.431219: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   34.023	    8.699	    8.699	  0.059%	  0.444%	   602.112	        0	mixed3b_3x3_pre_relu/conv
2018-09-10 13:35:21.431226: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   42.737	    0.082	    0.082	  0.001%	  0.444%	     0.000	        0	mixed3b_3x3_pre_relu
2018-09-10 13:35:21.431234: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   42.821	    0.033	    0.033	  0.000%	  0.444%	     0.000	        0	mixed3b_3x3
2018-09-10 13:35:21.431243: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   42.856	    0.161	    0.161	  0.001%	  0.446%	  1505.280	        0	mixed3b
2018-09-10 13:35:21.431250: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   43.021	    0.229	    0.229	  0.002%	  0.447%	   376.320	        0	maxpool4
2018-09-10 13:35:21.431257: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   43.262	    0.169	    0.169	  0.001%	  0.448%	   376.320	        0	mixed4a_pool
2018-09-10 13:35:21.431264: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   43.268	    0.293	    0.293	  0.002%	  0.450%	    12.544	        0	mixed4a_5x5_bottleneck_pre_relu/conv
2018-09-10 13:35:21.431272: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   43.564	    0.007	    0.007	  0.000%	  0.450%	     0.000	        0	mixed4a_5x5_bottleneck_pre_relu
2018-09-10 13:35:21.431281: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   43.573	    0.003	    0.003	  0.000%	  0.450%	     0.000	        0	mixed4a_5x5_bottleneck
2018-09-10 13:35:21.431287: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   43.578	    0.612	    0.612	  0.004%	  0.454%	    37.632	        0	mixed4a_5x5_pre_relu/conv
2018-09-10 13:35:21.431295: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   44.193	    0.012	    0.012	  0.000%	  0.455%	     0.000	        0	mixed4a_5x5_pre_relu
2018-09-10 13:35:21.431303: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   44.206	    0.004	    0.004	  0.000%	  0.455%	     0.000	        0	mixed4a_5x5
2018-09-10 13:35:21.431312: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   43.434	    0.920	    0.920	  0.006%	  0.461%	    50.176	        0	mixed4a_pool_reduce_pre_relu/conv
2018-09-10 13:35:21.431320: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   44.356	    0.014	    0.014	  0.000%	  0.461%	     0.000	        0	mixed4a_pool_reduce_pre_relu
2018-09-10 13:35:21.431327: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   44.371	    0.005	    0.005	  0.000%	  0.461%	     0.000	        0	mixed4a_pool_reduce
2018-09-10 13:35:21.431336: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   43.265	    1.383	    1.383	  0.009%	  0.470%	    75.264	        0	mixed4a_3x3_bottleneck_pre_relu/conv
2018-09-10 13:35:21.431344: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   43.264	    1.387	    1.387	  0.009%	  0.480%	   150.528	        0	mixed4a_1x1_pre_relu/conv
2018-09-10 13:35:21.431352: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   44.650	    0.020	    0.020	  0.000%	  0.480%	     0.000	        0	mixed4a_3x3_bottleneck_pre_relu
2018-09-10 13:35:21.431360: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   44.672	    0.006	    0.006	  0.000%	  0.480%	     0.000	        0	mixed4a_3x3_bottleneck
2018-09-10 13:35:21.431368: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   44.656	    0.040	    0.040	  0.000%	  0.480%	     0.000	        0	mixed4a_1x1_pre_relu
2018-09-10 13:35:21.431375: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   44.697	    0.012	    0.012	  0.000%	  0.480%	     0.000	        0	mixed4a_1x1
2018-09-10 13:35:21.431385: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   44.679	    1.293	    1.293	  0.009%	  0.489%	   159.936	        0	mixed4a_3x3_pre_relu/conv
2018-09-10 13:35:21.431392: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   45.979	    0.043	    0.043	  0.000%	  0.489%	     0.000	        0	mixed4a_3x3_pre_relu
2018-09-10 13:35:21.431399: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   46.024	    0.011	    0.011	  0.000%	  0.489%	     0.000	        0	mixed4a_3x3
2018-09-10 13:35:21.431407: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   46.036	    0.051	    0.051	  0.000%	  0.490%	   398.272	        0	mixed4a
2018-09-10 13:35:21.431415: I tensorflow/core/util/stat_summarizer.cc:85] 	                 AvgPool	   46.091	    0.064	    0.064	  0.000%	  0.490%	    32.512	        0	head0_pool
2018-09-10 13:35:21.431423: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   46.156	    0.170	    0.170	  0.001%	  0.491%	     8.192	        0	head0_bottleneck_pre_relu/conv
2018-09-10 13:35:21.431433: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   46.328	    0.004	    0.004	  0.000%	  0.491%	     0.000	        0	head0_bottleneck_pre_relu
2018-09-10 13:35:21.431441: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   46.334	    0.003	    0.003	  0.000%	  0.491%	     0.000	        0	head0_bottleneck
2018-09-10 13:35:21.431450: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Reshape	   46.338	    0.003	    0.003	  0.000%	  0.491%	     0.000	        0	head0_bottleneck/reshape
2018-09-10 13:35:21.431458: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	   46.342	    0.568	    0.568	  0.004%	  0.495%	     4.096	        0	nn0_pre_relu/matmul
2018-09-10 13:35:21.431466: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   46.912	    0.004	    0.004	  0.000%	  0.495%	     0.000	        0	nn0_pre_relu
2018-09-10 13:35:21.431475: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   46.917	    0.002	    0.002	  0.000%	  0.495%	     0.000	        0	nn0
2018-09-10 13:35:21.431482: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	   46.920	    0.248	    0.248	  0.002%	  0.497%	     4.032	        0	softmax0_pre_activation/matmul
2018-09-10 13:35:21.431490: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   47.170	    0.003	    0.003	  0.000%	  0.497%	     0.000	        0	softmax0_pre_activation
2018-09-10 13:35:21.431499: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Softmax	   47.174	    0.031	    0.031	  0.000%	  0.497%	     0.000	        0	softmax0
2018-09-10 13:35:21.431507: I tensorflow/core/util/stat_summarizer.cc:85] 	                Identity	   47.206	    0.002	    0.002	  0.000%	  0.497%	     0.000	        0	output
2018-09-10 13:35:21.431515: I tensorflow/core/util/stat_summarizer.cc:85] 	                 _Retval	   47.209	    0.004	    0.004	  0.000%	  0.497%	     0.000	        0	_retval_output_0_0
2018-09-10 13:35:21.431524: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 13:35:21.431528: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Top by Computation Time ==============================
2018-09-10 13:35:21.431532: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 13:35:21.431540: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    8.682	   12.030	   12.030	  0.081%	  0.081%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 13:35:21.431547: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   34.023	    8.699	    8.699	  0.059%	  0.140%	   602.112	        0	mixed3b_3x3_pre_relu/conv
2018-09-10 13:35:21.431555: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   33.318	    7.903	    7.903	  0.053%	  0.194%	   301.056	        0	mixed3b_5x5_pre_relu/conv
2018-09-10 13:35:21.431562: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.144	    5.922	    5.922	  0.040%	  0.234%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 13:35:21.431569: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   21.015	    4.092	    4.092	  0.028%	  0.261%	  2408.448	        0	localresponsenorm1
2018-09-10 13:35:21.431577: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   27.207	    3.818	    3.818	  0.026%	  0.287%	   401.408	        0	mixed3a_3x3_pre_relu/conv
2018-09-10 13:35:21.431584: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   31.231	    3.585	    3.585	  0.024%	  0.311%	   401.408	        0	mixed3b_1x1_pre_relu/conv
2018-09-10 13:35:21.431592: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   26.724	    2.810	    2.810	  0.019%	  0.330%	   100.352	        0	mixed3a_5x5_pre_relu/conv
2018-09-10 13:35:21.431599: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   31.717	    2.379	    2.379	  0.016%	  0.346%	   200.704	        0	mixed3b_pool_reduce_pre_relu/conv
2018-09-10 13:35:21.431608: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   25.549	    1.557	    1.557	  0.011%	  0.357%	   301.056	        0	mixed3a_3x3_bottleneck_pre_relu/conv
2018-09-10 13:35:21.431615: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 13:35:21.431619: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Top by Memory Use ==============================
2018-09-10 13:35:21.431623: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 13:35:21.431632: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.144	    5.922	    5.922	  0.040%	  0.040%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 13:35:21.431641: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	   21.015	    4.092	    4.092	  0.028%	  0.068%	  2408.448	        0	localresponsenorm1
2018-09-10 13:35:21.431648: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    8.682	   12.030	   12.030	  0.081%	  0.149%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 13:35:21.431659: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   42.856	    0.161	    0.161	  0.001%	  0.150%	  1505.280	        0	mixed3b
2018-09-10 13:35:21.431666: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   31.232	    0.481	    0.481	  0.003%	  0.153%	   802.816	        0	mixed3b_pool
2018-09-10 13:35:21.431674: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   31.116	    0.103	    0.103	  0.001%	  0.154%	   802.816	        0	mixed3a
2018-09-10 13:35:21.431683: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    7.944	    0.581	    0.581	  0.004%	  0.158%	   802.816	        0	conv2d1_pre_relu/conv
2018-09-10 13:35:21.431691: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	    7.207	    0.734	    0.734	  0.005%	  0.163%	   802.816	        0	localresponsenorm0
2018-09-10 13:35:21.431699: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	    6.454	    0.746	    0.746	  0.005%	  0.168%	   802.816	        0	maxpool0
2018-09-10 13:35:21.431707: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   25.545	    0.353	    0.353	  0.002%	  0.170%	   602.112	        0	mixed3a_pool
2018-09-10 13:35:21.431714: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 13:35:21.431717: I tensorflow/core/util/stat_summarizer.cc:85] Number of nodes executed: 139
2018-09-10 13:35:21.431721: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Summary by node type ==============================
2018-09-10 13:35:21.431725: I tensorflow/core/util/stat_summarizer.cc:85] 	             [Node type]	  [count]	  [avg ms]	    [avg %]	    [cdf %]	  [mem KB]	[times called]
2018-09-10 13:35:21.431733: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	       22	     0.266	    86.928%	    86.928%	 10077.888	        0
2018-09-10 13:35:21.431742: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	        2	     0.021	     6.863%	    93.791%	  3211.264	        0
2018-09-10 13:35:21.431750: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	        6	     0.008	     2.614%	    96.405%	  3562.496	        0
2018-09-10 13:35:21.431756: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	       23	     0.004	     1.307%	    97.712%	     0.000	        0
2018-09-10 13:35:21.431762: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	       24	     0.004	     1.307%	    99.020%	     0.000	        0
2018-09-10 13:35:21.431768: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	        2	     0.003	     0.980%	   100.000%	     8.128	        0
2018-09-10 13:35:21.431776: I tensorflow/core/util/stat_summarizer.cc:85] 	                 _Retval	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:35:21.431782: I tensorflow/core/util/stat_summarizer.cc:85] 	                    _Arg	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:35:21.431788: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Softmax	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:35:21.431794: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Reshape	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:35:21.431800: I tensorflow/core/util/stat_summarizer.cc:85] 	                    NoOp	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:35:21.431806: I tensorflow/core/util/stat_summarizer.cc:85] 	                Identity	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:35:21.431813: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	       50	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 13:35:21.431819: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	        3	     0.000	     0.000%	   100.000%	  2706.368	        0
2018-09-10 13:35:21.431826: I tensorflow/core/util/stat_summarizer.cc:85] 	                 AvgPool	        1	     0.000	     0.000%	   100.000%	    32.512	        0
2018-09-10 13:35:21.431832: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 13:35:21.431836: I tensorflow/core/util/stat_summarizer.cc:85] Timings (microseconds): count=221 first=73549 curr=67956 min=60535 max=80945 avg=66953.4 std=3017
2018-09-10 13:35:21.431840: I tensorflow/core/util/stat_summarizer.cc:85] Memory (bytes): count=221 curr=19598656(all same)
2018-09-10 13:35:21.431844: I tensorflow/core/util/stat_summarizer.cc:85] 139 nodes observed
2018-09-10 13:35:21.431848: I tensorflow/core/util/stat_summarizer.cc:85] 
```
