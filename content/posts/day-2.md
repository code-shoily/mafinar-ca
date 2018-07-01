+++
date = "2016-07-05T01:54:13+06:00"
author = "Mafinar Khan"
title = "Day 2 - Diving in..."
linktitle = "Day 2 - Diving In..."
tags = [
    "enum",
    "list",
    "tuple",
    "set",
    "comprehension"
]
weight = 10
description = "Since last night I've been playing with some of the data structures of Elixir, mostly lists, tuples and maps. While I'm not new to these data structures, the way Elixir's API is laid out is a little different and took awhile to get used to"
+++

Since last night I've been playing with some of Elixir's data structures, mostly lists, tuples and maps. While I'm not new to these data structures, the way Elixir's API is laid out is a little different and took awhile to get used to. The syntaxes for list and tuple seemed intuitive enough, `[1, 2, [3, 4]]` is a list, `{91, 23}` is a tuple. With maps it was a little different, `%{"key" => "value"}` or `%{keyThatsAtom: "value"}`. Actually, this is me remembering the feeling I had few days ago when I actually started Elixir. I already played a little, today I'll just get a better feeling of them since I've more or less grokked pattern matching (hopefully). 

### Tuple

I'll start with tuples. I first heard the term tuple when learning Python. In Elixir, a tuple is wrapped with curly braces and the syntax is like: `white = {255, 255, 255}`. The elements are place contiguously in memory. So it is faster for accessing by index. There's a function for that too, `Kernel.elem/2`. `elem(white, 0)` will yield `255` (I should've picked another color). Since data is placed contiguously on memory, finding the length of the tuple is also easy, which is done with the `Kernel.tuple_length/1` function. `Kernel.is_tuple/1` tests for tuple, a pattern which we will see for other types as well. 

Tuples are fit for use cases that leverages its find-by-index performance issue and do not need the structure to grow, tuples don't like to grow or shrink, they did give a function for it that returns a grown up or shrunk down copy, they just don't like it. They are awesome for patterns, for example; or arguments to functions or as return values.

There is this pattern I've seen a lot in Elixir. You use tuples as return values and put the status as the first element, as an atom. It's kindof like the `(result, err)` pattern of `Golang`, with the `err` part positioned first, and also as `keyword`. They are also good candidates as function arguments and replacing if-s. For instance:

```elixir
defmodule Area do
    @pi 3.1416

    def compute({:square, side}) do
        side * side
    end

    def compute({:rectangle, length, breadth}) do
        length * breadth
    end

    def compute({:circle, radius}) do
        @pi * radius * radius
    end
end
```

By the way, the `@pi` thing was *module attribute*, it has many purposes, one of them is to store module owned constants, and `@pi` was exactly that (I'll learn more on *module attributes* later). 

### Lists

Lists in Elixir are linked lists. Each element has a value and a pointer to the next element. A list is of the type `[first_element | nextElementsAsList]` all the way till `nextElementsAsList` becomes `[]`. From this behavior it is clear that prepending to an Elixir list should be easier since it would mean getting the new element and putting the current list in the *tail section* (obviously, returning the new structure, not modifying the old list in anyway, immutate all the things!). 

If I got a nickel everytime I tried to access an element of a list by `list[element_index]` :( That doesn't work. If you want to access an element at a particular index, then you'll have to use the `Enum` module and use the `Enum.at/2` or `Enum.at/3` functions. Like I've mentioned earlier and I'll mention again as long as I don't get fully comfy with the pattern: "Elixir module functions are called like static methods, you do a `Module.function` call, put the data structure in question as the first argument, others as rest, there's no `x.y` where `x` is not a module (or as we'll see later, a map?)". So, `Enum.at [1,2,4], 1` yields `2` and `Enum.at [1,2,3], 10` yields `nil`. If you want to put a default value instead, then use the `Enum.at/3` function and the third argument is the default argument in case there's a `nil`.

> *What if my list is `[1, nil, 3]` and I do a `Enum.at lst, 1`? Wouldn't it be better to raise an error instead of just `nil`? Or at least an `Enum.at!` alternative?*

#### Enum

So what is this `Enum` thing? It does all the kung-fu required to play with iterables, want to `find` something? `map`, `reduce`, `filter`? `Enum`-s got it all. As an example, let's say we have a list of latitudes and longitudes of the path of a certain locomotive and we want to find the total distance travelled. So, the first thing we do is, extract `{lat, lng}` tuple from the list, then take 2 of each in `a->b, b->c, c->d` etc format, call the distance function amongst each pair, and then return the sum total of it. The operationset is as follows: 

* Define the distance function.
* Take a list, form a 2-tuple out of all the elements of the list
* Take chunks of adjacent tuples
* Compute the distance between each tuples of every chunk in the list
* Compute the total of all distances

```elixir
defmodule Distance do
    # Saving me some typings, `import` saves you from 
    # `<module>.` prefixes. Can be just `map` instead of `Enum.map`
    import :math # Erlang modules are written like :atoms
    import Enum

    @radius 6_371_000 # You can use underscores as large number separator. Neat!
    @pi :math.pi

    def square(x), do: x*x
    def to_radians(degree), do: degree / (180/@pi)

    def compute_diff {lat1, lng1}, {lat2, lng2} do
        {lat2 - lat1, lng2 - lng1} 
    end

    def compute [{lat1, lng1}, {lat2, lng2}] do
        {lat_diff, lng_diff} = compute_diff {lat1, lng1}, {lat2, lng2}
 
        a = square(sin(lat_diff/2)) + cos(lat1) * cos(lat2) * square(sin(lng_diff/2))
        c = 2 * asin(sqrt(a))
        
        @radius * c
    end

    def total(points \\ []) do
        points |> map(&to_radians/1)
               |> chunk(2)
               |> map(&List.to_tuple/1) 
               |> chunk(2, 1)
               |> map(&compute/1) 
               |> reduce(&+/2)
               |> (&//2).(1000)
    end
end
```

The `|>` is a super fun operator. Its work is simple, you take an expression, and pipe it through several `partial functions` through it, the expression result in question will be the first (missing) argument of the partial function, the result will be fed to the second one, and so on... the snippet of `Distance.total/1` is actually the equivalent of `reduce(map(chunk(map(chunk(map(points, &to_radius/1), 2), &List.to_tuple), 2, 1), &compute/1), &+/2))/1000`. Phew! Each of the functions following the pipe command is actually missing a first argument... for instance, the `map(&List.to_tuple/1)` is really `map(?, &List.to_tuple/1)` where the `?` is to be filled up by the expression preceding it, the expressions keep evaulating and pillow passing the result to the next pipe until there's no more left, and that's when the combined result is expressed. It is a more `threaded together` approach at processing and is very handy when it comes to transforming collections. No wonder the Clojure equivalent is called the threaded macro.

> *One thing though* functions _must_ be called with parentheses when inside the pipe operator. No poetry there :(

By the way, in the code above I have used `import`s and it saves me from repetitively typing `Enum.map`, or `:math.sin`. I will look more into these later.

The `&` preceding what should've been a function body does exactly that. Expresses the function body. In `Elixir`, `f`, if not a variable, means call to the `f` function. So, had we written `map(list, List.to_tuple)`, the Elixir will attempt to call the `List.to_tuple/0` function, for which Elixir didn't define any. Also, it is important to mention the arity as well otherwise the compiler will get confused.  

So we see that, (Linked)Lists of Elixir are recursively defined in a head/tail format and are best suited for data that require prepending growth. They can be heterogenous or homogenous. They are iterable via the `Enum` module are promote an immutable attitude. Through combinations of `Enum` functions, we can traverse, project, filter or commit reductions on lists and the pipe operator does a beautiful job of keeping code readable, fluid, and concise.

### Keyword Lists

Keywords lists are special lists in which all elements are 2-tuples in which first element's an atom. They have special syntax privileges. `[{:x, 10}, {:y, 20}]` can be conveniently typed as `[x: 10, y: 10]` and accessed as `kwl[:x]`.

Keyword lists add an additional syntactic benefit. If a named functions last argument is expected to be a keyword list, then the enclosing `[]` can be stripped off.

```elixir
defmodule StrippedKW do
    def example name: name, id: id do
        {:info, "#{name} - #{id}"}
    end
end

# And when calling...
StrippedKW.example name: name, id: id
```
The sugar of omitting the `[` and `]` allows us to create fluent and beautiful APIs. For example, we can do stuff like:

```elixir 
Assets.all(  
  from vehicle in Assets.Vehicles,
      where: vehicle.truck? == true,
  select: vehicle
)
```

Omission of parentheses or brackets make a set of function call seem like a totally new **L**anguage tailored **S**pecifically for the **D**omain.

### Sets

`Set` structure is similar to its mathematical equivalent and is depicted by the `%MapSet{}` struct and created with the `new/1` function. For example `MapSet.new [1, 2, 3]` is equivalent of the mathematical `{1, 2, 3}`. All set operations such as `MapSet.union/2`, `MapSet.intersection/2`, `MapSet.difference/2` etc are defined under the `MapSet` module along with predicates such as `MapSet.member?/2`, `MapSet.disjoint?/2`.

```elixir
u = MapSet.new [1, 2, 3, 4, 5, 6]
a = MapSet.new [1, 3, 5]
b = MapSet.new [2, 4, 6]
c = MapSet.new [3, 6, 9]

MapSet.union a, b # {1, 2, 3, 4, 5, 6}
MapSet.intersection a, c # {3}
Mapset.member? a, 3 # true 
```

We can leverage the `Enum` functions on MapSets as well.

### Comprehensions

List comprehension is one of my favorite language features and I'm glad Elixir allows it smoothly. It is quite close to the mathematical treatment of it too. For example, if we want to find all numbers that are between `1` and `100` and are divisible by 3 or 5, we can write: `for i <- 1..100, rem(i, 3) == 0 || rem(i, 5) == 0, do: i`, or, if we want to find all Pythagorean triples maxing 10 unit: `for i <- 1..10, j <- 1..i, k <- 1..j, i*i == j*j + k*k, do: {i, j, k}`. The comprehension syntax is simple: `for` starts the expression, which is followed by several comma separated expressions, which can either be iterations, depicted by the `<-` operator, or conditions which are, well, condition expressions. Finally, the `do:` clause allows the yielded expression to give out.

What if we want to do a comprehension but yield the result into a different structure? For example, we iterate through a list and want to return a set? The `into:` is there for this (I will have to read more on this). `for i <- [1, 1, 5, 7, 8, 8, 16, 32, 32], into: %MapSet{}, do: i*i` does the trick!

### Playing on...

The `Enum` contains a good load of function to play with iterable structures. All I need to do is push `Enum.` and then the `<TAB>` button to see the list of possibilities and `h Enum.<function>` to see what it does in the `iex`. The documentation is fantastic and it's a piece of cake to know which function does what. The `|>` operator can compose multiple expressions and still be readable and feel fluent. This is especially due to the consistancy of first argument being the structure that the pipe operator can function. Some of the functions here reside in `Kernel` instead of their respective modules, which I need to dig further to grok the reason why.

Let's play a little more with `Enum`s (Will write the first few minutes' ones here)

```elixir
[10, 34, 32, 12, 11, 19, 20] # Le list 
    |> Enum.with_index # Get indexed tuple at {value, index} formation 
    |> Enum.filter(fn {v, idx} -> rem(idx, 2) == 1 end) # Find odd-indexed tuples
    |> Enum.map(fn {v, idx} -> v end) # Extract the values as value-list
    |> Enum.reduce(1, &*/2) # Multiply all the things

1..1000 # 1 to one thousand
    |> Enum.take_random(10) # Take any random 10 element 
    |> Enum.find(-1, fn v -> rem(v, 13) == 0) # Pluck ones whom 13 divides or -1

1..100 |> Enum.scan(&+/2) # Like reduce, but all intermediate results are enlisted!

# split_while, uniq, sort_by, zip, intersperse... my my!
```

> Wow!

Tomorrow I'll be playing with `Map` and `Stream`, and do some more `Enum` tricks, which I'm finding to be superfun! It's Eid festival today and I'm wishing everyone a great one!