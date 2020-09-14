---
layout: post
title: Once upon a "view" 
snippet: Things about data slicing.
tags: [julia]
---

Not long ago, I tried to avoid plain data slicing at all cost. For example, I would not write
```julia
y = x[:, 1]
x[1, :] = y
```
because I thought slicing the data is an evil act, in the sense that it creates a data copy and thus incurs unnecessary memory allocation. But yesterday, I realized I was wrong.

Just to make sure we are on the same page, data slicing refers to taking a slice of a dataset, such as taking a row from a matrix, or a matrix from a vector of matrices and so on. For illustration, let's say ```x``` is a 100-by-2-by-2 array and we want to calculate the column-wise mean for the first 100-by-2 matrix. 
```julia
x = rand(100, 2, 2);
```
If we use
```julia
function colmeans_slice(x::Array, i::Int)
    mean(x[:, :, i], dims = 1)
end
```
then Julia would first create a copy of the slice ```x[:, :, i]``` before calculating the mean. Since creating a copy incurs memory allocation, this approach makes the code slower. 

A more efficient approach uses a "view", which is **an array object that acts like a data slice, but references the data of the original array in-place**, and thus avoids unnecessary memory allocation. You can use "view" as a function (```view(x, :, :, 1)```), or as a macro (simply put ```@views``` before the function definition, which will make all data slices inside the function "views"). 

```julia
@views function colmeans_view(x::Array, i::Int)
    mean(x[:, :, i], dims = 1)
end
```
The following benchmark result shows that using "view" can avoid most of the memory allocation, and thus make the code much faster.
```julia
julia> @benchmark colmeans_slice(x, 1)
BenchmarkTools.Trial: 
  memory estimate:  2.83 KiB
  allocs estimate:  8
  --------------
  minimum time:     1.153 μs (0.00% GC)
  median time:      1.211 μs (0.00% GC)
  mean time:        1.346 μs (3.07% GC)
  maximum time:     86.419 μs (95.88% GC)
  --------------
  samples:          10000
  evals/sample:     10

julia> @benchmark colmeans_view(x, 1) 
BenchmarkTools.Trial: 
  memory estimate:  160 bytes
  allocs estimate:  2
  --------------
  minimum time:     161.744 ns (0.00% GC)
  median time:      165.135 ns (0.00% GC)
  mean time:        174.935 ns (2.85% GC)
  maximum time:     2.348 μs (91.14% GC)
  --------------
  samples:          10000
  evals/sample:     772
```
Note that even with "view", there is still a small amount of memory being allocated, but that's because I'm using Julia 1.3. With the latest release of Julia 1.5, the second approach should not allocate any memory. See [this](https://docs.julialang.org/en/v1.5-dev/NEWS/#Compiler/Runtime-improvements-1).

Now back to the story at the beginning. I thought data slicing always creates a copy, but that is not the case. In fact, there is one important exception and that's when data slicing is on the left of the equal sign. Let ```a = ones(100, 2)```, then ```x[:, :, 1] = a``` will be done in-place and there is no memory allocation.
```julia
julia> @benchmark x[:, :, 1] = a
BenchmarkTools.Trial: 
  memory estimate:  80 bytes
  allocs estimate:  3
  --------------
  minimum time:     671.089 ns (0.00% GC)
  median time:      675.449 ns (0.00% GC)
  mean time:        704.786 ns (0.30% GC)
  maximum time:     11.487 μs (93.18% GC)
  --------------
  samples:          10000
  evals/sample:     158
```
The small amount of memory allocation is from internal bounds checking (```@boundscheck```), not from copying the data. 

**To sum up, data slicing generally creates a copy of the data and thus should be used with "view" in performance-critical code. The only exception is when slicing happens on the left of the equal sign, in which case it does not lead to memory allocation.**


Before wrapping up, here is a question for you. Would the following code fill the last two columns of ```X``` with random numbers? If not, how can you modify the code so that ```X``` is updated?
```julia
X = zeros(5, 3)
rand!(X[:, 2:3])
```






