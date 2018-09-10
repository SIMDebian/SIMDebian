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
