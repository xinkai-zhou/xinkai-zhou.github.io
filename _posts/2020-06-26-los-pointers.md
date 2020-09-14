---
layout: post
title: Los Pointers
snippet: How I tripped over a pointer again.
tags: [julia]
---

Pointers often catch me at the most inconspicuous place. This is latest mistake I made: 

```julia
using Random
# define a type called "Obs" for storing simulated observations
struct Obs{T <: Real}
    x::Matrix{T}
    y::Vector{T}
end
# initialize x and y
x = Matrix{Float64}(undef, 2, 1)
y = Vector{Float64}(undef, 2)
# initialize a vector of type "Obs" 
obsvec = Vector{Obs}(undef, 2)
for i in 1:2
    # update x and y in-place with standard normal random numbers
    Random.randn!(x)
    Random.randn!(y)
    # construct the Obs type and update the ith element of obsvec
    obsvec[i] = Obs(x, y)
end
```
Nothing can go wrong, right? I was simply filling up ```obsvec``` with simulated data. 

Well, this is what I got:
```julia
julia> obsvec
2-element Array{Obs,1}:
 Obs{Float64}([0.6313754321706756; -0.5667842095988521], [-1.1646838114733535, -1.0489223537242356])
 Obs{Float64}([0.6313754321706756; -0.5667842095988521], [-1.1646838114733535, -1.0489223537242356])
 ```
See? The two elements are exactly the same! 

Why is that? Well, I thought ```Obs(x, y)``` would create the ```Obs``` type by making a copy of ```x``` and ```y```. However, that's not true. In fact, we have

```julia
julia> pointer(obsvec[1].x) == pointer(obsvec[2].x)
true
```

so ```obsvec[1].x``` and ```obsvec[2].x``` are pointing to the same memory address. This means the command ```Obs(x, y)``` did not create data copies. Instead, it simply points to the memory addresses of ```x``` and ```y```.

To properly capture the simulated data from each iteration, we need to  create data copies explicitly using ```copy()```:
```julia
obsvec = Vector{Obs}(undef, 2)
for i in 1:2
    Random.randn!(x)
    Random.randn!(y)
    obsvec[i] = Obs(copy(x), copy(y))
end
```
which gives
```julia
julia> obsvec
2-element Array{Obs,1}:
 Obs{Float64}([-1.4464105655902875; -0.17609789183797744], [-0.3046411926617105, 0.6374189298303475])
 Obs{Float64}([0.12553600335281864; 0.8587688822701708], [0.4357105884994289, -0.33280460286744384]) 
 ```
This is what we need.

<center><img src="https://imgs.xkcd.com/comics/pointers.png"></center>