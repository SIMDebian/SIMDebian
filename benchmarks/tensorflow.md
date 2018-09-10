tensorflow test result
===

Unfortunately TF doesn't support runtime code dispatch according
to CPU capacity. Hence recompiling TF with SIMD enabled is expected
to bring performance boost.

The exception is when TF is built against MKL, MKL is able to
dynamically select code according to the CPU.

I5-7440HQ: -march=native, ~10x performance boost

# [amd64/CPU] generic code / native code, benchmarking with inceptionv5

Source
> https://salsa.debian.org/science-team/tensorflow
> b7252d13d59ebecae7928daa25af3a5ab1289bae

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

march=native
```
~/p/t/tensorflow ❯❯❯ ./tf_benchmark_model --graph=../tensorflow_inception_graph.pb
2018-09-10 16:03:10.709451: I tensorflow/tools/benchmark/benchmark_model.cc:469] Graph: [../tensorflow_inception_graph.pb]
2018-09-10 16:03:10.709490: I tensorflow/tools/benchmark/benchmark_model.cc:470] Init ops:
2018-09-10 16:03:10.709495: I tensorflow/tools/benchmark/benchmark_model.cc:471] Input layers: [input:0]
2018-09-10 16:03:10.709499: I tensorflow/tools/benchmark/benchmark_model.cc:472] Input shapes: [1,224,224,3]
2018-09-10 16:03:10.709502: I tensorflow/tools/benchmark/benchmark_model.cc:473] Input types: [float]
2018-09-10 16:03:10.709505: I tensorflow/tools/benchmark/benchmark_model.cc:474] Output layers: [output:0]
2018-09-10 16:03:10.709508: I tensorflow/tools/benchmark/benchmark_model.cc:475] Target layers: []
2018-09-10 16:03:10.709515: I tensorflow/tools/benchmark/benchmark_model.cc:476] Num runs: [1000]
2018-09-10 16:03:10.709518: I tensorflow/tools/benchmark/benchmark_model.cc:477] Inter-inference delay (seconds): [-1.0]
2018-09-10 16:03:10.709522: I tensorflow/tools/benchmark/benchmark_model.cc:478] Inter-benchmark delay (seconds): [-1.0]
2018-09-10 16:03:10.709525: I tensorflow/tools/benchmark/benchmark_model.cc:480] Num threads: [-1]
2018-09-10 16:03:10.709528: I tensorflow/tools/benchmark/benchmark_model.cc:481] Benchmark name: []
2018-09-10 16:03:10.709531: I tensorflow/tools/benchmark/benchmark_model.cc:482] Output prefix: []
2018-09-10 16:03:10.709535: I tensorflow/tools/benchmark/benchmark_model.cc:483] Show sizes: [0]
2018-09-10 16:03:10.709538: I tensorflow/tools/benchmark/benchmark_model.cc:484] Warmup runs: [1]
2018-09-10 16:03:10.709560: I tensorflow/tools/benchmark/benchmark_model.cc:251] Loading TensorFlow.
2018-09-10 16:03:10.709566: I tensorflow/tools/benchmark/benchmark_model.cc:258] Got config, 0 devices
2018-09-10 16:03:10.773621: I tensorflow/tools/benchmark/benchmark_model.cc:496] Initialized session in 0.064025s
2018-09-10 16:03:10.773726: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1 iterations, max -1 seconds without detailed stat logging, with -1s sleep between inferences
2018-09-10 16:03:10.999520: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=1 curr=225469

2018-09-10 16:03:10.999572: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1000 iterations, max 10 seconds without detailed stat logging, with -1s sleep between inferences
2018-09-10 16:03:21.028048: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=618 first=19309 curr=16199 min=13342 max=20241 avg=16181.9 std=919

2018-09-10 16:03:21.028082: I tensorflow/tools/benchmark/benchmark_model.cc:327] Running benchmark for max 1000 iterations, max 10 seconds with detailed stat logging, with -1s sleep between inferences
2018-09-10 16:03:31.681492: I tensorflow/tools/benchmark/benchmark_model.cc:361] count=573 first=19017 curr=17118 min=15818 max=23959 avg=17474 std=991

2018-09-10 16:03:31.681528: I tensorflow/tools/benchmark/benchmark_model.cc:600] Average inference timings in us: Warmup: 225469, no stats: 16181, with stats: 17474
2018-09-10 16:03:31.682683: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Run Order ==============================
2018-09-10 16:03:31.682695: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 16:03:31.682702: I tensorflow/core/util/stat_summarizer.cc:85] 	                    NoOp	    0.000	    0.007	    0.007	  0.000%	  0.000%	     0.000	        0	_SOURCE
2018-09-10 16:03:31.682708: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.015	    0.011	    0.011	  0.000%	  0.000%	     0.000	        0	mixed4a_1x1_w
2018-09-10 16:03:31.682714: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.028	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_1x1_b
2018-09-10 16:03:31.682721: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.031	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_bottleneck_w
2018-09-10 16:03:31.682731: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.034	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_bottleneck_b
2018-09-10 16:03:31.682741: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.037	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_w
2018-09-10 16:03:31.682748: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.039	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_3x3_b
2018-09-10 16:03:31.682756: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.042	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_5x5_bottleneck_w
2018-09-10 16:03:31.682763: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.045	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed4a_5x5_bottleneck_b
2018-09-10 16:03:31.682771: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.047	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_5x5_w
2018-09-10 16:03:31.682778: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.049	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a_5x5_b
2018-09-10 16:03:31.682787: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.052	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_1x1_w
2018-09-10 16:03:31.682794: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.055	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_1x1_b
2018-09-10 16:03:31.682803: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.058	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_3x3_bottleneck_w
2018-09-10 16:03:31.682811: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.060	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_3x3_bottleneck_b
2018-09-10 16:03:31.682821: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.063	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_3x3_w
2018-09-10 16:03:31.682829: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.065	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_3x3_b
2018-09-10 16:03:31.682837: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.068	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_5x5_bottleneck_w
2018-09-10 16:03:31.682847: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.070	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_5x5_bottleneck_b
2018-09-10 16:03:31.682855: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.073	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_5x5_w
2018-09-10 16:03:31.682864: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.075	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3b_5x5_b
2018-09-10 16:03:31.682871: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.077	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed4a/concat_dim
2018-09-10 16:03:31.682883: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.080	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3a_1x1_w
2018-09-10 16:03:31.682891: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.082	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3a_1x1_b
2018-09-10 16:03:31.682900: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.085	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3a_3x3_bottleneck_w
2018-09-10 16:03:31.682909: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.088	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3a_3x3_bottleneck_b
2018-09-10 16:03:31.682917: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.090	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3a_3x3_w
2018-09-10 16:03:31.682926: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.092	    0.002	    0.002	  0.000%	  0.000%	     0.000	        0	mixed3a_3x3_b
2018-09-10 16:03:31.682935: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.095	    0.001	    0.001	  0.000%	  0.000%	     0.000	        0	mixed3a_5x5_bottleneck_w
2018-09-10 16:03:31.682943: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.097	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed3a_5x5_bottleneck_b
2018-09-10 16:03:31.682953: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.099	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed3a_5x5_w
2018-09-10 16:03:31.682961: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.102	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	mixed3a_5x5_b
2018-09-10 16:03:31.682972: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.104	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	conv2d0_w
2018-09-10 16:03:31.682980: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.107	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	conv2d0_b
2018-09-10 16:03:31.682989: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.109	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	conv2d1_w
2018-09-10 16:03:31.683001: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.111	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	conv2d1_b
2018-09-10 16:03:31.683012: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.114	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	conv2d2_w
2018-09-10 16:03:31.683021: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.116	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	conv2d2_b
2018-09-10 16:03:31.683030: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.119	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed3a_pool_reduce_w
2018-09-10 16:03:31.683041: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.121	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed3a_pool_reduce_b
2018-09-10 16:03:31.683050: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.124	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed3b_pool_reduce_w
2018-09-10 16:03:31.683060: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.126	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed3b_pool_reduce_b
2018-09-10 16:03:31.683070: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.129	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	mixed4a_pool_reduce_w
2018-09-10 16:03:31.683081: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.131	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	mixed4a_pool_reduce_b
2018-09-10 16:03:31.683089: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.133	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	head0_bottleneck_w
2018-09-10 16:03:31.683099: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.136	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	head0_bottleneck_b
2018-09-10 16:03:31.683108: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.138	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	head0_bottleneck/reshape/shape
2018-09-10 16:03:31.683121: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.141	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	nn0_w
2018-09-10 16:03:31.683132: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.143	    0.002	    0.002	  0.000%	  0.001%	     0.000	        0	nn0_b
2018-09-10 16:03:31.683142: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.146	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	softmax0_w
2018-09-10 16:03:31.683150: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	    0.148	    0.001	    0.001	  0.000%	  0.001%	     0.000	        0	softmax0_b
2018-09-10 16:03:31.683161: I tensorflow/core/util/stat_summarizer.cc:85] 	                    _Arg	    0.150	    0.003	    0.003	  0.000%	  0.001%	     0.000	        0	_arg_input_0_0
2018-09-10 16:03:31.683173: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.154	    3.185	    3.185	  0.023%	  0.023%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 16:03:31.683184: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	    3.354	    0.185	    0.185	  0.001%	  0.025%	     0.000	        0	conv2d0_pre_relu
2018-09-10 16:03:31.683194: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	    3.541	    0.115	    0.115	  0.001%	  0.025%	     0.000	        0	conv2d0
2018-09-10 16:03:31.683202: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	    3.661	    0.581	    0.581	  0.004%	  0.030%	   802.816	        0	maxpool0
2018-09-10 16:03:31.683213: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	    4.249	    0.256	    0.256	  0.002%	  0.031%	   802.816	        0	localresponsenorm0
2018-09-10 16:03:31.683222: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    4.514	    0.180	    0.180	  0.001%	  0.033%	   802.816	        0	conv2d1_pre_relu/conv
2018-09-10 16:03:31.683231: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	    4.699	    0.061	    0.061	  0.000%	  0.033%	     0.000	        0	conv2d1_pre_relu
2018-09-10 16:03:31.683241: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	    4.762	    0.026	    0.026	  0.000%	  0.033%	     0.000	        0	conv2d1
2018-09-10 16:03:31.683250: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    4.790	    2.886	    2.886	  0.020%	  0.054%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 16:03:31.683258: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	    7.693	    0.140	    0.140	  0.001%	  0.055%	     0.000	        0	conv2d2_pre_relu
2018-09-10 16:03:31.683267: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	    7.837	    0.064	    0.064	  0.000%	  0.055%	     0.000	        0	conv2d2
2018-09-10 16:03:31.683275: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	    7.903	    1.459	    1.459	  0.010%	  0.065%	  2408.448	        0	localresponsenorm1
2018-09-10 16:03:31.683284: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	    9.374	    0.324	    0.324	  0.002%	  0.068%	   602.112	        0	maxpool1
2018-09-10 16:03:31.683294: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    9.781	    0.202	    0.202	  0.001%	  0.069%	    50.176	        0	mixed3a_5x5_bottleneck_pre_relu/conv
2018-09-10 16:03:31.683304: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	    9.990	    0.014	    0.014	  0.000%	  0.069%	     0.000	        0	mixed3a_5x5_bottleneck_pre_relu
2018-09-10 16:03:31.683313: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   10.006	    0.005	    0.005	  0.000%	  0.069%	     0.000	        0	mixed3a_5x5_bottleneck
2018-09-10 16:03:31.683325: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	    9.718	    0.304	    0.304	  0.002%	  0.071%	   602.112	        0	mixed3a_pool
2018-09-10 16:03:31.683334: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    9.720	    0.467	    0.467	  0.003%	  0.075%	   200.704	        0	mixed3a_1x1_pre_relu/conv
2018-09-10 16:03:31.683344: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    9.724	    0.471	    0.471	  0.003%	  0.078%	   301.056	        0	mixed3a_3x3_bottleneck_pre_relu/conv
2018-09-10 16:03:31.683354: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   10.196	    0.031	    0.031	  0.000%	  0.078%	     0.000	        0	mixed3a_1x1_pre_relu
2018-09-10 16:03:31.683368: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   10.229	    0.009	    0.009	  0.000%	  0.078%	     0.000	        0	mixed3a_1x1
2018-09-10 16:03:31.683376: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   10.201	    0.042	    0.042	  0.000%	  0.079%	     0.000	        0	mixed3a_3x3_bottleneck_pre_relu
2018-09-10 16:03:31.683387: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   10.245	    0.012	    0.012	  0.000%	  0.079%	     0.000	        0	mixed3a_3x3_bottleneck
2018-09-10 16:03:31.683396: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   10.025	    0.497	    0.497	  0.004%	  0.082%	   100.352	        0	mixed3a_pool_reduce_pre_relu/conv
2018-09-10 16:03:31.683405: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   10.524	    0.016	    0.016	  0.000%	  0.082%	     0.000	        0	mixed3a_pool_reduce_pre_relu
2018-09-10 16:03:31.683413: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   10.542	    0.005	    0.005	  0.000%	  0.082%	     0.000	        0	mixed3a_pool_reduce
2018-09-10 16:03:31.683423: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   10.013	    1.208	    1.208	  0.009%	  0.091%	   100.352	        0	mixed3a_5x5_pre_relu/conv
2018-09-10 16:03:31.683432: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   11.224	    0.016	    0.016	  0.000%	  0.091%	     0.000	        0	mixed3a_5x5_pre_relu
2018-09-10 16:03:31.683441: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   11.241	    0.006	    0.006	  0.000%	  0.091%	     0.000	        0	mixed3a_5x5
2018-09-10 16:03:31.683451: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   10.259	    1.601	    1.601	  0.011%	  0.102%	   401.408	        0	mixed3a_3x3_pre_relu/conv
2018-09-10 16:03:31.683461: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   11.875	    0.052	    0.052	  0.000%	  0.103%	     0.000	        0	mixed3a_3x3_pre_relu
2018-09-10 16:03:31.683470: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   11.930	    0.026	    0.026	  0.000%	  0.103%	     0.000	        0	mixed3a_3x3
2018-09-10 16:03:31.683479: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   11.957	    0.103	    0.103	  0.001%	  0.104%	   802.816	        0	mixed3a
2018-09-10 16:03:31.683489: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   12.072	    0.478	    0.478	  0.003%	  0.107%	   802.816	        0	mixed3b_pool
2018-09-10 16:03:31.683499: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   12.080	    0.680	    0.680	  0.005%	  0.112%	   401.408	        0	mixed3b_3x3_bottleneck_pre_relu/conv
2018-09-10 16:03:31.683510: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   12.772	    0.103	    0.103	  0.001%	  0.113%	     0.000	        0	mixed3b_3x3_bottleneck_pre_relu
2018-09-10 16:03:31.683518: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   12.577	    0.481	    0.481	  0.003%	  0.116%	   100.352	        0	mixed3b_5x5_bottleneck_pre_relu/conv
2018-09-10 16:03:31.683528: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   13.063	    0.027	    0.027	  0.000%	  0.116%	     0.000	        0	mixed3b_5x5_bottleneck_pre_relu
2018-09-10 16:03:31.683538: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   13.092	    0.007	    0.007	  0.000%	  0.116%	     0.000	        0	mixed3b_5x5_bottleneck
2018-09-10 16:03:31.683548: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   12.880	    0.271	    0.271	  0.002%	  0.118%	     0.000	        0	mixed3b_3x3_bottleneck
2018-09-10 16:03:31.683557: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   12.557	    0.766	    0.766	  0.005%	  0.124%	   200.704	        0	mixed3b_pool_reduce_pre_relu/conv
2018-09-10 16:03:31.683568: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   13.335	    0.034	    0.034	  0.000%	  0.124%	     0.000	        0	mixed3b_pool_reduce_pre_relu
2018-09-10 16:03:31.683577: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   13.371	    0.010	    0.010	  0.000%	  0.124%	     0.000	        0	mixed3b_pool_reduce
2018-09-10 16:03:31.683588: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   12.075	    1.576	    1.576	  0.011%	  0.135%	   401.408	        0	mixed3b_1x1_pre_relu/conv
2018-09-10 16:03:31.683597: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   13.662	    0.696	    0.696	  0.005%	  0.140%	     0.000	        0	mixed3b_1x1_pre_relu
2018-09-10 16:03:31.683609: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   14.377	    0.361	    0.361	  0.003%	  0.143%	     0.000	        0	mixed3b_1x1
2018-09-10 16:03:31.683617: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   13.101	    2.512	    2.512	  0.018%	  0.160%	   301.056	        0	mixed3b_5x5_pre_relu/conv
2018-09-10 16:03:31.683627: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   15.640	    0.075	    0.075	  0.001%	  0.161%	     0.000	        0	mixed3b_5x5_pre_relu
2018-09-10 16:03:31.683637: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   15.718	    0.022	    0.022	  0.000%	  0.161%	     0.000	        0	mixed3b_5x5
2018-09-10 16:03:31.683646: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   13.153	    3.454	    3.454	  0.024%	  0.185%	   602.112	        0	mixed3b_3x3_pre_relu/conv
2018-09-10 16:03:31.683656: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   16.616	    0.046	    0.046	  0.000%	  0.186%	     0.000	        0	mixed3b_3x3_pre_relu
2018-09-10 16:03:31.683666: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   16.665	    0.028	    0.028	  0.000%	  0.186%	     0.000	        0	mixed3b_3x3
2018-09-10 16:03:31.683675: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   16.695	    0.105	    0.105	  0.001%	  0.187%	  1505.280	        0	mixed3b
2018-09-10 16:03:31.683688: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   16.808	    0.171	    0.171	  0.001%	  0.188%	   376.320	        0	maxpool4
2018-09-10 16:03:31.683697: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   16.991	    0.118	    0.118	  0.001%	  0.189%	   376.320	        0	mixed4a_pool
2018-09-10 16:03:31.683710: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   16.997	    0.114	    0.114	  0.001%	  0.190%	    12.544	        0	mixed4a_5x5_bottleneck_pre_relu/conv
2018-09-10 16:03:31.683720: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   17.115	    0.006	    0.006	  0.000%	  0.190%	     0.000	        0	mixed4a_5x5_bottleneck_pre_relu
2018-09-10 16:03:31.683730: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   17.122	    0.003	    0.003	  0.000%	  0.190%	     0.000	        0	mixed4a_5x5_bottleneck
2018-09-10 16:03:31.683739: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   17.127	    0.191	    0.191	  0.001%	  0.191%	    37.632	        0	mixed4a_5x5_pre_relu/conv
2018-09-10 16:03:31.683749: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   17.321	    0.009	    0.009	  0.000%	  0.191%	     0.000	        0	mixed4a_5x5_pre_relu
2018-09-10 16:03:31.683759: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   17.331	    0.003	    0.003	  0.000%	  0.191%	     0.000	        0	mixed4a_5x5
2018-09-10 16:03:31.683769: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   17.112	    0.227	    0.227	  0.002%	  0.193%	    50.176	        0	mixed4a_pool_reduce_pre_relu/conv
2018-09-10 16:03:31.683779: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   17.341	    0.011	    0.011	  0.000%	  0.193%	     0.000	        0	mixed4a_pool_reduce_pre_relu
2018-09-10 16:03:31.683789: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   17.354	    0.004	    0.004	  0.000%	  0.193%	     0.000	        0	mixed4a_pool_reduce
2018-09-10 16:03:31.683797: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   16.994	    0.396	    0.396	  0.003%	  0.196%	    75.264	        0	mixed4a_3x3_bottleneck_pre_relu/conv
2018-09-10 16:03:31.683806: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   17.393	    0.014	    0.014	  0.000%	  0.196%	     0.000	        0	mixed4a_3x3_bottleneck_pre_relu
2018-09-10 16:03:31.683816: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   17.408	    0.005	    0.005	  0.000%	  0.196%	     0.000	        0	mixed4a_3x3_bottleneck
2018-09-10 16:03:31.683826: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   16.991	    0.498	    0.498	  0.004%	  0.199%	   150.528	        0	mixed4a_1x1_pre_relu/conv
2018-09-10 16:03:31.683837: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   17.493	    0.023	    0.023	  0.000%	  0.199%	     0.000	        0	mixed4a_1x1_pre_relu
2018-09-10 16:03:31.683846: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   17.518	    0.006	    0.006	  0.000%	  0.199%	     0.000	        0	mixed4a_1x1
2018-09-10 16:03:31.683855: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   17.414	    0.459	    0.459	  0.003%	  0.203%	   159.936	        0	mixed4a_3x3_pre_relu/conv
2018-09-10 16:03:31.683864: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   17.881	    0.030	    0.030	  0.000%	  0.203%	     0.000	        0	mixed4a_3x3_pre_relu
2018-09-10 16:03:31.683873: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   17.912	    0.008	    0.008	  0.000%	  0.203%	     0.000	        0	mixed4a_3x3
2018-09-10 16:03:31.683884: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   17.922	    0.048	    0.048	  0.000%	  0.203%	   398.272	        0	mixed4a
2018-09-10 16:03:31.683894: I tensorflow/core/util/stat_summarizer.cc:85] 	                 AvgPool	   17.975	    0.058	    0.058	  0.000%	  0.204%	    32.512	        0	head0_pool
2018-09-10 16:03:31.683904: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   18.035	    0.064	    0.064	  0.000%	  0.204%	     8.192	        0	head0_bottleneck_pre_relu/conv
2018-09-10 16:03:31.683914: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   18.101	    0.004	    0.004	  0.000%	  0.204%	     0.000	        0	head0_bottleneck_pre_relu
2018-09-10 16:03:31.683924: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   18.107	    0.003	    0.003	  0.000%	  0.204%	     0.000	        0	head0_bottleneck
2018-09-10 16:03:31.683933: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Reshape	   18.111	    0.003	    0.003	  0.000%	  0.204%	     0.000	        0	head0_bottleneck/reshape
2018-09-10 16:03:31.683943: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	   18.115	    0.464	    0.464	  0.003%	  0.208%	     4.096	        0	nn0_pre_relu/matmul
2018-09-10 16:03:31.683953: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   18.581	    0.003	    0.003	  0.000%	  0.208%	     0.000	        0	nn0_pre_relu
2018-09-10 16:03:31.683961: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	   18.585	    0.002	    0.002	  0.000%	  0.208%	     0.000	        0	nn0
2018-09-10 16:03:31.683972: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	   18.589	    0.228	    0.228	  0.002%	  0.209%	     4.032	        0	softmax0_pre_activation/matmul
2018-09-10 16:03:31.683981: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   18.819	    0.003	    0.003	  0.000%	  0.209%	     0.000	        0	softmax0_pre_activation
2018-09-10 16:03:31.683993: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Softmax	   18.823	    0.027	    0.027	  0.000%	  0.209%	     0.000	        0	softmax0
2018-09-10 16:03:31.684002: I tensorflow/core/util/stat_summarizer.cc:85] 	                Identity	   18.851	    0.001	    0.001	  0.000%	  0.209%	     0.000	        0	output
2018-09-10 16:03:31.684012: I tensorflow/core/util/stat_summarizer.cc:85] 	                 _Retval	   18.853	    0.005	    0.005	  0.000%	  0.209%	     0.000	        0	_retval_output_0_0
2018-09-10 16:03:31.684021: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 16:03:31.684026: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Top by Computation Time ==============================
2018-09-10 16:03:31.684031: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 16:03:31.684041: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   13.153	    3.454	    3.454	  0.024%	  0.024%	   602.112	        0	mixed3b_3x3_pre_relu/conv
2018-09-10 16:03:31.684051: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.154	    3.185	    3.185	  0.023%	  0.047%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 16:03:31.684062: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    4.790	    2.886	    2.886	  0.020%	  0.067%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 16:03:31.684072: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   13.101	    2.512	    2.512	  0.018%	  0.085%	   301.056	        0	mixed3b_5x5_pre_relu/conv
2018-09-10 16:03:31.684085: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   10.259	    1.601	    1.601	  0.011%	  0.097%	   401.408	        0	mixed3a_3x3_pre_relu/conv
2018-09-10 16:03:31.684094: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   12.075	    1.576	    1.576	  0.011%	  0.108%	   401.408	        0	mixed3b_1x1_pre_relu/conv
2018-09-10 16:03:31.684104: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	    7.903	    1.459	    1.459	  0.010%	  0.118%	  2408.448	        0	localresponsenorm1
2018-09-10 16:03:31.684115: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   10.013	    1.208	    1.208	  0.009%	  0.127%	   100.352	        0	mixed3a_5x5_pre_relu/conv
2018-09-10 16:03:31.684124: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   12.557	    0.766	    0.766	  0.005%	  0.132%	   200.704	        0	mixed3b_pool_reduce_pre_relu/conv
2018-09-10 16:03:31.684133: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	   13.662	    0.696	    0.696	  0.005%	  0.137%	     0.000	        0	mixed3b_1x1_pre_relu
2018-09-10 16:03:31.684141: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 16:03:31.684146: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Top by Memory Use ==============================
2018-09-10 16:03:31.684151: I tensorflow/core/util/stat_summarizer.cc:85] 	             [node type]	  [start]	  [first]	 [avg ms]	     [%]	  [cdf%]	  [mem KB]	[times called]	[Name]
2018-09-10 16:03:31.684160: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    0.154	    3.185	    3.185	  0.023%	  0.023%	  3211.264	        0	conv2d0_pre_relu/conv
2018-09-10 16:03:31.684170: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    4.790	    2.886	    2.886	  0.020%	  0.043%	  2408.448	        0	conv2d2_pre_relu/conv
2018-09-10 16:03:31.684180: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	    7.903	    1.459	    1.459	  0.010%	  0.053%	  2408.448	        0	localresponsenorm1
2018-09-10 16:03:31.684188: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   16.695	    0.105	    0.105	  0.001%	  0.054%	  1505.280	        0	mixed3b
2018-09-10 16:03:31.684198: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	    4.514	    0.180	    0.180	  0.001%	  0.055%	   802.816	        0	conv2d1_pre_relu/conv
2018-09-10 16:03:31.684207: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	    4.249	    0.256	    0.256	  0.002%	  0.057%	   802.816	        0	localresponsenorm0
2018-09-10 16:03:31.684215: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	   12.072	    0.478	    0.478	  0.003%	  0.060%	   802.816	        0	mixed3b_pool
2018-09-10 16:03:31.684224: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	   11.957	    0.103	    0.103	  0.001%	  0.061%	   802.816	        0	mixed3a
2018-09-10 16:03:31.684234: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	    3.661	    0.581	    0.581	  0.004%	  0.065%	   802.816	        0	maxpool0
2018-09-10 16:03:31.684244: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	   13.153	    3.454	    3.454	  0.024%	  0.090%	   602.112	        0	mixed3b_3x3_pre_relu/conv
2018-09-10 16:03:31.684254: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 16:03:31.684258: I tensorflow/core/util/stat_summarizer.cc:85] Number of nodes executed: 139
2018-09-10 16:03:31.684263: I tensorflow/core/util/stat_summarizer.cc:85] ============================== Summary by node type ==============================
2018-09-10 16:03:31.684268: I tensorflow/core/util/stat_summarizer.cc:85] 	             [Node type]	  [count]	  [avg ms]	    [avg %]	    [cdf %]	  [mem KB]	[times called]
2018-09-10 16:03:31.684276: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Conv2D	       22	     0.028	    87.500%	    87.500%	 10077.888	        0
2018-09-10 16:03:31.684284: I tensorflow/core/util/stat_summarizer.cc:85] 	                     LRN	        2	     0.002	     6.250%	    93.750%	  3211.264	        0
2018-09-10 16:03:31.684292: I tensorflow/core/util/stat_summarizer.cc:85] 	                 MaxPool	        6	     0.001	     3.125%	    96.875%	  3562.496	        0
2018-09-10 16:03:31.684303: I tensorflow/core/util/stat_summarizer.cc:85] 	                 BiasAdd	       24	     0.001	     3.125%	   100.000%	     0.000	        0
2018-09-10 16:03:31.684310: I tensorflow/core/util/stat_summarizer.cc:85] 	                 _Retval	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 16:03:31.684318: I tensorflow/core/util/stat_summarizer.cc:85] 	                    _Arg	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 16:03:31.684328: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Softmax	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 16:03:31.684335: I tensorflow/core/util/stat_summarizer.cc:85] 	                 Reshape	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 16:03:31.684343: I tensorflow/core/util/stat_summarizer.cc:85] 	                    Relu	       23	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 16:03:31.684351: I tensorflow/core/util/stat_summarizer.cc:85] 	                    NoOp	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 16:03:31.684359: I tensorflow/core/util/stat_summarizer.cc:85] 	                  MatMul	        2	     0.000	     0.000%	   100.000%	     8.128	        0
2018-09-10 16:03:31.684367: I tensorflow/core/util/stat_summarizer.cc:85] 	                Identity	        1	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 16:03:31.684378: I tensorflow/core/util/stat_summarizer.cc:85] 	                   Const	       50	     0.000	     0.000%	   100.000%	     0.000	        0
2018-09-10 16:03:31.684386: I tensorflow/core/util/stat_summarizer.cc:85] 	                  Concat	        3	     0.000	     0.000%	   100.000%	  2706.368	        0
2018-09-10 16:03:31.684395: I tensorflow/core/util/stat_summarizer.cc:85] 	                 AvgPool	        1	     0.000	     0.000%	   100.000%	    32.512	        0
2018-09-10 16:03:31.684403: I tensorflow/core/util/stat_summarizer.cc:85] 
2018-09-10 16:03:31.684407: I tensorflow/core/util/stat_summarizer.cc:85] Timings (microseconds): count=573 first=29600 curr=24149 min=21413 max=33478 avg=24661.1 std=1769
2018-09-10 16:03:31.684413: I tensorflow/core/util/stat_summarizer.cc:85] Memory (bytes): count=573 curr=19598656(all same)
2018-09-10 16:03:31.684418: I tensorflow/core/util/stat_summarizer.cc:85] 139 nodes observed
2018-09-10 16:03:31.684422: I tensorflow/core/util/stat_summarizer.cc:85] 
```
