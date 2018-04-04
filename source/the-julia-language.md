---
title : The Julia Language
description: A Fresh Approach to Numerical Computing
author : Roger Luo

markdown:
  extensions:
  # more pymdownx extensions here:
  # https://facelessuser.github.io/pymdown-extensions/
    - pymdownx.magiclink
    - pymdownx.tasklist

reveal:
    theme: 'white'
    # full configuration see:
    # https://github.com/hakimel/reveal.js#configuration
    config:
        transition: 'fade'
...

---

![julia-logo](/media/julia-logo.svg){: style="border: 0; box-shadow: none"}

**A Fresh New Approach to Numerical Computing**

---

## Some Selected Features

---

- Multiple Dispatch
{: .fragment .current-visible}
- Dynamic Type System
{: .fragment .current-visible}
- Good Performance, approaching statically-compiled languages like C
{: .fragment .current-visible}
- Lisp-like macros and other metaprogramming facilities
{: .fragment .current-visible}
- Call Python functions: use the PyCall package
{: .fragment .current-visible}

---

- Call C functions directly: no wrappers or special APIs
{: .fragment .current-visible}
- Designed for parallelism and distributed computation
{: .fragment .current-visible}
- Automatic generation of efficient, specialized code for different argument types
{: .fragment .current-visible}
- elegant and extensible conversions and promotions for numeric and other types
{: .fragment .current-visible}
- Efficient support for Unicode, including but not limited to UTF-8
{: .fragment .current-visible}
- MIT licensed: free and open source
{: .fragment .current-visible}

---

## Let's learn it from examples

---

### REPL

Julia has an awesome REPL, just type `julia` in your terminal

```shell
shell> julia
```

---

```
               _
   _       _ _(_)_     |  A fresh approach to technical computing
  (_)     | (_) (_)    |  Documentation: https://docs.julialang.org
   _ _   _| |_  __ _   |  Type "?help" for help.
  | | | | | | |/ _` |  |
  | | |_| | | | (_| |  |  Version 0.6.3-pre.0 (2017-12-18 07:11 UTC)
 _/ |\__'_|_|_|\__'_|  |  Commit 93168a6 (106 days old release-0.6)
|__/                   |  x86_64-linux-gnu

julia>
```

---

### CLI Compiler

Like **GCC**, **Python** and other compilers, you can run julia scripts by
its CLI interface

```shell
shell> julia script.jl
```

---

### **Demo:**{: style='color: #CD5C5C'} Hello World

```julia
println("hello world!")
```

```shell
shell> julia hello.jl
hello world!
```

---
note: "julia has abundant builtin types, even including arbitrary precision numerical types"
...

### Builtin Numerical Types

- `Int8` to `Int128`
{: .fragment .current-visible}
- `Float16` to `Float64`
{: .fragment .current-visible}
- `BigInt` and `BigFloat` (Arbitrary Precision)
{: .fragment .current-visible}

---
note: "a function is defined using the key word function and end, we use :: to declare the variable type, or you can just let julia itself find out the type."
...

### **Demo:**{: style='color: #CD5C5C'} Sum up two numbers

Julia has abundant builtin types, including:

```julia
function mysum(a::Int, b)
    return a + b
end
```

---
note: "We can use `@code_typed` to look julia's type inference"
...

```julia-repl
julia> @code_typed mysum(1, 2)
CodeInfo(:(begin 
        return (Base.add_int)(a, b)::Int64
    end))=>Int64
```

---
note: "Julia has an amazing type system, I will introduce them by writing our own Complex type"
...

### Type System

---
note: "Except abundant builtin types, we can defined our own composite types."
...

```julia
struct Complex128
    real::Float64
    imag::Float64
end
```

---
note: "You can use its default constructor, and note the parameters have an order"
...

```julia
julia> Complex128(1, 2)
```

---
note: "However there are many complex types in different precision, we will use the parameter type"
...

```julia
struct Complex{T}
    real::T
    imag::T
end
```

---
note: You can specify the type parameter by filling its constructor
...

```julia
julia> Complex{Float64}(1.0, 2.0)
Complex{Float64}(1.0, 2.0)
```

---

But actually julia can infer the parameters itself if possible

```julia
julia> Complex(1.0, 2.0)
Complex{Float64}(1.0, 2.0)
```

---
note: "However, we need to limit the type parameter to numerical numbers for safety"
...

This does not make sense for numerics:

```julia
Complex{String}("a", "b")
```

---
note: "All numbers are subtypes of Number in Julia, we will declare our Complex type as an subtype of Number, which means the parameter of the type can only be subtype of Number, and the Complex type itself is an subtype of Number. Number is something called abstract type, which we will introduce later."
...

```julia
struct Complex{T <: Number} <: Number
    real::T
    imag::T
end
```

---
note: "try it out! And you will receive an error."
...


```julia-repl
julia> Complex{String}
ERROR: TypeError: Complex: in T, expected T<:Number, got Type{String}
```

---
note: ""
...

---

### **Demo:**{: style='color: #CD5C5C'} Multiple Dispatch

---
note: "abstract types are something slightly similar to virtual class, it does not contain anything as its member and can not be constructed."
...

#### Abstract Types

We could define some abstract type hierarchies.

```julia
# The zoo society
abstract type Animal end
abstract type Bird <: Animal end
abstract type Cat <: Animal end
```

---
note: "There are two kind of composite types"
...
