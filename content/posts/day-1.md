+++
date = "2016-07-02T01:54:13+06:00"
author = "Mafinar Khan"
title = "Day 1 - Tabula Rasa"
linktitle = "Day 1 - Tabula Rasa"
tags = [
    "basic",
    "error-handling",
    "pattern",
    "guard"
]
weight = 10
description = "I did enough exploration the past few days and it's time I clean slate and proceed as someone learning Elixir. Not a Python programmer trying to translate Python idioms into Elixir or a Clojure programmer's view of the language, but starting with a clean slate and learning from zero."
+++

I did enough explorations the past few days and it's time I emptied my cup and proceeded as a total newcomer to the language. Not a Python programmer trying to translate Python idioms into Elixir nor a Clojure programmer checking the language out, but starting with a clean slate and learning from zero. It is not an easy thing to do. Most of the time I would fight the urge to skip a line of the documentation with the assumption that "I already know". I don't. My exposure to Elixir is that of a pre-noob's and I should act like it.

Although it's entitled *"Day 1"*, I had spent the past 2/3 days reading through the documentation and browsing through some source codes and snippets, I needed to get a feel for the language before I started documenting my experiences with it. While from a distance, Elixir seemed to have a familiar face but in reality, the way it makes you think and the way your code becomes art through it is quite different. The same is true for Python, or Golang, or Clojure. Every language boasts their own personality and it is important that their coders respect that. Writing Fortran in any language never did any good.

So, assuming zero knowledge of the language, I proceed with the rest of my days with Elixir.

## Running a File

How do I run a file? I know `iex` is the magical shell that let's me insta-code, but what if I want to write a program, a script of sorts with Elixir? Easy, I write a `.exs` file and do an `elixir <my_file_name>.exs`. 

```elixir
# hello_world.exs
defmodule HelloWorld do
    def play do
        IO.puts "Hello World"
    end
end

HelloWorld.play
```

There you go, my first super sophisticated Elixir program. A quick `elixir hello_world.exs` printed the legendary "Hello World" to my console. I could go to the `iex` and type `c("hello_world.exs")` and enable it for a `HelloWorld.run` too if I want.

What if I am in the shell, compiled the code and then I change it and want to see the results? Just make the `c` call again. 

## A little on Modules

*Modules* in Elixir contain functions. There are anonymous functions which are of the `fn(<arg1>,<arg2>...) -> <body> end` syntax and can be bound with a var, but for the named functions to exist, they have to reside under a module. 

```elixir
# Anonymous function
square_area = fn(n) -> n*n end

# Inside a module
defmodule Area do
    def square(side) do
        side * side
    end
end
```

#### Default Arguments

We can add more functions to the module, for instance, the ability to compute the area of a rectangle, given `height` and `width`:

```elixir
defmodule Area do
    def square(side \\ 0) do
        side * side
    end

    def rectangle(height \\ 0, width \\ 0) do
        height * width
    end
end
```

What were those `\\ 0` about? That's how Elixir shows default arguments. `height \\ 0` and `width \\ 0` meant that if any of those parameters were missing during the function call, they'd be replaced with `0`s. I think I will typo `//` instead of `\\` a lot.

#### Match Operator

The `=` in Elixir is called the *match operator*. It doesn't seem like other languages' `=`-s in a sense that it doesn't assign anything to a variable, it matches, and binds the right hand side expression with the left hand side one. 

```elixir
x = 1 # We all know what happens here
x = 2 # `x` now refers to someone else
3 = x # 3 does *not* match with `x`, so we get a `MatchError`
2 = x # There it is, RHS matches with LHS, so it works!
```

## Error Handling

I always make sure I familiar myself with error messages of a programing language. `2 = x` yielded no error but `3 = x` did spit up a `MatchError` and naturally, so should `3 = z`, as `z` doesn't exist to be matched in the first place. However, this will yield a completely different error- the `CompileError`. It's because the compiler expect the right hand side to be an expression which the left hand side will match with. `x` is an expression, it didn't match with `3`. `z` isn't, so it took it for a function (it looks like a no-args function since parentheses are optional in Elixir), so the error message is, `undefined function, z/0`.

#### try ... rescue and after

This brings us to exception handling. I know it's too early to be handling exceptions but well, I had dived in there early for all but my first and second languages. Let's start with everybody's favorite example:

```elixir
# Let's 1/0 and see what the error is called, ArithmeticError eh?
defmodule BadMath do
    def divide(a, b) do
        try do
            a / b
        rescue
            e in ArithmeticError -> IO.puts("An error occured: " <> e.message)
        after
            IO.puts "I just print this message for no reason at all"
        end
    end

    def play do
        IO.puts divide(10, 0)
    end
end
```

> **Question** What does the `<>` operator do?
>
> **Answer** That's Elixir string concatenation operator. (I felt kinda weird to me at first)

When we either run the script or call `BadMath.play` from `iex` we get the error message and the ~~finally~~ `after` message. So I guess we can tell that `after` puts a piece of code that gets executed regardless whether we made an error or not.

How do you ~~throw~~ `raise` an exception? The `raise` macro, of course. Just put `raise` with the ErrorName as the first argument and message as the second. Call it from within `try`-s scope and it'll ~~catch~~ `rescue` it.

#### throw and catch

~~Speaking of striking out things,~~ looks like Elixir has `throw` and `catch` as well (See what I mean by starting with _No knowlege at all?_). `throw` is *not* like `raise`. It just does it's namesake, takes a value, and throws it into the `catch`ers pitch, anything that is thrown gets handled. Like the following:

```elixir
tct = fn a,b ->
    try do
        c = a/b
        throw c
    rescue
        e in ArithmeticException -> IO.puts "Know thy Arithmetic"
    catch
        v when v <= 10 -> IO.puts "Small result: #{v}"; v
        v when v > 10 == 1 -> IO.puts "Large result: #{v}"; v
        v -> IO.puts "Is it even possible to get here?"
end
```

> **Question** `#{v}`? Does it do what I think it does?
>
> **Answer** Yes. The braces take Elixir expression, evaluates, stringifies, and replaces the `#{}` with it. It's called *String Interpolation*.

> **Question** What's with the `->` and the `when`?
>
> **Answer** Bare with me, I'll get there ~~tomorrow?~~ right after this. It's readable though, right?


#### Rescuing a Function

One thing we notice is, a lot of functions demand to be wrapped inside a `try`. And there's sugar for it too:

```elixir
defmodule BadMath do
    def divide(a, b) do
        a/b
    rescue
        e in ArithmeticError -> IO.puts("Oops: #{e.message}")
    after
        IO.puts("I get printed regardless of whether you screwed up")
    end
end
```

So a named function can be an implicit `try` too.

#### Summary

Here's the takeaway:

* `try` creates somewhat of a protected zone around your code. Any ~~Exception~~ errors occuring or `raise`d becomes a candidate for `rescue`.
* The `rescue` clause takes the error in question and matches it with a list of patterns (We're getting there soon). Whenever a pattern is matched, the associated expression is run.
* The `catch` clause deals with stuff that `try` threw at it via the `throw` function. It too, has it's own set of pattern against which the thrown value is matched with, it is better for the patterns to be exhaustive here. 
* The difference between `raise` and `catch` is that `raise` is activated when an error occures, it knows the `error` and its `message`. `catch` on the other hand is activated when something is `throw`n at it. It can be *any* value the author chooses it to be. And the value will be matched against a series of pattern, just like the `rescue` clause.
* Named functions can act as an *implicit* try. If the whole body of the function is to be probed for error, then the try wouldn't be required.
* `after` takes place regardless of whether error was made
* `else` takes place when no errors were raised or values were thrown. It also matches the value with a series of patterns just like `raise` and `catch`, it just takes on the good guys. (I had forgotten about it earlier and am too lazy to be showing an example now :()


## Patterns and Guards

Now, let's focus on the patterns we were talking about earlier. Patterns are one of the coolest things I liked about Elixir. It is actually quite simple, just take two parts, the left one will contain variables to be bound, wrapped in its data structure while the right side will be an expression. Now, mentally superimpose the right part on top of the left. If they make a complete one-to-one match (including the data structure), then you got your variables bound.

Let's take an example here, `[1, 2, 3]` is a list, right? And what of `[x, y, z]`? A list too. But whether or not you will be slapped with a `CompilerError` or not is totally upto the context and declaration conditions of the variables. Now, what happens if you place `[1, 2, 3]` on top of `[x, y, z]`? You see, `1` will sit on top of `x`, `2` on `y`, and `3` on `z`. Bring the match operator in the mix and you have `[x, y, z] = [1, 2, 3]` binding `x`, `y`, and `z`, to `1`, `2`, and `3`. However, if we place `{x, y, z} = [1, 2, 3]` they won't really match, `{` will reject the `[` and we get our favorite `MatchError`, and neither would `[1, x] = [2, 6]` because 1 ain't 2. The pattern has to match completely. 

> **Question** What one earth is a `{1, 2, 3}`?
> 
> **Answer** They're `tuples`. They are like `Lists`, but with different agenda and performance profile. Use them when you have a fixed number of elements. I'm sure I'll talk about them in a day or two.

So, let's do some pattern matchings based on whatever we know:

```elixir
[a, 2, [c, d], 5] = [1, 2, [3, 4], 5]
#[a, 2, [c, d], 5]
#[1, 2, [3, 4], 5]
# Variables on top, values on bottom, you tell me who's what?

%{lat: lat, lng: lng} = %{lat: 23, lng: 90}
#%{lat: lat, lng: lng}
#%{lat: 23,  lng: 90}
# You get the picture...

{:circle, 10} = {:square, 10}
#{:circle, 10}
#{:square, 10}
#:circle ain't :square... MatchError!!!
```

As I've mentioned in *Day-0*, `%{x: 0, y: 0}` refers to a `Map`. A special case of it too because in here, keys are atoms. If no keys were atoms, we'd do a `%{"x" => 0, "y" => 0}` instead.


#### Lists, Maps, Tuples

I have briefly mentioned `List`, `Tuple` and `Map` without talking about it much, I know I will some day but here are some quickies:

* `Atom`s are like constants where their names and values are the same.
* `List`s can be written in the form `[1, 2, 3]`. However, they are recursively constructed. For example, `[0 | []]` is `[0,1]`, `[0 | [1 | []]]` is `[0, 1]` and so on. This construction is of the form `[h|t]` where `h` refers to the first element and `t` refers to the rest. This can be used as a pattern too.
* `Map`s are written like `%{"a" => 10, "b" => 20}` but if all of its keys are atoms, then it can be of the type `%{a: 10, b: 20}`. Normally, maps are accessed via the indexing operator but in case of keyword maps, `.` operator can be used. `m = %{x: 0, y: 0}` can be accessed like either `m[:a]` or `m.a`. For keyword maps only.
* A `List` whose elements are all tuples of two elements and the first of whom are atoms, then they are called keywords and have a special sugar as well. `[{:a, 2}, {:b, 3}]` can also be written as `[a: 2, b: 3]` and queried like `lst[:a]`.

#### Functions and Patterns

Back to patterns. And here's something interesting, function arguments are pattern-ready. Which means if we define a function definition like `def f(0, 1, x)` and put `0, 1, 2` as the actual parameters, then the function will be activated and `x` will be bound with `2`. This eliminates a lot of conditions and logics and makes the programs look declarative. Modules match all definitions of the functions from top to bottom and activates the first match. Here's an example:

```elixir
defmodule BadMath do
    def factorial(0), do: 1
    def factorial(n), do: n * factorial(n-1)
end
```

In the snippet above, when we call `BadMath.factorial(4)`, then it first matches with `factorial(0)` signature, it doesn't find it, so it matches the second one. This stops when `n` is finally `0` due to the decrements and first one (non-recursive) is matched.

Here's one with the head rest pattern.

```elixir
defmodule Sort do
    def quicksort([]), do: []
    def quicksort([h|t]) do
        center = (for x <- t, x == h, do: x) ++ [h]
        quicksort(for x <- t, x < h, do: x) ++ center ++ quicksort(for x <- t, x > h, do: x) 
    end
end
```

> **Question** ++ what's that?
> 
> **Answer** You concatenate two lists with the ++ function. Or the `++/2`

This is fun... let's write some more of these.

```elixir
defmodule List do
    def len([]), do: 0
    def len([h|t]), do: 1 + len(t)

    def map(f, []), do: []
    def map(f, [h|t]), do: [f.(h)] ++ map(f, t)

    def filter(f, []), do: []
    def filter(f, [h|t]), do: (if f.(h), do: [h], else: []) ++ filter(f, t)
end
```

#### And Guards

Then there are guards. Guards are basically the `when` clauses that were mentioned in the `Exception` zone.

Guards guard their patterns, a guard starts with `when` and is followed by a condition expression. Boolean functions can be called too but predicates that guards allow are very few in number and user-custom functions cannot be used.

```elixir
defmodule Grade do
    def show(n) when n <= 100 and n >= 90, do: "A"
    def show(n) when n < 90 and n > 80, do: "B"
    def show(n) when n < 80 and n >= 70, do: "C"
    def show(n) when n < 70 and n >= 60, do: "D"
    def show(n) when n < 60 and n >= 0, do: "F"
    def show(_), do: "What were they smoking?"
end
```

This brings us to the `case` macro. The `case` macro takes an expression and matches it with a set of patterns. The grade above could be `case`d like:

```elixir
pass_or_fail = fn marks ->
    case marks do
        m when m < 33 -> :fail
        m when m >= 33 -> :pass
        m when m > 100 or m < 0 -> :wrong
    end
end
```

See when we call the function above, the `:wrong` never appears. This is because when we assign an impossible value like `-10` or `110`, then they get matched with the top two clauses, hence the last clause is never met. We should either put the final clause on top, or add a high/low value checking bound in the `when` clauses. 

Similar is `cond` macro. Instead of taking a value and matching a set of patterns, it dictates a set of conditions and expression that is associated with them.

```elixir
pass_or_fail = fn marks ->
    cond do
        marks <= 100 and marks >= 33 -> :pass
        marks >= 0 and marks < 33 -> :fail
        :else -> :wrong
    end
end
```

That's with the guards. There are a few things though:

Firstly, Guards know limited functions. Not all functions can be used in guards, no matter how boolean they are. 

Guards don't throw exceptions. I had understood it the hard way. The following piece of code, for example:

```elixir
defmodule WeirdMath do
    # def division_even(a, 0), do: raise ArithmeticError, "Division by zero"
    def division_even(a, b) when rem(a/b, 2) == 1, do: false
    def division_even(a, b) when rem(a/b, 2) == 0, do: true
end
```

When I call `WeirdMath.division_even(10, 2)` it sends me a `FunctionClauseError`, saying, there's no function clause that matches it. Then when I separately do a `rem(10, 2)` then I get an `ArithmeticError` that tells me I have bad argument in my expression. This didn't get handled in the guard.

Now, if we go back to the error handling section, we would have an easier time understanding it and be more creative while handling errors. 

## PHEW

That was a long post. And a fun one too. I will be writing on data structures tomorrow. It's holiday season here so I'll be able to write more.

> **Question** What's this `macro` you talk about?
> 
> **Answer** Pure awesomeness. I can't wait to know Elixir's version of it. 