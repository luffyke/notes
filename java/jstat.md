## jstat

```
jstat -gc PID
```
```
S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT
5632.0 5632.0 5186.2  0.0   163328.0 57383.6   349696.0   96410.6   82816.0 80404.2 8832.0 8317.1    748    6.075  102    22.827   28.902
```
- S0C S0区容量，单位字节，下同
- S1C S1区容量
- S0U S0区使用
- S1U S1区使用
- EC Eden区容量
- EU Eden区使用
- OC 老年代容量
- OU 老年代使用
- MC Metaspace容量
- MU Metaspace使用
- CCSC Compressed class space容量
- CCSU Compressed class space使用
- YGC Young GC次数
- YGCT Young GC总耗时，单位s，下同
- FGC Full GC次数
- FGCT Full GC总耗时
- GCT GC总耗时
