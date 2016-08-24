# An Excursion Through Recursion In Elixir

#### tour guide - Nathan Hessler

---

## The Excursion

* Method Stack
* Recursion
* Beam Specifics
* Decisions, Decisions


Note:
Get a sense of where the group stands on understanding the basics recursion

We'll have a couple breaks for Questions throughout and a big Q & A at the end

---

## The Method Stack

---

### Stack Definition

Stacks in computing architectures are regions of memory where data is added or removed in a last-in-first-out (LIFO) manner.

https://en.wikipedia.org/wiki/Stack-based_memory_allocation

---

### Stack actions

* **Push** - adds to the top of the stack
* **Pop** - removes from the top of the stack
* **Peek** - look at the top of the stack

---

### Real World Example

<img src="/images/partial_stack_toy.jpg"  height=400>

---

### Elixir Example

``` elixir
defmodule Person do

  defstruct fname: "John", lname: "Doe"

  def greeting(%Person{fname: first, lname: last}) do
    greet_parts = ["hello ", receiver, ", I'm ", first, " ", last]
    IO.puts Enum.join(greet_parts, "")
  end

  defp receiver, do: "NoVA Elixir"
end

me = %Person{fname: "Nathan", lname: "Hessler"}

# What's happening when this gets run?
Person.greeting(me)
```

Note: FYI - This is not idiomatic Elixir, Also Applicative Order is in affect.

---

### Elixir Walkthrough

---
<!-- .slide: data-transition="none" -->
**Push Person.greeting/1**

<img src="/images/Hanoi_Tower_Set_Elixir_1-01.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Push Person.receiver/0**

<img src="/images/Hanoi_Tower_Set_Elixir_1-02.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop Person.receiver/0**

<img src="/images/Hanoi_Tower_Set_Elixir_1-03.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Push Enum.join/2**

<img src="/images/Hanoi_Tower_Set_Elixir_1-04.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop Enum.join/2**

<img src="/images/Hanoi_Tower_Set_Elixir_1-05.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Push IO.puts/2**

<img src="/images/Hanoi_Tower_Set_Elixir_1-06.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop IO.puts/2**

<img src="/images/Hanoi_Tower_Set_Elixir_1-07.svg" height=500>

---

### Note

The Stack is not infinite, for now we'll use a stack size of 10

---

## Method Stack Questions?

---

## Recursion

---

### Definition

Recursion is the process a method goes through when one of the steps of the method involves invoking the method itself.

https://en.wikipedia.org/wiki/Recursion

---

### Factorial
**The Hello World of Recursion**

In mathematics, the factorial of a non-negative integer n, denoted by n!, is the product of all positive integers less than or equal to n.

https://en.wikipedia.org/wiki/Factorial

```bash
3! =     3 * 2 * 1 = 6
4! = 4 * 3 * 2 * 1 = 24

# more formally (using recursion)

n! = n * (n-1)! when n > 1
n! = 1          when n = 0 or n = 1
```

---

### Factorial In Elixir

```elixir
defmodule Math do
  def factorial(n) when n > 1, do: n * factorial(n - 1)
  def factorial(n) when n == 0 or n == 1 , do: 1
end

# What happens when we run this?
Math.factorial(3)
```

---
<!-- .slide: data-transition="none" -->
**Push Math.factorial(3)**

<img src="/images/Hanoi_Tower_Set_Elixir_2-01.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Push Math.factorial(2)**

<img src="/images/Hanoi_Tower_Set_Elixir_2-02.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Push Math.factorial(1)**

<img src="/images/Hanoi_Tower_Set_Elixir_2-03.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop Math.factorial(1)**

<img src="/images/Hanoi_Tower_Set_Elixir_2-04.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop Math.factorial(2)**

<img src="/images/Hanoi_Tower_Set_Elixir_2-05.svg" height=500>

---

### Stackoverflow

* Remember:
  * The Stack is not infinite
  * Our stack size is 10
* Recursion is often what *'overflows'* the stack

```elixir
# What happens when we run this?
Math.factorial(11)
```

---

**The Stack Overfloweth**

<img src="/images/Hanoi_Tower_Set_Elixir_3-01.svg" height=500>

---

### Tail Recursion

a tail call is a method call performed as the **final action** of a method. If a tail call leads to the same method being called again, the method is said to be **tail-recursive**,

https://en.wikipedia.org/wiki/Tail_call

**Note**: Remember to take **applicative order** into consideration. And, anything not **tail-recursive** is called **body-recursive**.

---

### Tail Recursive Factorial

```elixir
defmodule Math do

  # maintain interface
  def factorial(n), do: factorial_helper 1, n

  defp factorial_helper(acc, n) when n == 0 or n == 1, do: acc
  defp factorial_helper(acc, n) when n > 1 do
    factorial_helper(n * acc, n - 1)
  end
end

#What happens when we run this?
Math.factorial(3)
```
---

<!-- .slide: data-transition="none" -->
**Push Math.factorial(3)**

<img src="/images/Hanoi_Tower_Set_Elixir_4-01.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Push Math.factorial_helper(1, 3)**

<img src="/images/Hanoi_Tower_Set_Elixir_4-02.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Push Math.factorial_helper(3, 2)**

<img src="/images/Hanoi_Tower_Set_Elixir_4-03.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Push Math.factorial_helper(6, 1)**

<img src="/images/Hanoi_Tower_Set_Elixir_4-04.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop Math.factorial_helper(6, 1)**

<img src="/images/Hanoi_Tower_Set_Elixir_4-05.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop Math.factorial_helper(3, 2)**

<img src="/images/Hanoi_Tower_Set_Elixir_4-06.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop Math.factorial_helper(1, 3)**

<img src="/images/Hanoi_Tower_Set_Elixir_4-07.svg" height=500>

---

### Tail Call Elimination

Tail calls can be implemented without adding a new stack frame to the method stack. Most of the frame of the current method is not needed any more, and it can be replaced by the frame of the tail call, modified as appropriate.

https://en.wikipedia.org/wiki/Tail_call

**Note**: This is usually implemented by functional Languages(Lisp, Elixir, Haskell), and not by OO Languages(Java, Python, Ruby)

---
<!-- .slide: data-transition="none" -->
**Push Math.factorial(3)**

<img src="/images/Hanoi_Tower_Set_Elixir_5-01.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Push Math.factorial_helper(1, 3)**

<img src="/images/Hanoi_Tower_Set_Elixir_5-02.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Swap call (1,3) for call (3,2)**

<img src="/images/Hanoi_Tower_Set_Elixir_5-03.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Swap call (3,2) for call (6,1)**

<img src="/images/Hanoi_Tower_Set_Elixir_5-04.svg" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop Math.factorial_helper(6, 1)**

<img src="/images/Hanoi_Tower_Set_Elixir_5-05.svg" height=500>

---

## Recursion Questions

---

## Beam Specifics

---

### Beam Basics

* VM for Erlang, Elixir, LFE, etc...
* Register Based with 1024 Virtual Registers
* One OS process
  *  One OS thread per core for max utilization
* Can use the complete address space per node
  * The OS often limits a single process to less than that.

---

### Beam Stack Size

Most Languages use the OS to manage their stack. Beam on the other hand implements it's own stack

* Each process has it's own stack
* Limited by available space in the Heap (allocated memory in VM)

```bash
  ulimit -s #=> 8192 for OS managed stacks
```

---

### Beam Recursion Myth

**Myth**: Tail-Recursive Functions are Much Faster Than Recursive Functions

---

### Beam Recursion Details

This used to be true before *R7B*

---

### Beam Recursion Details

**body-recursive** functions use more memory thus invoking GC more often than **tail-recursive** functions

---

### Beam Recursion Details

In *R12B* and later releases, there is an optimization that in many cases reduces the number of words used on the stack in **body-recursive** calls.

---

### Beam Recursion Details

A **tail-recursive** function is still most of the time faster than a **body-recursive** function.

A **tail-recursive** function that does not need to reverse the list at the end is faster than a **body-recursive** function

---

### Beam Recursion Details

A **body-recursive** list function and a **tail-recursive** function that calls `lists:reverse/1` at the end will use the same amount of memory.

`lists:map/2`, `lists:filter/2`, list comprehensions, and many other recursive functions now use the same amount of space as their **tail-recursive** equivalents.

---

## Beam Q & A

---

## Decisions, Decisions

---

### What does this all mean for the stack?

``` elixir
defmodule Math do
  def times(factor1, 1), do: factor1
  def times(factor1, factor2) do
    factor1 + times(factor1, factor2 - 1)
  end
end

# What happens when we run this?
Math.times(5, 10_000_000)
```

---

### What does this all mean for speed?

So, which is faster? It depends. On certain architectures, the **body-recursive** function seems to be slightly faster, even for lists with a lot of elements. On other architectures, **tail-recursion** was up to about 30% faster.

---

### What this means

So, the choice is now mostly a matter of taste.

If you really do need the utmost speed, you must measure. You can no longer be sure that the tail-recursive list function always is the fastest.

---

### Caveat

Infinitely recursive methods should always be tail optimized or you will effectively 'leak' memory

---

### Caveat

``` elixir
defmodule KeyValStore do
  def start_link do
    Task.start_link(fn -> loop(%{}) end)
  end

  defp loop(map) do
    receive do
      {:get, key, caller} ->
        send caller, Map.get(map, key)
        loop(map)
      {:put, key, value} ->
        loop(Map.put(map, key, value))
    end
  end
end
```

---

## Thank You!

**Nathan Hessler**

* twitter: @spune
* github: nhessler
* work: customink
* meetup: NoVA Code & Coffee

---

**Shout Outs**
* Kat Hubbs (stack slides)

---

## Q & A

<img src="/images/signed_erlang_pennant.jpg"  height=500>

---
