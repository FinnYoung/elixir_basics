#### Integers
`1234`, `0xcafe`, `0o765`, `0b1010`, `1_000_000`

#### Atoms
`:fred` `:is_binary?` `:var@2` `:<>` `:===` `:"func/3"` `:"long john silver"`

#### Floating-Point Numbers
`1.0`   `0.2455`   `0.314159e1` `314159.0e-5`

#### Regular Expressions
`~r{regexp}` or `~r{regexp}opts`

```
Regex.run ~r{[aeiou]}, "caterpillar" # ["a"]
Regex.scan ~r{[aeiou]}, "caterpillar" # [["a"], ["e"], ["i"], ["a"]]
Regex.split ~r{[aeiou]}, "caterpillar" # ["c", "t", "rp", "ll", "r"]
Regex.replace ~r{[aeiou]}, "caterpillar", "*" # "c*t*rp*ll*r"
```

#### Tuples
A tuple is an ordered collection of values. Once created a tuple cannot be modified.
```
{ 1, 2 }
{ :ok, 42, "next" }
{ :error, :enoent }
{ status, file } = File.open("Rakefile") # {:ok, #PID<0.39.0>}
```

#### Lists
```
[1,2,3]++[4,5,6] # [1, 2, 3, 4, 5, 6]
[1, 2, 3, 4] -- [2, 4] # [1, 3]
1 in [1,2,3,4] # true
"wombat" in [1, 2, 3, 4] # false
```

#### Keyword Lists
```
[ name: "Bob", city: "Dallas", likes: "Programming" ]
```

#### Maps
```
states = %{ "AL" => "Alabama", "WI" => "Wisconsin" }
responses = %{ { :error, :enoent } => :fatal, { :error, :busy } => :retry }
colors = %{ :red => 0xff0000, :green => 0x00ff00, :blue => 0x0000ff }
```

#### Binaries
```
bin = << 1, 2 >> # <<1, 2>>
byte_size bin # 2
```

```
bin = <<3 :: size(2), 5 :: size(4), 1 :: size(2)>> # <<213>>
:io.format("~-8.2b~n", :binary.bin_to_list(bin)) # 11010101
byte_size bin # 1
```

## Operators

#### Comparison operators
```
a === b # strict equality (so 1 === 1.0 is false)
a !== b # strict inequality (so 1 !== 1.0 is true)
a == b  # value equality (so 1 == 1.0 is true)
a != b  # value inequality (so 1 != 1.0 is false)
a > b   # normal comparison
a >= b  # :
a < b   # :
a <= b  # :
```
#### Boolean operators
Expect true or false as first argument.
```
a or b   # true if a is true, otherwise b
a and b  # false if a is false, otherwise b
not a    # false if a is true, true otherwise
```

#### Relaxed Boolean operators
Expect arguments of any type.
```
a || b  # a if a is truthy, otherwise b
a && b  # b if a is truthy, otherwise a
!a      # false if a is truthy, otherwise true
```

#### Arithmetic operators
```
+ - * / div rem
```

#### Join operators
```
binary1 <> binary2 # concatenates two binaries
list1   ++ list2   # concatenates two lists
list1   -- list2   # returns elements in list1 not in list2
```

#### The in operator
`a in enum` # tests if a is included in enum

## Anonymous Functions
```
sum = fn (a, b) -> a + b end
f1 = fn a, b -> a * b end
greet = fn -> IO.puts "Hi there" end
sum.(1, 2) # 3
```

```
handle_open = fn
  {:ok, file} -> "Read data: #{IO.read(file, :line)}"
  {_, error} -> "Error: #{:file.format_error(error)}"
end
handle_open.(File.open("files/file1.exs"))
```

#### Functions Can Return Functions
```
fun1 = fn -> fn -> "Hello" end end
fun1 = fn -> (fn -> "Hello" end) end
fun1.()
other = fun1.()
fun1.().() # "Hello"
other.() # "Hello"
```

```
fun1 = fn ->
          fn ->
             "Hello"
          end
       end
```

```
greeter = fn name -> (fn -> "Hello #{name}" end) end
dave_greeter = greeter.("Bob")
dave_greeter.() #"Hello Bob"
```
#### Parameterized Functions
```
add_n = fn n -> (fn other -> n + other end) end
add_two = add_n.(2)
add_five = add_n.(5)
add_two.(3) # 5
add_five.(7) # 12
```

#### Passing Functions As Arguments
```
times_2 = fn n -> n * 2 end
apply = fn (fun, value) -> fun.(value) end
apply.(times_2, 6) # 12

list = [1, 3, 5, 7, 9]
Enum.map list, fn elem -> elem * 2 end    # [2, 6, 10, 14, 18]
```

#### The & Notation
```
add_one = &(&1 + 1) # same as add_one = fn (n) -> n + 1 end
add_one.(44) # 45

square = &(&1 * &1)
square.(8) # 64

speak = &(IO.puts(&1))
speak.("Hello") # Hello

divrem = &{ div(&1,&2), rem(&1,&2) }
divrem.(13, 5) # {2, 3}

l = &length/1 # &:erlang.length/1
l.([1,3,5,7]) # 4

len = &Enum.count/1 # &Enum.count/1
len.([1,2,3,4]) # 4
```

## Modules and Named Functions
```
defmodule Times do
  def double(n) do
    n*2
  end
end

Times.double 4 # 8

def double(n), do: n * 2

def greet(greeting, name), do: (
  IO.puts greeting
  IO.puts "How're you doing, #{name}?"
)
```
#### Function Calls and Pattern Matching
```
defmodule Factorial do
  def of(0), do: 1
  def of(n), do: n * of(n-1)
end

```

#### Guard Clauses
```
defmodule Guard do
  def what_is(x) when is_number(x) do
    IO.puts "#{x} is a number"
  end
  def what_is(x) when is_list(x) do
    IO.puts "#{inspect(x)} is a list"
  end
  def what_is(x) when is_atom(x) do
    IO.puts "#{x} is an atom"
  end
end

defmodule Factorial do
  def of(0), do: 1
  def of(n) when n > 0 do
    n * of(n-1)
  end
end
```

#### Default Parameters
```
defmodule Example do
  def func(p1, p2 \\ 2, p3 \\ 3, p4) do
    IO.inspect [p1, p2, p3, p4]
  end
end

Example.func("a", "b")           # => ["a",2,3,"b"]
Example.func("a", "b", "c")      # => ["a","b",3,"c"]
Example.func("a", "b", "c", "d") # => ["a","b","c","d"]

defmodule Params do
  def func(p1, p2 \\ 123)
  def func(p1, p2) when is_list(p1) do
    "You said #{p2} with a list"
  end
  def func(p1, p2) do
    "You passed in #{p1} and #{p2}"
  end
end

IO.puts Params.func(99)          # You passed in 99 and 123
IO.puts Params.func(99, "cat")   # You passed in 99 and cat
IO.puts Params.func([99])        # You said 123 with a list
IO.puts Params.func([99], "dog") # You said dog with a list

defmodule Chop do
  def guess(result, range) do
    guess(result, range, div(range.last + range.first, 2))
  end

  def guess(result, range, try) when result == try do
    result
  end

  def guess(result, range, try) when result > try do
    IO.puts "Is it #{try}"
    guess(result, try..range.last, div(try + range.last, 2))
  end

  def guess(result, range, try) when result < try do
    IO.puts "Is it #{try}"
    guess(result, range.first..try, div(range.first + try, 2))
  end
end
```

#### Private Functions
```
defp fun(a), do: false
```

#### |> - Pipe Operator
```
people = DB.find_customers
orders = Orders.for_customers(people)
tax    = sales_tax(orders, 2013)
filing = prepare_filing(tax)
```
the same as
```
filing = DB.find_customers
           |> Orders.for_customers
           |> sales_tax(2013)
           |> prepare_filing

```

`val |> f(a,b)` the same as `f(val,a,b)`

```
list
|> sales_tax(2013)
|> prepare_filing
```
the same as `prepare_filing(sales_tax(list, 2013))`

#### Modules
```
defmodule Mod do
  def func1 do
    IO.puts "in func1"
  end
  def func2 do
    func1
    IO.puts "in func2"
  end
end

Mod.func1
Mod.func2
```

```
defmodule Outer do
  defmodule Inner do
    def inner_func do
    end
  end
  def outer_func do
    Inner.inner_func
  end
end

Outer.outer_func
Outer.Inner.inner_func
```

```
defmodule Mix.Tasks.Doctest do
  def run do
  end
end

Mix.Tasks.Doctest.run
```

#### The import Directive
```
import Module [, only:|except: ]
import List, only: [flatten: 1, duplicate: 2]
```

```
defmodule Example do
  def func1 do
    List.flatten [1,[2,3],4]
  end
  def func2 do
    import List, only: [flatten: 1]
    flatten [5,[6,7],8]
  end
end
```

#### The alias Directive
```
defmodule Example do
  def func do
    alias Mix.Tasks.Doctest, as: Doctest
    doc = Doctest.setup
    doc.run(Doctest.defaults)
  end
end
```

#### The require Directive
The require directive ensures that the given module is loaded before your code tries to use any of the macros it defines.

#### Module Attributes
(ruby constants)

```
defmodule Example do
  @author "Bob"
  def get_author do
    @author
  end
end

Example.get_author
```

```
defmodule Example do
  @attr "one"
  def first, do: @attr

  @attr "two"
  def second, do: @attr
end
```

#### Module names
Module names are just atoms

```
is_atom IO # true
to_string IO # "Elixir.IO"
:"Elixir.IO" === IO # true
:"Elixir.IO".puts 123 # 123
```

#### Calling a Function in an Erlang Library
```
:io.format("The number is ~3.1f~n", [5.678]) # The number is 5.7
```

#### Heads and Tails
```
[head | tail] = [1, 2, 3]
head # 1
tail # [2, 3]
```

#### Using Head and Tail to Process a List
```
defmodule MyList do
  def len([]),             do: 0
  def len([_head | tail]), do: 1 + len(tail)
end
```

#### Using Head and Tail to Build a List
```
def square([]),              do: []
def square([ head | tail ]), do: [ head*head | square(tail) ]
```

#### Creating a Map Function
```
def map([], _func),             do: []
def map([ head | tail ], func), do: [ func.(head) | map(tail, func) ]

MyList.map [1,2,3,4], fn (n) -> n*n end
```

#### Keeping Track of Values During Recursion
```
defmodule MyList do
  def sum(list), do: _sum(list, 0)

  defp _sum([], total),              do: total
  defp _sum([ head | tail ], total), do: _sum(tail, head+total)
end
```

#### Generalizing Sum Function
```
defmodule MyList do
  def reduce([], value, _), do: value
  def reduce([head | tail], value, func) do
    reduce(tail, func.(head, value), func)
  end

  def mapsum([], _func), do: 0
  def mapsum([head | tail], func) do
    func.(head) + mapsum(tail, func)
  end
end

MyList.reduce([1,2,3,4,5], 0, &(&1 + &2)) # 15
MyList.mapsum [1, 2, 3], &(&1 * &1) # 14

#### Max value in list

defmodule MyList do
  def max([head | tail]) do
    _max(tail, head)
  end
  def max([]), do: nil

  defp _max([], value), do: value
  defp _max([head | tail], current_max) when head > current_max do
    _max(tail, head)
  end
  defp _max([head | tail], current_max) when head <= current_max do
    _max(tail, current_max)
  end
end
```

#### Lists of lists
```
defmodule WeatherHistory do
  def for_location([], target_loc), do: []
  def for_location([ head = [_, target_loc, _, _ ] | tail], target_loc) do
    [ head | for_location(tail, target_loc) ]
  end
  def for_location([ _ | tail], target_loc), do: for_location(tail, target_loc)
end

defmodule MyList do
  def span(from, from) do
    [from]
  end

  def span(from, to) do
    [from | span(from + 1, to)]
  end

end
```
#### Dictionaries
`Keyword` if more than one entry with the same key or ordering required. `Map` for patten matching. `HashDict` for more than hundred entries.

```
defmodule Sum do
  def values(dict) do
    dict |> Dict.values |> Enum.sum
  end
end

hd = [ one: 1, two: 2, three: 3 ] |> Enum.into HashDict.new
IO.puts Sum.values(hd) # => 6

map = %{ four: 4, five: 5, six: 6 }
IO.puts Sum.values(map) # => 15

kw_list = [name: "Bob", likes: "Programming", where: "Dallas"] # [name: "Bob", likes: "Programming", where: "Dallas"]
hashdict = Enum.into kw_list, HashDict.new # HashDict<[name: "Bob", where: "Dallas", likes: "Programming"]>
map = Enum.into kw_list, Map.new # %{likes: "Programming", name: "Bob", where: "Dallas"}

kw_list[:name] # "Bob"
hashdict[:likes] # "Programming"
map[:where] # "Dallas"

hashdict = Dict.drop(hashdict, [:where, :likes]) # #HashDict<[name: "Bob"]>
hashdict = Dict.put(hashdict, :also_likes, "Ruby") # #HashDict<[name: "Bob", also_likes: "Ruby"]>
combo = Dict.merge(map, hashdict) # %{also_likes: "Ruby", likes: "Programming", name: "Bob", where: "Dallas"}

kw_list = [name: "Bob", likes: "Programming", likes: "Elixir"] # [name: "Bob", likes: "Programming", likes: "Elixir"]
kw_list[:likes] # "Programming"
Dict.get(kw_list, :likes) # "Programming"
Keyword.get_values(kw_list, :likes) # ["Programming", "Elixir"]
```
#### Pattern Matching and Updating Maps
```
person = %{ name: "Bob", height: 1.88 } # %{height: 1.88, name: "Bob"}
%{ name: a_name } = person # %{height: 1.88, name: "Bob"}
a_name # "Bob"
%{ name: _, height: _ } = person # %{height: 1.88, name: "Bob"}
%{ name: "Bob" } = person # %{height: 1.88, name: "Bob"}

people = [
  %{ name: "Grumpy",    height: 1.24 },
  %{ name: "Bob",       height: 1.88 },
  %{ name: "Dopey",     height: 1.32 },
  %{ name: "Shaquille", height: 2.16 },
  %{ name: "Sneezy",    height: 1.28 }
]

for person = %{ height: height } <- people,
  height > 1.5,
  do: IO.inspect person

%{height: 1.88, name: "Bob"} %{height: 2.16, name: "Shaquille"}

defmodule HotelRoom do
  def book(%{name: name, height: height})
  when height > 1.9 do
    IO.puts "Need extra long bed for #{name}"
  end

  def book(%{name: name, height: height})
  when height < 1.3 do
    IO.puts "Need low shower controls for #{name}"
  end

  def book(person) do
    IO.puts "Need regular bed for #{person.name}"
  end
end

people |> Enum.each(&HotelRoom.book/1)
```
#### Updating a Map
```
m = %{ a: 1, b: 2, c: 3 } # %{a: 1, b: 2, c: 3}
m1 = %{ m | b: "two", c: "three" } # %{a: 1, b: "two", c: "three"}
m2 = %{ m1 | a: "one" } # %{a: "one", b: "two", c: "three"}
m3 = Dict.put_new(m, :z, 1) # %{a: 1, b: 2, c: 3, z: 1}
```
####  Maps and Structs
```
defmodule Subscriber do
  defstruct name: "", paid: false, over_18: true
end

s1 = %Subscriber{} # %Subscriber{name: "", over_18: true, paid: false}
s2 = %Subscriber{ name: "Bob" } # %Subscriber{name: "Bob", over_18: true, paid: false}
s3 = %Subscriber{ name: "Mary", paid: true } # %Subscriber{name: "Mary", over_18: true, paid: true}

s3.name # "Mary"
%Subscriber{name: a_name} = s3 # %Subscriber{name: "Mary", over_18: true, paid: true}
a_name # "Mary"
s4 = %Subscriber{ s3 | name: "Marie"} # %Subscriber{name: "Marie", over_18: true, paid: true}
```

```
defmodule Attendee do
  defstruct name: "", paid: false, over_18: true

  def may_attend_after_party(attendee = %Attendee{}) do
    attendee.paid && attendee.over_18
  end

  def print_vip_badge(%Attendee{name: name}) when name != "" do
    IO.puts "Very cheap badge for #{name}"
  end

  def print_vip_badge(%Attendee{}) do
    raise "missing name for badge"
  end
end

a1 = %Attendee{name: "Bob", over_18: true} %Attendee{name: "Bob", over_18: true, paid: false}
Attendee.may_attend_after_party(a1) # false
a2 = %Attendee{a1 | paid: true} # %Attendee{name: "Bob", over_18: true, paid: true}
Attendee.may_attend_after_party(a2) # true
Attendee.print_vip_badge(a2) # Very cheap badge for Bob
a3 = %Attendee{}
%Attendee{name: "", over_18: true, paid: false} iex> Attendee.print_vip_badge(a3) # (RuntimeError) missing name for badge
```
#### @derive to access fields using square brackets
```
defmodule Attendee do
  @derive Access
  defstruct name: "", over_18: false
end

a = %Attendee{name: "Sally", over_18: true} %Attendee{name: "Sally", over_18: true}
a[:name] # "Sally"
a[:over_18] # true
a.name # "Sally"
```
#### Nested Dictionary Structures
```
defmodule Customer do
  defstruct name: "", company: ""
end

defmodule BugReport do
  defstruct owner: %{}, details: "", severity: 1
end

report = %BugReport{owner: %Customer{name: "Bob", company: "Sweethome"}, details: "broken"}
report.owner.company # "Sweethome"

report = %BugReport{ report | owner: %Customer{ report.owner | company: "Cmp" }}
put_in(report.owner.company, "Cmp") # %BugReport{details: "broken", owner: %Customer{company: "Cmp", name: "Bob"}, severity: 1}

update_in(report.owner.name, &("Mr. " <> &1)) # %BugReport{details: "broken", owner: %Customer{company: "Cmp", name: "Mr. Bob"}, severity: 1}
```
#### Nested Accessors and Nonstructs
```
report = %{ owner: %{ name: "Bob", company: "Sweethome" }, severity: 1} # %{owner: %{company: "Sweethome", name: "Bob"}, severity: 1}
put_in(report[:owner][:company], "Cmp") # %{owner: %{company: "Cmp", name: "Bob"}, severity: 1}
update_in(report[:owner][:name], &("Mr. " <> &1)) # %{owner: %{company: "Sweethome", name: "Mr. Bob"}, severity: 1}
```
#### Dynamic (Runtime) Nested Accessors
```
nested = %{
    buttercup: %{
      actor: %{
        first: "Robin",
        last: "Wright"
      },
      role: "princess"
    },
    westley: %{
      actor: %{
        first: "Carey",
        last: "Ewes"
      },
    role: "music listener"
  }
}

IO.inspect get_in(nested, [:buttercup]) # %{actor: %{first: "Robin", last: "Wright"}, role: "princess"}
IO.inspect get_in(nested, [:buttercup, :actor]) # %{first: "Robin", last: "Wright"}
IO.inspect get_in(nested, [:buttercup, :actor, :first]) # "Robin"

IO.inspect put_in(nested, [:westley, :actor, :last], "Elwes")
```

```
authors = [
  %{ name: "José", language: "Elixir" },
  %{ name: "Matz", language: "Ruby" },
  %{ name: "Larry", language: "Perl" }
]


languages_with_an_r = fn (:get, collection, next_fn) ->
  for row <- collection do
    if String.contains?(row.language, "r") do
      next_fn.(row)
    end
  end
end

IO.inspect get_in(authors, [languages_with_an_r, :name]) # [ "José", nil, "Larry" ]
```
#### Sets
```
set1 = Enum.into 1..5, HashSet.new # #HashSet<[1, 2, 3, 4, 5]>
Set.member? set1, 3 # true

set2 = Enum.into 3..8, HashSet.new # #HashSet<[3, 4, 5, 6, 7, 8]>
Set.union set1, set2 # #HashSet<[7, 6, 4, 1, 8, 2, 3, 5]>
Set.difference set1, set2 # #HashSet<[1, 2]>
Set.difference set2, set1 # #HashSet<[6, 7, 8]>
Set.intersection set1, set2 # #HashSet<[3, 4, 5]>
```
#### Enum
Convert any collection into a list:
```
list = Enum.to_list 1..5 # [1, 2, 3, 4, 5]
```
Concatenate collections:
```
Enum.concat([1,2,3], [4,5,6]) # [1, 2, 3, 4, 5, 6]
Enum.concat [1,2,3], 'abc' # [1, 2, 3, 97, 98, 99]
```
Create collections whose elements are some function of the original:
```
Enum.map(list, &(&1 * 10)) # [10, 20, 30, 40, 50]
Enum.map(list, &String.duplicate("*", &1)) # ["*", "**", "***", "****", "*****"]
```
Select elements by position or criteria:
```
Enum.at(10..20, 3) # 13
Enum.at(10..20, 20) # nil
Enum.at(10..20, 20, :no_one_here) # :no_one_here
Enum.filter(list, &(&1 > 2)) # [3, 4, 5]
Enum.filter(list, &Integer.is_even/1) # [2, 4]
Enum.reject(list, &Integer.is_even/1) # [1, 3, 5]
```
Sort and compare elements:
```
Enum.sort ["there", "was", "a", "crooked", "man"] # ["a", "crooked", "man", "there", "was"]
Enum.sort ["there", "was", "a", "crooked", "man"], &(String.length(&1) <= String.length(&2)) # ["a", "man", "was", "there", "crooked"]
Enum.max ["there", "was", "a", "crooked", "man"] # "was"
Enum.max_by ["there", "was", "a", "crooked", "man"], &String.length/1 # "crooked"
```
Split a collection:
```
Enum.take(list, 3) # [1, 2, 3]
Enum.take_every list, 2 # [1, 3, 5]
Enum.take_while(list, &(&1 < 4)) # [1, 2, 3]
Enum.split(list, 3) # {[1, 2, 3], [4, 5]}
Enum.split_while(list, &(&1 < 4)) # {[1, 2, 3], [4, 5]}
```
Join a collection:
```
Enum.join(list) # "12345"
Enum.join(list, ", ") # "1, 2, 3, 4, 5"
```
Predicate operations:
```
Enum.all?(list, &(&1 < 4)) # false
Enum.any?(list, &(&1 < 4)) # true
Enum.member?(list, 4) # true
Enum.empty?(list) # false
```
Merge collections:
```
Enum.zip(list, [:a, :b, :c]) # [{1, :a}, {2, :b}, {3, :c}]
Enum.with_index(["once", "upon", "a", "time"]) # [{"once", 0}, {"upon", 1}, {"a", 2}, {"time", 3}]
```
Fold elements into a single value:
```
Enum.reduce(1..100, &(&1+&2)) # 5050

Enum.reduce(["now", "is", "the", "time"],fn word, longest ->
  if String.length(word) > String.length(longest) do
    word
  else
    longest
  end
end # "time"

Enum.reduce(["now", "is", "the", "time"], 0, fn word, longest ->
  if String.length(word) > longest do
    String.length(word)
  else
    longest
  end
end
```
Deal a hand of cards:
```
deck = for rank <- '23456789TJQKA', suit <- 'CDHS', do: [suit,rank]
['C2', 'D2', 'H2', 'S2', 'C3', 'D3', ... ]

deck |> shuffle |> take(13)
['DQ', 'S6', 'HJ', 'H4', 'C7', 'D6', 'SJ', 'S9', 'D7', 'HA', 'S4', 'C2', 'CT']

hands = deck |> shuffle |> chunk(13)
[['D8', 'CQ', 'H2', 'H3', 'HK', 'H9', 'DK', 'S9', 'CT', 'ST', 'SK', 'D2', 'HA'],
 ['C5', 'S3', 'CK', 'HQ', 'D3', 'D4', 'CA', 'C8', 'S6', 'DQ', 'H5', 'S2', 'C4'],
 ['C7', 'C6', 'C2', 'D6', 'D7', 'SA', 'SQ', 'H8', 'DT', 'C3', 'H7', 'DA', 'HT'],
 ['S5', 'S4', 'C9', 'S8', 'D5', 'H4', 'S7', 'SJ', 'HJ', 'D9', 'DJ', 'CJ', 'H6']]
```
#### Streams—Lazy Enumerables
```
s = Stream.map [1, 3, 5, 7], &(&1 + 1)
Enum.to_list s # [2, 4, 6, 8]

squares = Stream.map [1, 2, 3, 4], &(&1*&1)
plus_ones = Stream.map squares, &(&1+1)
odds = Stream.filter plus_ones, fn x -> rem(x,2) == 1 end
Enum.to_list odds # [5, 17]

[1,2,3,4]
|> Stream.map(&(&1*&1))
|> Stream.map(&(&1+1))
|> Stream.filter(fn x -> rem(x,2) == 1 end) |> Enum.to_list
```

```
IO.puts File.open!("/usr/share/dict/words")
        |> IO.stream(:line)
        |> Enum.max_by(&String.length/1)

```
Slower than prev example:
```
IO.puts File.stream!("/usr/share/dict/words") |> Enum.max_by(&String.length/1)
```
#### Infinite Streams
```
Stream.map(1..10_000_000, &(&1+1)) |> Enum.take(5) # [2, 3, 4, 5, 6]
```
**Stream.cycle**
```
Stream.cycle(~w{ green white }) |>
    Stream.zip(1..5) |>
    Enum.map(fn {class, value} ->
        ~s{<tr class="#{class}"><td>#{value}</td></tr>\n} end) |>
    IO.puts

<tr class="green"><td>1</td></tr>
<tr class="white"><td>2</td></tr>
<tr class="green"><td>3</td></tr>
<tr class="white"><td>4</td></tr>
<tr class="green"><td>5</td></tr>
```
**Stream.repeatedly**
```
Stream.repeatedly(fn -> true end) |> Enum.take(3) # [true, true, true]
Stream.repeatedly(&:random.uniform/0) |> Enum.take(3) # [0.7230402056221108, 0.94581636451987, 0.5014907142064751]
```
**Stream.iterate**
```
Stream.iterate(0, &(&1+1)) |> Enum.take(5) # [0, 1, 2, 3, 4]
Stream.iterate(2, &(&1*&1)) |> Enum.take(5) # [2, 4, 16, 256, 65536]
Stream.iterate([], &[&1]) |> Enum.take(5) # [[], [[]], [[[]]], [[[[]]]], [[[[[]]]]]]
```
**Stream.unfold**
```
fn state -> { stream_value, new_state } end
Stream.unfold({0,1}, fn {f1,f2} -> {f1, {f2, f1+f2}} end) |> Enum.take(15) # [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377]
```
**Stream.resource**
```
Stream.resource(fn -> File.open("sample") end,
                fn file ->
                  case IO.read(file, :line) do
                    line when is_binary(line) -> { [line], file }
                    _ -> {:halt, file}
                  end
                end,
                fn file -> File.close!(file) end)

```
```
defmodule Countdown do
  def sleep(seconds) do
    receive do
      after seconds*1000 -> nil
    end
  end

  def say(text) do
    spawn fn -> :os.cmd('say #{text}') end
  end

  def timer do
    Stream.resource(
      fn -> # the number of seconds to the start of the next minute
        {_h,_m,s} = :erlang.time
        60 - s - 1
      end,

      fn # wait for the next second, then return its countdown
        0 ->
          {:halt, 0}

        count ->
          sleep(1)
          { [inspect(count)], count - 1 }
      end,

      fn _ -> end # nothing to deallocate
    )
  end
end

counter = Countdown.timer
printer = counter |> Stream.each(&IO.puts/1)
speaker = printer |> Stream.each(&Countdown.say/1)
speaker |> Enum.take(5)
```
#### The Collectable Protocol
```
Enum.into 1..5, [] # [1, 2, 3, 4, 5]
Enum.into 1..5, [100, 101] # [100, 101, 1, 2, 3, 4, 5]
Enum.into IO.stream(:stdio, :line), IO.stream(:stdio, :line)
```
#### Comprehensions
```
for x <- [1, 2, 3, 4, 5], do: x * x # [1, 4, 9, 16, 25]
for x <- [1, 2, 3, 4, 5], x < 4, do: x * x # [1, 4, 9]
for x <- [1,2], y <- [5,6], do: x * y # [5, 6, 10, 12]

min_maxes = [{1,4}, {2,3}, {10, 15}]
for {min,max} <- min_maxes, n <- min..max, do: n # [1, 2, 3, 4, 2, 3, 10, 11, 12, 13, 14, 15]

first8 = [1,2,3,4,5,6,7,8]
for x <- first8, y <- first8, x >= y, rem(x*y, 10)==0, do: {x, y} # [{5, 2}, {5, 4}, {6, 5}, {8, 5}]

reports = [dallas: :hot, minneapolis: :cold, dc: :muggy, la: :smoggy]
for { city, weather } <- reports, do: { weather, city } # [hot: :dallas, cold: :minneapolis, muggy: :dc, smoggy: :la]

for << ch <- "hello" >>, do: ch # 'hello'
for << ch <- "hello" >>, do: <<ch>> # ["h", "e", "l", "l", "o"]

for << << b1::size(2), b2::size(3), b3::size(3) >> <- "hello" >>,
  do: "0#{b1}#{b2}#{b3}" # ["0150", "0145", "0154", "0154", "0157"]

name = "Bob"
for name <- ["cat", "dog"], do: String.upcase(name) # ["CAT", "DOG"]
name # "Bob"

for x <- ~w{ cat dog }, into: %{}, do: { x, String.upcase(x) } # %{"cat" => "CAT", "dog" => "DOG"}
for x <- ~w{ cat dog }, into: Map.new, do: { x, String.upcase(x) } # %{"cat" => "CAT", "dog" => "DOG"}
for x <- ~w{ cat dog }, into: %{"ant" => "ANT"}, do: { x, String.upcase(x) } # %{"ant" => "ANT", "cat" => "CAT", "dog" => "DOG"}

for x <- ~w{ cat dog }, into: IO.stream(:stdio,:line), do: "<<#{x}>>\n" <<cat>>
<<dog>>
```
#### Strings
```
IO.puts "start" IO.write "
  my
  string
"
IO.puts "end"
```
produces
```
start
  my
  string
end
```
```
IO.puts "start"
IO.write """
  my
  string
"""
IO.puts "end"
```
produces
```
start
my
string
end
```
#### Sigils
Delimiters:
```
<...>, {...}, [...], (...), |...|, /.../, "...", '...'
```
types:
```
~C A character list with no escaping or interpolation
~c A character list, escaped and interpolated just like a single-quoted string
~R A regular expression with no escaping or interpolation
~r A regular expression, escaped and interpolated
~S A string with no escaping or interpolation
~s A string, escaped and interpolated just like a double-quoted string
~W A list of whitespace-delimited words, with no escaping or interpolation
~w A list of whitespace-delimited words, with escaping and interpolation
```
examples:
```
~C[1\n2#{1+2}] # '1\\n2\#{1+2}'
~c"1\n2#{1+2}" # '1\n23'
~S[1\n2#{1+2}] # "1\\n2\#{1+2}"
~s/1\n2#{1+2}/ # "1\n23"
~W[the c#{'a'}t sat on the mat] # ["the", "c\#{'a'}t", "sat", "on", "the", "mat"]
~w[the c#{'a'}t sat on the mat] # ["the", "cat", "sat", "on", "the", "mat"]
```
optional type specifier: a(atoms), c(list), s(strings of characters)
```
~w[the c#{'a'}t sat on the mat]a # [:the, :cat, :sat, :on, :the, :mat]
~w[the c#{'a'}t sat on the mat]c # ['the', 'cat', 'sat', 'on', 'the', 'mat']
~w[the c#{'a'}t sat on the mat]s # ["the", "cat", "sat", "on", "the", "mat"]

~w"""
the
cat
sat
"""
["the", "cat", "sat"]

~r"""
hello
"""i
~r/hello\n/i
```
#### Single-Quoted Strings - Lists of Character Codes
```
str = 'wombat' # 'wombat'
is_list str # true
length str # 6
Enum.reverse str # 'tabmow'

str = 'wombat' # 'wombat'
:io.format "~w~n", [ str ] # [119,111,109,98,97,116]
List.to_tuple str # {119, 111, 109, 98, 97, 116}
str ++ [0] # [119, 111, 109, 98, 97, 116, 0]

'∂x/∂y' # [8706, 120, 47, 8706, 121]
'pole' ++ 'vault' # 'polevault'
'pole' -- 'vault' # 'poe'

List.zip [ 'abc', '123' ] # [{97, 49}, {98, 50}, {99, 51}]

[ head | tail ] = 'cat'
head # 99
tail # 'at'
[ head | tail ] # 'cat'
```

```
defmodule Parse do
  def number([ ?- | tail ]), do: _number_digits(tail, 0) * -1
  def number([ ?+ | tail ]), do: _number_digits(tail, 0)
  def number(str), do: _number_digits(str, 0)

  defp _number_digits([], value), do: value
  defp _number_digits([ digit | tail ], value)
  when digit in '0123456789' do
    _number_digits(tail, value*10 + digit - ?0)
  end
  defp _number_digits([ non_digit | _ ], _) do
    raise "Invalid digit '#{[non_digit]}'"
  end
end

Parse.number('123') # 123
Parse.number('-123') # -123
Parse.number('+123') # 123
```
#### Binaries
```
b = << 1, 2, 3 >> # <<1, 2, 3>>
byte_size b # 3
bit_size b # 24

b = << 1::size(2), 1::size(3) >> # <<9::size(5)>>
byte_size b # 1
bit_size b # 5

int = << 1 >> # <<1>>
float = << 2.5 :: float >> # <<64, 4, 0, 0, 0, 0, 0, 0>>
mix = << int :: binary, float :: binary >> # <<1, 64, 4, 0, 0, 0, 0, 0, 0>>
```
#### Double-Quoted Strings Are Binaries
```
dqs = "∂x/∂y" # "∂x/∂y"
String.length dqs # 5
byte_size dqs # 9
String.at(dqs, 0) # "∂"
String.codepoints(dqs) # ["∂", "x", "/", "∂", "y"]
String.split(dqs, "/") # ["∂x", "∂y"]
```
#### Strings and Elixir Libraries
```
String.at("∂og", 0) # "∂"
String.at("∂og", -1) # "g"
String.capitalize "école" # "École"
String.capitalize "ÎÎÎÎÎ" # "Îîîîî"
String.codepoints("∂x/∂y") # ["∂", "x", "/", "∂", "y"]
String.downcase "ØRSteD" # "ørsted"
String.duplicate "Ho! ", 3 # "Ho! Ho! Ho! "
String.ends_with? "string", ["elix", "stri", "ring"] # true
String.first "∂og" # "∂"
String.last "∂og" # "g"
String.length "∂x/∂y" # 5
String.ljust("cat", 5) # "cat  "
String.lstrip "\t\f Hello\t\n" # "Hello\t\n"
String.lstrip "!!!SALE!!!", ?! # "SALE!!!"
String.printable? "José" # true
String.printable? "\x{0000} a null" # false
String.replace "the cat on the mat", "at", "AT"                         # "the cAT on the mAT"
String.replace "the cat on the mat", "at", "AT", global: false          # "the cAT on the mat"
String.replace "the cat on the mat", "at", "AT", insert_replaced: 0     # "the catAT on the matAT"
String.replace "the cat on the mat", "at", "AT", insert_replaced: [0,2] # "the catATat on the matATat"
String.reverse "pupils" # "slipup"
String.reverse "∑ƒ÷∂" # "∂÷ƒ∑"
String.rjust("cat", 5, ?>) # ">>cat"
String.rstrip(" line \r\n") " line"
String.rstrip "!!!SALE!!!", ?! # "!!!SALE"
String.slice "the cat on the mat", 4, 3 # "cat"
String.slice "the cat on the mat", -3, 3 # "mat"
String.split " the cat on the mat "                   # ["the", "cat", "on", "the", "mat"]
String.split "the cat on the mat", "t"                # ["", "he ca", " on ", "he ma", ""]
String.split "the cat on the mat", ~r{[ae]}           # ["th", " c", "t on th", " m", "t"]
String.split "the cat on the mat", ~r{[ae]}, parts: 2 # ["th", " cat on the mat"]
String.starts_with? "string", ["elix", "stri", "ring"] # true
String.strip "\t Hello \r\n" # "Hello"
String.strip "!!!SALE!!!", ?! # "SALE"
String.upcase "José Ørstüd" # "JOSÉ ØRSTÜD"
String.valid_character? "∂" # true
String.valid_character? "∂og" # false


defmodule MyString do
  def each(str, func), do: _each(String.next_codepoint(str), func)
  defp _each({codepoint, rest}, func) do
    func.(codepoint)
    _each(String.next_codepoint(rest), func)
  end
  defp _each(nil, _), do: []
end
MyString.each "∂og", fn c -> IO.puts c end
```
#### Binaries and Pattern Matching
Types: binary, bits, bitstring, bytes, float, integer, utf8, utf16, and utf32.
Qualifiers: size(n), signed/unsigned, big/little/native (endianness)
```
<< length::unsigned-integer-size(12), flags::bitstring-size(4) >>  = data
```
```
defmodule Utf8 do
  def each(str, func) when is_binary(str), do: _each(str, func)
  defp _each(<< head :: utf8, tail :: binary >>, func) do
    func.(head)
    _each(tail, func)
  end
  defp _each(<<>>, _func), do: []
end

Utf8.each "∂og", fn char -> IO.puts char end # 8706 111 103
```
#### if and unless
```
if 1 == 1, do: "true part", else: "false part" # "true part"
if 1 == 2, do: "true part", else: "false part" # "false part"

if 1 == 1 do
  "true part"
else
  "false part"
end # "true part"

unless 1 == 1, do: "error", else: "OK" # "OK"
unless 1 == 2, do: "OK", else: "error" # "OK"

unless 1 == 2 do
  "OK"
else
  "error"
end # "OK"
```
#### cond
```
defmodule FizzBuzz do
  def upto(n) when n > 0, do: _downto(n, [])

  defp _downto(0, result), do: result
  defp _downto(current, result) do
    next_answer =
      cond do
        rem(current, 3) == 0 and rem(current, 5) == 0 ->
          "FizzBuzz"
        rem(current, 3) == 0 ->
          "Fizz"
        rem(current, 5) == 0 ->
          "Buzz"
        true ->
          current
      end
    _downto(current - 1, [next_answer | result])
  end
end

defmodule FizzBuzz do
  def upto(n) when n > 0 do
    1..n |> Enum.map(&fizzbuzz/1)
  end

  defp fizzbuzz(n) do
    cond do
      rem(n, 3) == 0 and rem(n, 5) == 0 ->
        "FizzBuzz"
      rem(n, 3) == 0 ->
        "Fizz"
      rem(n, 5) == 0 ->
        "Buzz"
      true ->
        n
    end
  end
end

defmodule FizzBuzz do
  def upto(n) when n > 0 do
    1..n |> Enum.map(&fizzbuzz/1)
  end

  defp fizzbuzz(n) when rem(n, 3) == 0 and rem(n, 5) == 0, do: "FizzBuzz"
  defp fizzbuzz(n) when rem(n, 3) == 0, do: "Fizz"
  defp fizzbuzz(n) when rem(n, 5) == 0, do: "Buzz"
  defp fizzbuzz(n), do: n
end
```
#### case
```
case File.open("case.ex") do
{:ok, file} ->
  IO.puts "First line: #{IO.read(file, :line)}"
{:error, reason} ->
  IO.puts "Failed to open file: #{reason}"
end

defmodule Users do
  bob = %{name: "Bob", state: "TX", likes: "food"}

  case bob do
    %{state: some_state} = person ->
      IO.puts "#{person.name} lives in #{some_state}"
    _ ->
      IO.puts "No matches"
  end
end

defmodule Bouncer do
  bob = %{name: "Bob", age: 27}

  case bob do
    person = %{age: age} when is_number(age) and age >= 21 ->
      IO.puts "It's fine, #{person.name}"
    _ ->
      IO.puts "Something goes wrong"
  end
end
```
#### Raising Exceptions
```
raise "Giving up" # ** (RuntimeError) Giving up
raise RuntimeError # ** (RuntimeError) runtime error
raise RuntimeError, message: "override message" # ** (RuntimeError) override message
```
#### Designing with Exceptions
```
case File.open("config_file") do
{:ok, file} ->
  process(file)
{:error, :message} ->
  raise -> "Failed to open file: #{message}"
```
### Multiple Processes
```
defmodule SpawnBasic do
  def greet do
    IO.puts "Hello"
  end
end

SpawnBasic.greet # Hello
spawn(SpawnBasic, :greet, []) # Hello #PID<0.42.0>
```
#### Sending Messages Between Processes
```
defmodule Spawn1 do
  def greet do
    receive do
      {sender, msg} ->
        send sender, {:ok, "Hello, #{msg}"}
    end
  end
end

pid = spawn(Spawn1, :greet, [])
send pid, {self, "World!"}

receive do
  {:ok, message} ->
    IO.puts message
end
```
#### Handling Multiple Messages
```
defmodule Spawn2 do
  def greet do
    receive do
      {sender, msg} ->
        send sender, {:ok, "Hello, #{msg}"}
        greet
    end
  end
end

pid = spawn(Spawn2, :greet, [])

send pid, {self, "World!"}

receive do
  {:ok, message} ->
    IO.puts message
end

send pid, {self, "Kermit!"}
receive do
  {:ok, message} ->
    IO.puts message
  after 500 ->
    IO.puts "The greeter has gone away"
end
```
#### Recursive Fib
```
defmodule TailRecursive do
  def factorial(n),   do: _fact(n, 1)
  defp _fact(0, acc), do: acc
  defp _fact(n, acc), do: _fact(n - 1, acc * n)
end
```
#### Process Overhead
Run with
```
elixir --erl "+P 1000000"  -r chain.exs -e "Chain.run(1_000_000)"
```
```
defmodule Chain do
  def counter(next_pid) do
    receive do
      n ->
        send next_pid, n + 1
    end
  end

  def create_processes(n) do
    last = Enum.reduce 1..n, self,
      fn(_, send_to) ->
        spanw(Chain, :counter, [send_to])
      end

    # start the count by sending
    send last, 0

    # and wait for the result to come back
    receive do
      final_answer when is_integer(final_answer) ->
        "Result is #{inspect(final_answer)}"
    end
  end

  def run(n) do
    IO.puts inspect :timer.tc(Chain, :create_processes, [n])
  end
end
```
#### When Process Die
```
defmodule Link1 do
  import :timer, only: [sleep: 1]

  def sad_function do
    sleep 500
    exit(:boom)
  end

  def run do
    Process.flag(:trap_exit, true)
    spawn(Link1, :sad_function, [])
    receive do
      msg ->
        IO.puts "Message received: #{inspect msg}"
    after 1000 ->
        IO.puts "Nothing happened as far as i'm concerned"
    end
  end
end

Link1.run # Nothing happened as far as i'm concerned
```
#### Linking 2 processes
```
defmodule Link2 do
  import :timer, only: [sleep: 1]

  def sad_function do
    sleep 500
    exit(:boom)
  end

  def run do
    spawn_link(Link2, :sad_function, [])
    receive do
      msg ->
        IO.puts "Message received: #{inspect msg}"
    after 1000 ->
        IO.puts "Nothing happened as far as i'm concerned"
    end
  end
end

Link2.run # Message received: {:EXIT, #PID<0.121.0>, :boom}
```
Trapping exit signal from child process
```
defmodule Link3 do
  import :timer, only: [sleep: 1]

  def sad_function do
    sleep 500
    exit(:boom)
  end

  def run do
    Process.flag(:trap_exit, true)
    spawn_link(Link2, :sad_function, [])
    receive do
      msg ->
        IO.puts "Message received: #{inspect msg}"
    after 1000 ->
        IO.puts "Nothing happened as far as i'm concerned"
    end
  end
end

Link3.run # Message received: {:EXIT, #PID<0.121.0>, :boom}
```
#### Monitoring Process
```
defmodule Monitor1 do
  import :timer, only: [sleep: 1]

  def sad_method do
    sleep 500
    exit(:boom)
  end

  def run do
    res = spawn_monitor(Monitor1, :sad_method, [])
    IO.puts inspect res
    receive do
      msg ->
        IO.puts "Message received: #{inspect msg}"
    after 1000 ->
        IO.puts "Nothing happened as far as i'm concerned"
    end
  end
end

Monitor1.run
{#PID<0.37.0>,#Reference<0.0.0.53>}
MESSAGE RECEIVED: {:DOWN,#Reference<0.0.0.53>,:process,#PID<0.37.0>,:boom}
```
#### Parallel Map—The “Hello, World” of Erlang
```
defmodule Parallel do
  def pmap(collection, fun) do
    me = self
    collection
    |> Enum.map(fn (elem) ->
         spawn_link fn -> (send me, {self, fun.(elem)}) end
       end)
    |> Enum.map(fn(pid) ->
         receive do { ^pid, result } -> result end
       end)
  end
end


```
#### A Fibonacci Server
```
defmodule FibSolver do
  def fib(scheduler) do
    send scheduler, {:ready, self}

    receive do
      {:fib, n, client} ->
        send client, {:answer, n, fib_calc(n), self}
        fib(scheduler)
      {:shutdown} ->
        exit(:normal)
    end
  end

  defp fib_calc(0), do: 0
  defp fib_calc(1), do: 1
  defp fib_calc(n), do: fib_calc(n-1) + fib_calc(n-2)
end

defmodule Scheduler do
  def run(num_processes, module, func, to_calculate) do
    (1..num_processes)
    |> Enum.map(fn(_) -> spawn(module, func, [self]) end)
    |> schedule_processes(to_calculate, [])
  end

  defp schedule_processes(processes, queue, results) do
    receive do
      {:ready, pid} when length(queue) > 0 ->
        [next | tail] = queue
        send pid, {:fib, next, self}
        schedule_processes(processes, tail, results)
      {:ready, pid} ->
        send pid, {:shutdown}
        if length(processes) > 1 do
          schedule_processes(List.delete(processes, pid), queue, results)
        else
          Enum.sort(results, fn {n1, _}, {n2, _} -> n1 <= n2 end)
        end
      {:answer, number, result, _pid} ->
        schedule_processes(processes, queue, [{number, result} | results])
    end
  end
end

to_process = [37, 37, 37, 37, 37, 37]

Enum.each 1..10, fn num_processes ->
  {time, result} = :timer.tc(Scheduler, :run, [num_processes, FibSolver, :fib, to_process])
  if num_processes == 1 do
    IO.puts inspect result
    IO.puts "\n #  time (s)"
  end
  :io.format "~2B     ~.2f~n", [num_processes, time/1_000_000.0]
end
```
#### Agents
```
defmodule FibAgent do
  def start_link do
    cache = Enum.into([{0, 0}, {1, 1}], HashDict.new)
    Agent.start_link(fn -> cache end)
  end

  def fib(pid, n) when n >= 0 do
    Agent.get_and_update(pid, &do_fib(&1, n))
  end

  defp do_fib(cache, n) do
    if cached = cache[n] do
      {cached, cache}
    else
      {val, cache} = do_fib(cache, n - 1)
      result = val + cache[n-2]
      {result, Dict.put(cache, n, result)}
    end
  end
end
```
### Nodes

#### Naming Nodes
```
Node.self # :nonode@nohost
```

```
iex --name wibble@finn.local
iex(wibble@finn.local)> Node.self
:"wibble@finn.local"

iex --sname wobble
iex(wobble@finn)> Node.self
:"wobble@finn"

iex --sname node_one
iex --sname node_two

Node.list # []
Node.connect :"node_one@finn" # true
Node.list # [:"node_one@finn"]
func = fn -> IO.inspect Node.self end
Node.spawn(:"node_one@finn", func)
Node.spawn(:"node_two@finn", func)
```
#### Node cookies
default cookie stored at home directory in `.erlang.cookie` file
```
iex --sname node_one --cookie cookie-one
iex --sname node_two --cookie cookie-one
```
#### Naming Processes
```
defmodule Ticker do
  @interval 2000 # 2 seconds
  @name :ticker

  def start do
    pid = spawn(__MODULE__, :generator, [[]])
    :global.register_name(@name, pid)
  end

  def register(client_pid) do
    send :global.whereis_name(@name), {:register, client_pid}
  end

  def generator(clients) do
    receive do
      {:register, pid} ->
        IO.puts "registering #{inspect pid}"
        generator([pid | clients])
    after
      @interval ->
        IO.puts "tick"
        Enum.each clients, fn client ->
          send client, {:tick}
        end
        generator(clients)
    end
  end
end

defmodule Client do
  def start do
    pid = spawn(__MODULE__, :receiver, [])
    Ticker.register(pid)
  end

  def receiver do
    receive do
      {:tick} ->
        IO.puts "tock in client"
        receiver
    end
  end
end
```
#### I/O, PIDs, and Nodes
```
Node.connect(:"one@finn") # window2
:global.register_name(:two, :erlang.group_leader) # window2
two = :global.whereis_name :two # window1
IO.puts(two, "Hello") # window1
```
### Tasks and Agents
#### Tasks
```
defmodule Fib do
  def of(0), do: 0
  def of(1), do: 1
  def of(n), do: Fib.of(n-1) + Fib.of(n-2)
end

IO.puts "Start the task"
worker = Task.async(fn -> Fib.of(20) end)
IO.puts "Do something else"
IO.puts "Wait for the task"
result = Task.await(worker)
IO.puts "The result is #{result}"

worker = Task.async(Fib, :of, [20])
result = Task.await(worker)
IO.puts "The result is #{result}"
```
#### Agents
```
{:ok, count} = Agent.start(fn -> 0 end)
Agent.get(count, &(&1)) # 0
Agent.update(count, &(&1+1)) # :ok
Agent.update(count, &(&1+1)) # :ok
Agent.get(count, &(&1)) # 2

Agent.start(fn -> 1 end, name: Sum)
Agent.get(Sum, &(&1)) # 1
Agent.update(Sum, &(&1+99)) # :ok
Agent.get(Sum, &(&1)) # 100
```

```
defmodule Frequency do
  def start_link do
    Agent.start_link(fn -> HashDict.new end, name: __MODULE__)
  end

  def add_word(word) do
    Agent.update(__MODULE__,
                 fn dict ->
                   Dict.update(dict, word, 1, &(&1+1))
                 end)
  end

  def count_for(word) do
    Agent.get(__MODULE__, fn dict -> Dict.get(dict, word) end)
  end

  def words do
    Agent.get(__MODULE__, fn dict -> Dict.keys(dict) end)
  end
end

Frequency.start_link
Frequency.add_word "bob"
Frequency.words # ["bob"]
Frequency.add_word "was"
Frequency.add_word "here"
Frequency.add_word "he"
Frequency.add_word "was"
Frequency.words # ["he", "bob", "was", "here"]
Frequency.count_for("bob") # 1
Frequency.count_for("was") # 2
```
### Macros and Code Evaluation
#### Macro
```
defmodule My do
  defmacro macro(param) do
    IO.inspect param
  end
end

defmodule Test do
  require My
  My.macro :atom       #=> :atom
  My.macro 1           #=> 1
  My.macro 1.0         #=> 1.0
  My.macro [1,2,3]     #=> [1,2,3]
  My.macro "binaries"  #=> "binaries"
  My.macro { 1, 2 }    #=> {1,2}
  My.macro do: 1       #=> [do: 1]
  My.macro do
    1
  end                  #=> [do: 1]
end
```
#### The Quote Function
```
quote do: :atom # :atom
quote do: 1 # 1
quote do: [do: 1] # [do: 1]
quote do: {1,2,3,4,5} # {:"{}",[],[1,2,3,4,5]}
```
#### Using the Representation As Code
```
defmodule My do
  defmacro macro(code) do
    IO.inspect code
    code
  end
end

defmodule Test do
  require My
  My.macro(IO.puts("hello"))
end
```
result:
```
{{:.,[line: 11],[{:__aliases__,[line: 11],[:IO]},:puts]}, [line: 11],["hello"]}
hello
```

```
defmodule My do
  defmacro macro(code) do
    quote do
      IO.inspect(unquote(code))
    end
  end
end
```

```
Code.eval_quoted(quote do: [1,2,unquote([3,4])]) # {[1,2,[3,4]],[]}
Code.eval_quoted(quote do: [1,2,unquote_splicing([3,4])]) # {[1,2,3,4],[]}
```

```
defmodule My do
  defmacro if(condition, clauses) do
    do_clause = Keyword.get(clauses, :do, nil)
    else_clause = Keyword.get(clauses, :else, nil)
    quote do
      case unquote(condition) do
        val when val in [false, nil] -> unquote(else_clause)
        _ -> unquote(do_clause)
      end
    end
  end
end

defmodule Test do
  require My
  My.if 1==2 do
    IO.puts "1 == 2"
  else
    IO.puts "1 != 2"
  end
end
```
#### Using Bindings to Inject Values
```
defmodule My do
  defmacro mydef(name) do
    quote bind_quoted: [name: name] do
      def unquote(name)(), do: unquote(name)
    end
  end
end

defmodule Test do
  require My
  [:fred, :bert] |> Enum.each(&My.mydef(&1))
end


IO.puts Test.fred #=> fred
```
#### Other Ways to Run Code Fragments
```
fragment = quote do: IO.puts("hello")
Code.eval_quoted fragment

fragment = quote do: IO.puts(var!(a))
Code.eval_quoted fragment, [a: "cat"]

fragment = Code.string_to_quoted("defmodule A do def b(c) do c+1 end end")
Macro.to_string(fragment)
Code.eval_string("[a, a*b, c]", [a: 2, b: 3, c: 4])

quote do: 1 + 2 # {:+, [context: Elixir, import: Kernel], [1, 2]}
Code.eval_quoted {:+, [], [1,2]} # {3,[]}
```
#### Macros and Operators
```
defmodule Operators do
  defmacro a + b do
    quote do
      to_string(unquote(a)) <> to_string(unquote(b))
    end
  end
end

defmodule Test do
  IO.puts(123 + 456)             #=> "579"
  import Kernel, except: [+: 2]
  import Operators
  IO.puts(123 + 456)             #=> "123456"
end

IO.puts(123 + 456)               #=> "579"


require Macro
Macro.binary_ops
Macro.unary_ops
```
### Behaviours and Use
#### Defining Behaviours
```

defmodule URI.Parser do
  @moduledoc """
  Defines the behavior for each URI.Parser.
  Check URI.HTTP for a possible implementation.
  """
  use Behaviour

  @doc """
  Responsible for parsing extra URL information.
  """
  defcallback parse(uri_info :: URI.Info.t) :: URI.Info.t

  @doc """
  Responsible for returning the default port.
  """
  defcallback default_port() :: integer
end
```
#### Declaring Behaviours
```
defmodule URI.HTTP do
  @behaviour URI.Parser
  def default_port(), do: 80
  def parse(info), do: info
end
```
#### Tracing Method Calls
```
defmodule Tracer do
  def dump_args(args) do
    args |> Enum.map(&inspect/1) |> Enum.join(", ")
  end

  def dump_defn(name, args) do
    "#{name}(#{dump_args(args)})"
  end

  defmacro def(definition={name, _, args}, do: content) do
    quote do
      Kernel.def(unquote(definition)) do
        IO.puts "==> call:   #{Tracer.dump_defn(unquote(name), unquote(args))}"
        result = unquote(content)
        IO.puts "<== result: #{result}"
        result
      end
    end
  end

  defmacro __using__(opts) do
    quote do
      import Kernel, except: [def: 2]
      import unquote(__MODULE__), only: [def: 2]
    end
  end
end

defmodule Test do
  use Tracer

  def puts_sum_three(a, b, c), do: IO.inspect(a+b+c)
  def add_list(list),          do: Enum.reduce(list, 0, &(&1+&2))
end
```
### Protocols
#### Defining a Protocol
```
defprotocol Inspect do
  def inspect(thing, opts)
end
```
Implementation goes separately.
#### Implementing a Protocol
```
defimpl Inspect, for: PID do
  def inspect(pid, _opts) do
    "#PID" <> iolist_to_binary(:erlang.pid_to_list(pid))
  end
end

defimpl Inspect, for: Reference do
  def inspect(ref, _opts) do
    '#Ref' ++ rest = :erlang.ref_to_list(ref)
    "#Reference" <> iolist_to_binary(rest)
  end
end
```
Defaults can be redefined:
```
defimpl Inspect, for: PID do
  def inspect(pid, _) do
    "#Process: " <> iolist_to_binary(:erlang.pid_to_list(pid)) <> "!!"
  end
end
```

```
defprotocol Collection do
  @fallback_to_any true
  def is_collection?(value)
end

defimpl Collection, for: [List, Tuple, BitString] do
  def is_collection?(_), do: true
end

defimpl Collection, for: Any do
  def is_collection?(_), do: false
end

Enum.each [ 1, 1.0, [1,2], {1,2}, HashDict.new, "cat" ], fn value ->
  IO.puts "#{inspect value}: #{Collection.is_collection?(value)}"
end
```
#### Protocols and Structs
```
defmodule Blob do
  defstruct content: nil
end

b = %Blob(content: 123)
inspect b # "%Blob{content: 123}"
inspect b, structs: false # "%{__struct__: Blob, content: 123}"
```
#### Built-in Protocols: Enumerable
```
defprotocol Enumerable do
  def count(collection)
  def member?(collection, value)
  def reduce(collection, acc, fun)
end
```
```
defmodule Bitmap do
  defstruct value: 0
  defimpl Enumerable do
    import :math, only: [log: 1]
    def count(%Bitmap{value: value}) do
      { :ok, trunc(log(abs(value))/log(2)) + 1 }
    end
  end
end

fifty = %Bitmap{value: 50}
IO.puts Enum.count fifty

def member?(value, bit_number) do
  {:ok, 0 <= bit_number && bit_number < Enum.count(value)}
end

IO.puts Enum.member? fifty, 4 # => true
IO.puts Enum.member? fifty, 6 # => false

def reduce(bitmap, {:cont, acc}, fun) do
  bit_count = Enum.count(bitmap)
  _reduce({bitmap, bit_count}, { :cont, acc }, fun)
end

defp _reduce({_bitmap, -1}, { :cont, acc }, _fun), do: { :done, acc }
defp _reduce({bitmap, bit_number}, { :cont, acc }, fun) do
  _reduce({bitmap, bit_number-1}, fun.(bitmap[bit_number], acc), fun)
end
defp _reduce({_bitmap, _bit_number}, { :halt, acc }, _fun), do: { :halted, acc }
defp _reduce({bitmap, bit_number}, { :suspend, acc }, fun),
do: { :suspended, acc, &_reduce({bitmap, bit_number}, &1, fun), fun }

IO.inspect Enum.reverse fifty # => [0, 1, 0, 0, 1, 1, 0]
IO.inspect Enum.join fifty, ":" # => "0:1:1:0:0:1:0"
```
#### Built-in Protocols: String.Chars
```
defmodule Bitmap do
  defstruct value: 0

  defimpl String.Chars do
    def to_string(value), do: Enum.join(value, "")
  end
end

fifty = %Bitmap{value: 50}
IO.puts "Fifty in bits is #{fifty}" # => Fifty in bits is 0110010
```
#### Built-in Protocols: Inspect
```
defmodule Bitmap do
  defstruct value: 0

  defimpl Inspect do
    def inspect(%Bitmap{value: value}, _opts) do
      "%Bitmap{#{value}=#{as_binary(value)}}"
    end

    defp as_binary(value) do
      to_string(:io_lib.format("~.2B", [value]))
    end
  end
end

fifty = %Bitmap{value: 50}
IO.inspect fifty                 # => %Bitmap{50=0110010}
IO.inspect fifty, structs: false # => %{__struct__: Bitmap, value: 50}
#### Writing Your Own Sigils
```
defmodule LineSigil do @doc """
  Implement the `~l` sigil, which takes a string containing
  multiple lines and returns a list of those lines.

  ## Example usage
      iex> import LineSigil
      nil
      iex> ~l"""
      ...> one
      ...> two
      ...> three
      ...> """ ["one","two","three"]
  """
  def sigil_l(lines, _opts) do
    lines |> String.rstrip |> String.split("\n")
  end
end

defmodule Example do
  import LineSigil
  def lines do
    ~l"""
    line 1
    line 2
    and another line in #{__MODULE__}
    """
  end
end
```

```
defmodule ColorSigil do
  @color_map [
    rgb: [red: 0xff0000, green: 0x00ff00, blue: 0x0000ff],
    hsb: [red: {0,100,100}, green: {120,100,100}, blue: {240,100,100}]
  ]

  def sigil_c(color_name, []), do: _c(color_name, :rgb)
  def sigil_c(color_name, 'r'), do: _c(color_name, :rgb)
  def sigil_c(color_name, 'h'), do: _c(color_name, :hsb)

  defp _c(color_name, color_space) do
    @color_map[color_space][binary_to_atom(color_name)]
  end

  defmacro __using__(_opts) do
    quote do
      import Kernel, except: [sigil_c: 2]
      import unquote(__MODULE__), only: [sigil_c: 2]
    end
  end
end

defmodule Example do
  use ColorSigil

  def rgb, do: IO.inspect ~c{red}
  def hsb, do: IO.inspect ~c{red}h
end

Example.rgb #=> 16711680 (== 0xff0000)
Example.hsb #=> {0,100,100}
```
### Exceptions
#### Raising an Exception
```
raise "Giving up" # ** (RuntimeError) Giving up
raise RuntimeError # ** (RuntimeError) runtime error
raise RuntimeError, message: "override message" # ** (RuntimeError) override message
```
```
defmodule Boom do
  def start(n) do
    try do
      raise_error(n)
    rescue
      [ FunctionClauseError, RuntimeError ] ->
        IO.puts "no function match or runtime error"
      error in [ArithmeticError]  ->
        IO.puts "Uh-oh! Arithmetic error: #{error.message}"
        raise error, [ message: "too late, we're doomed"], System.stacktrace
      other_errors ->
        IO.puts "Disaster! #{other_errors.message}"
    after
      IO.puts "DONE!"
    end
  end

  defp raise_error(0) do
    IO.puts "No error"
  end

  defp raise_error(1) do
    IO.puts "About to divide by zero"
    1 / 0
  end

  defp raise_error(2) do
    IO.puts "About to call a function that doesn't exist"
    raise_error(99)
  end

  defp raise_error(3) do
    IO.puts "About to try creating a directory with no permission"
    File.mkdir!("/not_allowed")
  end
end
```
#### catch, exit, and throw
```
defmodule Catch do
  def start(n) do
    try do
      incite(n)
    catch
      :exit, code   -> "Exited with code #{inspect code}"
      :throw, value -> "throw called with #{inspect value}"
      what, value   -> "Caught #{inspect what} with #{inspect value}"
    end
  end

  defp incite(1) do
    exit(:something_bad_happened)
  end

  defp incite(2) do
    throw {:animal, "wombat"}
  end

  defp incite(3) do
    :erlang.error "Oh no!"
  end
end
```
#### Defining Exceptions
```
defmodule KinectProtocolError do
  defexception message: "Kinect protocol error",
               can_retry: false

  def full_message(me) do
    "Kinect failed: #{me.message}, retriable: #{me.can_retry}"
  end
end

defmodule B do
  def talk_to_kinect do
    raise KinectProtocolError, message: "usb unplugged", can_retry: true
  end

  def schedule_retry do
    IO.puts "Retrying in 10 seconds"
  end

  def start do
    try do
      talk_to_kinect
    rescue
      error in [KinectProtocolError] ->
        IO.puts KinectProtocolError.full_message(error)
        if error.can_retry, do: schedule_retry
    end
  end
end
```
