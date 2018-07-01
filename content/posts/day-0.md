+++
author = "Mafinar Khan"
date = "2016-06-27T10:29:32+06:00"
title = "Day 0 - Unboxing and Exploration"
linktitle = "Day 0 - Unboxing and Exploration"
tags = [
    "scribble",
    "repl",
]
weight = 10
description = "Let's install this thing and do some explorations. This is a totally random, unplanned, whatever-may-come-into-your-mind post where I just query syntactic constructs and map it with Elixir's. Going to be full of misinformations and bloopers, I assume"
+++

## Get, Set, Go!!!

Obviously the first thing I need to do is, to unbox the goodies. For me, it's just `brew install elixir` and that's it. I will try out my Linux machine tomorrow. Moving on...

### The Editor

Vim's been good to me for the past decade and I see no reason why I should not use it. A quick googling introduced me to [alchemist.vim](https://github.com/slashmili/alchemist.vim) and [vim-elixir](https://github.com/elixir-lang/vim-elixir). I'm guessing the former does autocompletion and stuff while the latter is for syntax highlighting.

I'm also liking Visual Studio Code a lot these days so might as well go and set it up too. I think the command I'm looking for is `ext install vscode-elixir`. Done. Though I don't think I'll be using the editor much today.

### The Shell

Here's the fun part. I like languages with REPLs, and those that encourage the use of it by giving some nice helpers built-in. After firing up the `iex` command, it seems to me that Elixir has awesome support for it. Just go ahead and try typing `h()` in the shell and you'll see what I mean.

One thing I noticed is the naming of the form `xxx/n` (i.e. `pwd/0`, `r/1`). A quick googling later, I see it's a function/arity pair Erlang guys use. **Does that mean no variadic argument for Elixir?**

Anyway, one of the things we Pythonistas are used to is the `help()` and `dir()` functions. Is there a way to do it in Elixir? The `h/0` and `h/1` are your friends for the former, and *dot(.) + TAB* for the latter. So, if I do a `h(List)` (or `h List`) in the shell, then I'd see good old documentation of `List`. Parentheses are optional for function calls, a little Ruby envy I always had. Awesome! But I still need to explore if I can get a list of methods of a particular module (That's what they call it?) like Python does with `dir()`. I can get it by `<module>.__info__(:functions)` expression. Let's try: `List.__info__(:functions)`. Like a charm.

How do I get out? `CTRL+C` does it for Elixir shell. That's exit!


### Exploration

This is where I fire up the shell and start playing. The first thing I need to see is how I get to assign stuff. That's easy, just do a `variable_name = value`. No `let`, `var`, `val` etc. Since Elixir is a functional programming language, the first thing I'd look for is, well, functions? Let's see,

```elixir
odd? = fn(n) -> rem(n, 2) == 1 end
even? = fn n -> not odd?.(n) end

IO.puts odd?.(11) #=> true
IO.puts even?.(11) #=> false
```

Despite knowing it, I did end up calling the function as `odd?(11)`. It seems that Elixir anonymous functions need a `.()` for the arguments to be applied. True and False are just true/false. There's a convenient shortcut for it too:

```elixir
odd? = &(rem(&1, 2) == 1)
event? = &(not odd?.(&1))
right_triangle? = &(&1*&1 == &2*&2 + &3*&3)
```

Sort of like Clojure's `#(odd? %1)`. Convenient. But how do I make stuff that I can call without the parenthesis? Define a named function inside a module:

```elixir
defmodule OddEven do
    def odd? n do
        rem(n, 2) == 1
    end

    def even? n do
        not odd? n
    end
end
```

Oh, and comments begin `# with a hash`

It is a common syntax pattern of Elixir to have constructs like `<something> <expression> do <body> end` it seems. Let's take a look at `if`:

```elixir
defmodule LeapYear do
    def leap_year? year do
        if rem(n, 400) == 0 do
            return true
        end
        if rem(n, 100) == 0 do
            return false
        end
        if rem(n, 4) == 0 do
            return true
        end

        false
    end
end
```

See what I mean? But Elixir is a very pattern-happy language, so there's another way of doing it:

```elixir
defmodule LeapYear do
    def leap_year?(year) when rem(year, 400) == 0 do
        true
    end
    def leap_year?(year) when rem(year, 100) == 0 do
        false
    end
    def leap_year?(year) when rem(year, 4) == 0 do
        true
    end
    def leap_year? year do
        false
    end
end
```

It's like those piecewise defined functions we did at school.

We skimmed `conditions`, let's `loop`. I didn't see any C-style `for` equivalent yet. And I'm not supposed to since Elixir doesn't mutate things. Instead I get recursion:

```elixir
defmodule Fibonacci do
    def compute n do
        if n <= 1 do
            n
        else
            compute(n - 1) + compute(n - 2)
        end
    end

    def range n do
        for i <- 1..n do
            compute(i)
        end
    end
end
```

So we have a `foreach`-ish construct. It's called comprehension and is better viewed as `for i <- <range> do: ...`. The `something do body end`-s have a short form of `something, do: ...` it seems. And instead of the `if` as base case in `compute`, could we instead use that piecewise defined thingy?

```elixir
defmodule Fibonacci do
    def compute(n) when n <= 1 do
        n
    end

    def compute(n) do
        compute(n - 1) + compute(n - 2)
    end

    def range(n) do
        for i <- 1..n, do: compute i
    end
end
```

An interesting thing about for comprehensions is that you can put conditions in the comma separated values, or multiple iterations too, take for example, this one:

```elixir
# Can you tell me what this yields?
triplets = for a <- 1..10, b <- 1..10, c <- 1..10, c*c = a*a + b*b, do: {a, b, c}
```

This brings us to composite types. It's safe to assume that List and Map types exist. And there's a Tuple too. There's more, obviously.

```elixir
# Lists
lost_numbers = [4, 8, 15, 16, 23, 42]
Enum.at(lost_numbers, 0) #=> 4
Enum.map(lost_numbers, fn n -> n*n end) #=> Square each of lost numbers
Enum.reduce(lost_numbers, &(&1 + &2)) #=> Find the sum of lost numbers
42 in lost_numbers #=> true

# MAPS
lost_candidates = %{
    4 => "Locke",
    8 => "Hugo",
    15 => "Sawyer",
    16 => "Sayid",
    23 => "Jack",
    42 => "Kwon",
}

Dict.keys(lost_candidates) == lost_numbers #=> true
Dict.values(lost_candidates) #=> ["Locke", "Hugo", "Sawyer", "Sayid", "Jack", "Kwon"]
Dict.put(lost_candidates, 47, "Mafinar") #=> Returns a new Dict with me with number 47
Dict.get(lost_candidates, 4) #=> "Locke"
lost_candidates[23] #=> "Jack"
```

Looks to me like Elixir calls module functions a lot. It's not `lost_candidates.keys` but `Dict.keys lost_candidates`, not `lost_numbers.at(0)` but `Enum.at lost_numbers, 0`. The first argument being lost_numbers can have an important impact:

```elixir
# Square the lost_numbers, then find the odd ones, then spit out the product.
square = fn n -> n*n end
odd? = fn n -> rem(n, 2) == 1 end
product = fn a, b -> a * b end

Enum.reduce(Enum.filter(Enum.map(lost_numbers, square), odd?), product)
```

So, the `map` gives out the squared numbers, and feeds it as the first argument to the `filter`, which in turn spits the odd numbers and becomes the first argument of the `reduce` function. Instead, why not do a pipe? Remember those UNIX `|` constructs?

```elixir
square = fn n -> n*n end
odd? = fn n -> rem(n, 2) == 1 end
product = fn a, b -> a * b end

lost_numbers |> Enum.map(square) |> Enum.filter(odd?) |> Enum.reduce(product)
```

I have always loved Clojure's threading macros. Looks like Elixir has one too. And it looks awesome with Firacode ligatures!

I must admit, I was a little confused with some of the Map examples I saw, `%{ 2 => "Two"}` works but `%{2: "Two"}` spits out an error. And what's with this `[:x 25, :y 30]` thingy? Looks like I need to inspect the APIs better and get a good understanding of Atoms and Tuples.

## Okay then

This was just random scribbles on my part, hence the *day 0* bit. I just wanted to do a quick run down on Elixir and see how things fall and write-up whatever came out. And I must say, Elixir seems to have potential of being a super fun language. So far, it seemed braing and sanity friendly to me. And I find the code beautiful too. I'm glad I took it.

I am tired right now. That's all exploration for me that I'd write about. I'll just go and explore more (i.e string, structs etc) on my own and share it tomorrow?