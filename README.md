# Hiding Work in Subroutines

## Learning Goals

- Identify techniques for breaking down large problems: hide work in subroutines

## Introduction

Complexity usually creeps up on us. We start with something simple and clear,
but then some new requirement leads us to put more complexity in our procedure.
All of a sudden the proper "shape" of the method looks weird and it's harder to
see how the core of the procedure works. In this lesson we'll see how our
methods' shapes get distorted **and** how to set them back to normal by hiding
work on subroutines.

Let's solve the problem of "someone is hungry."

## Pseudocoding "Solve My Hunger"

Let's recall our Flatiron Programming Process:

1. Identify the problem
2. Identify the output that would solve the problem
3. Name the procedure that fixes the problem
4. Identify what inputs are needed to create the output
5. Define the procedure’s implementation
6. Translate the procedure description into code
7. Verify the procedure's output


We'll fill out steps 1-5 here.

```text
1. Identify the problem

I am hungry

2. Identify the output that would solve the problem

I want a peanut butter and jelly (pbj) sandwich.

3. Name the procedure that fixes the problem

make_pbj

4. Identify what inputs are needed to create the output

2 slices of bread, 20oz smooth PB, 20oz strawberry jam

5. Define the procedure’s implementation

* Get a slice of bread, put peanut butter on it
* Get the other slice of bread, put jelly on it
* Put the two slices of bread together with ingredients touching
* End result: sandwich
```

## Coding "Solve My Hunger"

Let's finish following the Flatiron process by performing Step 6: Translate the
procedure description into code.

We might translate this pseudocode into the following code in Ruby:

```ruby
def make_pbj_sandwich(bread1, bread2, peanut_butter, jelly)
  # Join ingredients
  puts "You join #{bread1} and #{peanut_butter}"
  side1 = "#{peanut_butter} on #{bread1}"

  # Join ingredients
  puts "You join #{bread2} and #{jelly}"
  side2 = "#{jelly} on #{bread2}"

  # Join ingredient on bread
  puts "You join #{side1} with #{side2}"
  "A #{peanut_butter} and #{jelly} sandwich"
end

puts make_pbj_sandwich("dark rye", "dark rye", "smooth premium Adirondack peanut butter", "organic strawberry jelly")

#=> You join dark rye and smooth premium Adirondack peanut butter
#=> You join dark rye and organic strawberry jelly
#=> You join smooth premium Adirondack peanut butter on dark rye with organic strawberry jelly on dark rye
#=> A smooth premium Adirondack peanut butter and organic strawberry jelly sandwich
```

Follow Step 7 on your own. Work through the flow of the program and verify that
it works as we described in our pseudocode.

## New Directives

We should feel good about our code. It's easy to read and does the right thing.
It's "shape" is almost like a To-Do list:

```text
* Get items
* Join half of the items with each other
* Join the other halves of the items together
* Join the two previous steps' outputs together
* Viol&agrave;
```

"Shape" is a pretty fuzzy term, but it's the mental "image" of "what the code
does" that you can easily keep in your head. Some programmers have called this
the "theory of the program." Not the syntax, not the words, but
the..._character_ of the code. Detecting when the "shape" makes a radical
departure at an _intuitive_ level can be a valuable signal of maybe your
implementation is taking a bad turn.

But now let's suppose the inputs change slightly. For each PBJ, we now get a
_loaf_ of bread _instead of_ two slices. We must make our PBJ on two equally
cut slices of bread from the loaf we are given.

Let's update our code:

```ruby
# GIVEN CODE
#
# Required to make the Ruby work.
require 'ostruct'
demo_loaf = OpenStruct.new(:type => "light rye", :length => 60)
# (end GIVEN CODE)

def make_pbj_sandwich(loaf, peanut_butter, jelly, slice_width)
  slices = []
  slices_count = loaf.length / slice_width
  slices_count.times do
    slices << "slice of #{loaf.type}"
  end

  if slices.length >= 2
    bread1 = slices.shift # one less slice in slices
    bread2 = slices.shift # one less slice in slices

    # Join ingredients
    puts "You join #{bread1} and #{peanut_butter}"
    side1 = "#{peanut_butter} on #{bread1}"

    # Join ingredients
    puts "You join #{bread2} and #{jelly}"
    side2 = "#{jelly} on #{bread2}"

    # Join ingredient on bread
    puts "You join #{side1} with #{side2}"
    "A #{peanut_butter} and #{jelly} sandwich"
  else
    puts "Sorry we don't have enough bread to make a PBJ!"
  end
end

make_pbj_sandwich(demo_loaf, "crunchy monkey brand peanut butter", "Belgian forest-berry", 2)

#=> You join slice of light rye and crunchy monkey brand peanut butter
#=> You join slice of light rye and Belgian forest-berry
#=> You join crunchy monkey brand peanut butter on slice of light rye with Belgian forest-berry on slice of light rye

```

Wow! Look at that, to add a little intelligence around working with a loaf
instead of two slices, our code lost a lot of its readability!  The new code is
confusing the core activity of the method which used to be there.

The "shape" is different. While it *should* still be like the To-Do list that
it once was, the shape has become:

```text
* Get items
* Cut a loaf of bread
* Verify the loaf's outputs
* If the loaf provides sufficient materials
  * Join half of the items with each other
  * Join the other halves of the items together
  * Join the two previous steps' outputs together
* If not, then
  * Handle an error state
* Viol&agrave;
```

You can easily imagine with another change or two our method would grow to be
3-5 times its current size!

We'd like to hide away this new and confusing work that distorts the original
"shape" of the code. The secret is to use a _subroutine_.

## Hiding the Work in a Subroutine

The great part is that we can apply the Flatiron Process to help us think
through the subroutine. In fact, we can apply the Flatiron Process
_recursively_ until our problem is simple enough to reason about easily. Any
time a procedure is too complicated to easily grasp, feel free to use this
process to create a new subroutine.

1. Identify the problem
2. Identify the output that would solve the problem
3. Name the procedure that fixes the problem
4. Identify what inputs are needed to create the output
5. Define the procedure’s implementation
6. Translate the procedure description into code
7. Verify the procedure's output

```text
1. Identify the problem

We have a loaf and we need two slices

2. Identify the output that would solve the problem

Two slices of bread

3. Name the procedure that fixes the problem

get_two_slices_from_loaf

4. Identify what inputs are needed to create the output

* a loaf
* the width

5. Define the procedure’s implementation

* Given a loaf and a width
* Cut the loaf as many times as posssible
* Collect the slices
* if there are at least two, return them
* otherwise `raise` an error and crash the program

6. Translate the procedure description into code

def get_two_slices_from_loaf(loaf, width)
  slices = []
  slices_count = loaf.length / slice_width
  slices_count.times do
    slices << "slice of #{loaf.type}"
  end

  if slices.length >= 2
    return slice[0,2]
  else
    raise ArgumentError, "Could not make enough bread from the loaf!"
    # `raise` makes a program crash with an error
  end
end

7. Verify the procedure's output

_Exercise left to the reader_
```

Let's use this subroutine to return `make_pbj_sandwich` back to its original
"shape."

```ruby
# Required to make the Ruby work.
require 'ostruct'
demo_loaf = OpenStruct.new(:type => "light rye", :length => 60)
# (end GIVEN CODE)

def get_two_slices_from_loaf(loaf, width)
  slices = []
  slices_count = loaf.length / width
  slices_count.times do
    slices << "slice of #{loaf.type}"
  end

  if slices.length >= 2
    return slices[0,2]
  else
    raise ArgumentError, "Could not make enough bread from the loaf!"
  end
end

def make_pbj_sandwich(loaf, peanut_butter, jelly, slice_width)
  bread1, bread2 = get_two_slices_from_loaf(loaf, slice_width)

  # Join ingredients
  puts "You join #{bread1} and #{peanut_butter}"
  side1 = "#{peanut_butter} on #{bread1}"

  # Join ingredients
  puts "You join #{bread2} and #{jelly}"
  side2 = "#{jelly} on #{bread2}"

  # Join ingredient on bread
  puts "You join #{side1} with #{side2}"
  "A #{peanut_butter} and #{jelly} sandwich"
end

make_pbj_sandwich(demo_loaf, "crunchy monkey brand peanut butter", "Belgian
forest-berry", 2)
```

The "shape" of the code has grown by only one line:

```text
* Get items
* Process input to fit into the two halves' groups
* Join half of the items with each other
* Join the other halves of the items together
* Join the two previous steps' outputs together
* Viol&agrave;
```

## Conclusion

Learning to move complexity out of one procedure into subroutines is a vital
tool for keeping your code readable and for ensuring that the core character,
purpose, or "shape" of your given procedure can be quickly grasped and
understood. Hiding complexity behind "abstractions" like subroutines is the
mark of a professional developer.
