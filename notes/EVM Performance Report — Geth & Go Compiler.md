# EVM Performance Report — Geth & Go Compiler

In this report we compare performance of Geth EVM compiled with go 1.16 and 1.17 on the linux/amd64 architecture. We expect to see a difference because go 1.17 is [_passing function arguments and results using registers instead of the stack_](https://go.dev/doc/go1.17#compiler) on this architecture.

We used the latest Go toolchains from 1.16.x and 1.17.x series. The benchmark suite, tools and hardware are the same as in [the previous report](https://notes.ethereum.org/@ipsilon/evm-performance-report-geth-vs-evmone):
- [geth](https://github.com/ethereum/go-ethereum) [1.10.12](https://github.com/ethereum/go-ethereum/releases/tag/v1.10.12) released 2021-11-08
- [go 1.16.10](https://go.dev/doc/devel/release#go1.16) released 2021-11-04
- [go 1.17.3](https://go.dev/doc/devel/release#go1.17) released 2021-11-04
- [evmone's benchmark suite](https://github.com/ethereum/evmone/tree/master/test/benchmarks)
- Intel Haswell CPU [i7-4790K](https://ark.intel.com/content/www/us/en/ark/products/80807/intel-core-i74790k-processor-8m-cache-up-to-4-40-ghz.html) with turbo frequency 4.4 GHz

### go 1.16 vs go 1.17 (times)
```
name                                    1.16 time    1.17 time    delta 
micro/memory_grow_mload/nogrow         295µs ± 0%   250µs ± 1%  -15.21%  (p=0.000 n=10+10)
micro/memory_grow_mload/by1            303µs ± 0%   258µs ± 1%  -14.74%  (p=0.000 n=10+10)
micro/memory_grow_mload/by16           369µs ± 0%   328µs ± 1%  -11.13%  (p=0.000 n=10+10)
micro/memory_grow_mload/by32           449µs ± 1%   405µs ± 1%   -9.95%  (p=0.000 n=10+9) 
micro/signextend/zero                  433µs ± 1%   462µs ± 0%   +6.60%  (p=0.000 n=9+8)  
micro/signextend/one                   442µs ± 1%   473µs ± 1%   +6.95%  (p=0.000 n=10+10)
micro/loop_with_many_jumpdests/empty  3.58µs ± 1%  3.39µs ± 1%   -5.13%  (p=0.000 n=10+9) 
micro/jump_around/empty                180µs ± 0%   156µs ± 1%  -13.42%  (p=0.000 n=10+10)
micro/memory_grow_mstore/nogrow        530µs ± 0%   486µs ± 1%   -8.41%  (p=0.000 n=10+10)
micro/memory_grow_mstore/by1           541µs ± 1%   495µs ± 0%   -8.54%  (p=0.000 n=10+10)
micro/memory_grow_mstore/by16          625µs ± 0%   588µs ± 0%   -5.90%  (p=0.000 n=9+10) 
micro/memory_grow_mstore/by32          694µs ± 0%   653µs ± 0%   -5.87%  (p=0.000 n=10+9) 
micro/JUMPDEST_n0/empty               4.03ms ± 1%  3.50ms ± 0%  -13.21%  (p=0.000 n=10+10)
main/sha1_divs/empty                   221µs ± 0%   223µs ± 1%   +0.98%  (p=0.000 n=10+10)
main/sha1_divs/1351                   4.44ms ± 0%  4.49ms ± 0%   +1.20%  (p=0.000 n=8+10) 
main/sha1_divs/2737                   8.63ms ± 0%  8.72ms ± 0%   +1.11%  (p=0.000 n=10+9) 
main/sha1_divs/5311                   16.9ms ± 1%  17.1ms ± 0%   +1.14%  (p=0.000 n=10+9) 
main/weierstrudel/0                    491µs ± 0%   497µs ± 1%   +1.29%  (p=0.000 n=8+10) 
main/weierstrudel/1                   1.04ms ± 0%  1.06ms ± 0%   +1.88%  (p=0.000 n=10+9) 
main/weierstrudel/3                   1.64ms ± 0%  1.66ms ± 0%   +1.52%  (p=0.000 n=10+10)
main/weierstrudel/9                   3.42ms ± 1%  3.47ms ± 0%   +1.24%  (p=0.000 n=9+9)  
main/weierstrudel/14                  4.91ms ± 0%  4.95ms ± 0%   +0.84%  (p=0.000 n=10+8) 
main/sha1_shifts/empty                 179µs ± 1%   175µs ± 0%   -2.29%  (p=0.000 n=10+9) 
main/sha1_shifts/1351                 3.66ms ± 1%  3.58ms ± 1%   -2.36%  (p=0.000 n=10+10)
main/sha1_shifts/2737                 7.14ms ± 1%  6.98ms ± 1%   -2.33%  (p=0.000 n=7+10) 
main/sha1_shifts/5311                 14.0ms ± 1%  13.6ms ± 1%   -2.36%  (p=0.000 n=10+10)
main/blake2b_huff/empty                111µs ± 0%   109µs ± 1%   -2.03%  (p=0.000 n=10+10)
main/blake2b_huff/2805nulls           2.14ms ± 1%  2.06ms ± 0%   -3.62%  (p=0.000 n=10+10)
main/blake2b_huff/5610nulls           4.15ms ± 0%  4.00ms ± 0%   -3.47%  (p=0.000 n=10+10)
main/blake2b_huff/8415nulls           6.05ms ± 0%  5.85ms ± 1%   -3.34%  (p=0.000 n=9+10)
main/blake2b_shifts/2805nulls         16.9ms ± 1%  16.6ms ± 1%   -1.68%  (p=0.000 n=10+10)
main/blake2b_shifts/5610nulls         33.6ms ± 2%  33.2ms ± 1%   -1.14%  (p=0.001 n=10+10)
main/blake2b_shifts/8415nulls         50.0ms ± 1%  49.5ms ± 1%   -1.05%  (p=0.000 n=10+10)
[Geo mean]                            1.35ms       1.30ms        -3.58%
```

### go 1.16 vs go 1.17 (gas rate)
```
name                                   1.16 gas/s   1.17 gas/s    delta
micro/memory_grow_mload/nogrow          195M ± 0%    229M ± 1%  +17.93%  (p=0.000 n=10+10)
micro/memory_grow_mload/by1             191M ± 0%    224M ± 1%  +17.28%  (p=0.000 n=10+10)
micro/memory_grow_mload/by16            195M ± 0%    219M ± 1%  +12.53%  (p=0.000 n=10+10)
micro/memory_grow_mload/by32            228M ± 1%    253M ± 1%  +11.05%  (p=0.000 n=10+9)
micro/signextend/zero                   222M ± 1%    208M ± 0%   -6.19%  (p=0.000 n=9+8)
micro/signextend/one                    217M ± 1%    203M ± 1%   -6.50%  (p=0.000 n=10+10)
micro/loop_with_many_jumpdests/empty   8.95M ± 1%   9.43M ± 1%   +5.40%  (p=0.000 n=10+9)
micro/jump_around/empty                 272M ± 0%    315M ± 1%  +15.50%  (p=0.000 n=10+10)
micro/memory_grow_mstore/nogrow         116M ± 0%    127M ± 1%   +9.18%  (p=0.000 n=10+10)
micro/memory_grow_mstore/by1            114M ± 1%    125M ± 0%   +9.34%  (p=0.000 n=10+10)
micro/memory_grow_mstore/by16           121M ± 0%    129M ± 0%   +6.27%  (p=0.000 n=9+10)
micro/memory_grow_mstore/by32           154M ± 0%    163M ± 0%   +6.23%  (p=0.000 n=10+9)
micro/JUMPDEST_n0/empty                 131M ± 1%    151M ± 0%  +15.22%  (p=0.000 n=10+10)
main/sha1_divs/empty                    165M ± 0%    164M ± 1%   -0.97%  (p=0.000 n=10+10)
main/sha1_divs/1351                     177M ± 0%    175M ± 0%   -1.18%  (p=0.000 n=8+10)
main/sha1_divs/2737                     178M ± 0%    176M ± 0%   -1.10%  (p=0.000 n=10+9)
main/sha1_divs/5311                     177M ± 1%    175M ± 0%   -1.13%  (p=0.000 n=10+9)
main/weierstrudel/0                    96.9M ± 0%   95.7M ± 1%   -1.27%  (p=0.000 n=8+10)
main/weierstrudel/1                     125M ± 0%    123M ± 0%   -1.85%  (p=0.000 n=10+9)
main/weierstrudel/3                     123M ± 0%    122M ± 0%   -1.50%  (p=0.000 n=10+10)
main/weierstrudel/9                     122M ± 1%    121M ± 0%   -1.22%  (p=0.000 n=9+9)
main/weierstrudel/14                    122M ± 0%    121M ± 0%   -0.84%  (p=0.000 n=10+8)
main/sha1_shifts/empty                  196M ± 1%    201M ± 0%   +2.34%  (p=0.000 n=10+9)
main/sha1_shifts/1351                   208M ± 1%    213M ± 1%   +2.41%  (p=0.000 n=10+10)
main/sha1_shifts/2737                   208M ± 1%    213M ± 1%   +2.39%  (p=0.000 n=7+10)
main/sha1_shifts/5311                   207M ± 1%    212M ± 1%   +2.42%  (p=0.000 n=10+10)
main/blake2b_huff/empty                 126M ± 0%    128M ± 1%   +2.08%  (p=0.000 n=10+10)
main/blake2b_huff/2805nulls             143M ± 1%    149M ± 0%   +3.76%  (p=0.000 n=10+10)
main/blake2b_huff/5610nulls             145M ± 0%    150M ± 0%   +3.60%  (p=0.000 n=10+10)
main/blake2b_huff/8415nulls             145M ± 0%    150M ± 1%   +3.45%  (p=0.000 n=9+10)
main/blake2b_shifts/2805nulls           202M ± 1%    206M ± 1%   +1.70%  (p=0.000 n=10+10)
main/blake2b_shifts/5610nulls           221M ± 2%    224M ± 1%   +1.15%  (p=0.001 n=10+10)
main/blake2b_shifts/8415nulls           241M ± 1%    243M ± 1%   +1.07%  (p=0.000 n=10+10)
[Geo mean]                              152M         157M        +3.71%
```
