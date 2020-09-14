---
layout: post
title: Assignment versus Mutation 
snippet: It's all about "=".
tags: [julia]
---

Check this out:

```julia
x = ones(1,3)
function change_array1!(y)
    y .= 2 .* y
end
change_array1!(x)
x
```
After running it, I got
```julia
julia> x
1×3 Array{Float64,2}:
 2.0  2.0  2.0
```

Here is a similar example:
```julia
x = ones(1,3)
function change_array2!(y)
    y = 2 * y
end
change_array2!(x)
x
```
but instead of getting the same result, I got
```julia
julia> x
1×3 Array{Float64,2}:
 1.0  1.0  1.0
```
Apparently ```change_array2!()``` did not update ```x``` as I had expected. To understand what's going on, we need to distinguish between two concepts: assignment and mutation. 

<span class="highlight-orange">Assignment</span> declares (or changes) which object a name refers to. For example, in ```x = expression```, we declare that ```x``` refers to the result of ```expression```. Some people call this "variable binding" rather than "assignment", so you can also say ```x = expression``` binds ```x``` to the result of ```expression```.  The important thing is that <span class="highlight-blue">assignment never changes the value of any object</span>. 

<span class="highlight-orange">Mutation</span>, on the other hand, changes the value of an object. For example, in ```x[i] = expression```, we change the ```i```th value of ```x``` to the result of ```expression```. Of course, ```expression``` must evaluate to the same type as the elements of ```x```. Note that mutation never changes the object that the name refers to. In other words, it never changes the variable binding. 


Here comes the confusing part: both assignment and mutation use the same notation: the equal sign (```=```). So it's easy to mix them up and run into problems, like I did above. But if we take a closer look, it's not difficult to see the meaning of each ```=```. 

Going back to the function ```change_array1!()```, we see that upon calling ```change_array1!(x)```, the variable ```y``` was first bound to ```x```, then inside the function, each element of ```y``` was multiplied by 2. The ```=``` sign is clearly a mutation because it changes the value of ```y``` instead of the object it refers to. Since ```y``` was bound to ```x```, it's no surprise that the value of ```x``` changed. 

In contrast, upon calling ```change_array2!(x)```, the variable ```y``` was first bound to ```x```, but then re-bound to ```2 * y```. The ```=``` sign re-assigned the object that ```y``` referred to. To verify that the re-assignment happened, we can use the following code:

```julia
x = ones(1,3)
pointer(x)
function change_array3!(y)
    y = 2 * y
    print(pointer(y))
end
change_array3!(x)
```

you'll see that ```x``` and ```y``` point to different memory addresses. Since no mutation happened, the value of ```x``` should not change, as we saw at the beginning. 


As you can see, assignment and mutation are actually  simple concepts. With a bit of practice, you'll surely recognize which is which. 

---

Here are a few more practices.

Example 1:
```julia
x = rand(2,2,1)
a = ones(2,2)
x[:,:,1] = a
a[1,1] = 5
```
What is ```x[1,1,1]```? 1 or 5?

Example 2:
```julia
y = rand(2,2)
b = ones(2,2)
y = b
b[1,1] = 5
```
What is ```y[1,1]```? 1 or 5?

Example 3:
```julia
x = 1
function change_value!(y::Real)
    y += 1
    return y
end
change_value!(x)
```
What is ```x```? 1 or 2?
