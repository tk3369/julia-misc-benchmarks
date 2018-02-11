Not surprising - Preallocating IOBuffer is must faster than allocating it every time.

```
julia> function echo1(str)
         buf = IOBuffer()
         print(buf, str)
         String(take!(buf))
       end
echo1 (generic function with 1 method)

julia> function echo2(str, buf)
         seek(buf, 0)
         print(buf, str)
         String(take!(buf))
       end
echo2 (generic function with 1 method)

julia> buf = IOBuffer(100)
IOBuffer(data=UInt8[...], readable=true, writable=true, seekable=true, append=false, size=0, maxsize=100, ptr=1, mark=-1)

julia> @benchmark echo1("hello")
BenchmarkTools.Trial: 
  memory estimate:  288 bytes
  allocs estimate:  5
  --------------
  minimum time:     132.526 ns (0.00% GC)
  median time:      139.373 ns (0.00% GC)
  mean time:        168.664 ns (11.62% GC)
  maximum time:     2.320 μs (88.46% GC)
  --------------
  samples:          10000
  evals/sample:     896

julia> @benchmark echo2("hello", $buf)
BenchmarkTools.Trial: 
  memory estimate:  192 bytes
  allocs estimate:  2
  --------------
  minimum time:     88.046 ns (0.00% GC)
  median time:      92.252 ns (0.00% GC)
  mean time:        108.072 ns (10.18% GC)
  maximum time:     1.844 μs (88.63% GC)
  --------------
  samples:          10000
  evals/sample:     957
```
