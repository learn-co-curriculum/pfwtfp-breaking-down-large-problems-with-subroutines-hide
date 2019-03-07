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
* Join argument 0 with argument 2 (bread1 and peanut_butter)
* Join argument 1 with argument 3 (bread2 and jelly)
* Join the two previous steps' outputs together
* Violà: All done
```

"Shape" is a pretty fuzzy term, but it's the mental "image" of "what the code
does" that you can easily keep in your head. Some programmers have called this
the "theory of the program." Not the syntax, not the words, but
the..._character_ of the code. Detecting when the "shape" makes a radical
departure at an _intuitive_ level can be a valuable signal that (maybe) your
implementation is taking a bad turn.

## When Shape Goes Wrong

But now let's suppose the inputs change slightly.

```ruby
def make_pbj_sandwich(loaf_of_bread, peanut_butter, jelly)
```

For each PBJ, we now get a _loaf_ of bread _instead of_ two slices. We must make our PBJ on two equally
cut slices of bread from the loaf we are given. Let's pseudocode the "shape" of this
method.

```text
* Get items
* Process argument 0, a loaf of bread
  * Determine thickness of each slice
  * Ensure loaf is thick enough to support 2 slice of the desired size; if not, error
  * Make cuts
  * Take two slices and store them as bread1 and bread2
* Join argument 1 with bread1
* Join argument 2 with bread2
* Join the two previous steps' outputs together
* Violà: All done
```

Wow! Look at that, to add a little intelligence around working with a loaf
instead of two slices, our pseudocode lost a lot of its readability! 

Handling the slicing has _just as many steps_ as the previous "shape!" And what if
argument 0 were pre-sliced bread? We'd have to add more conditional logic..._in
a method that was about assembling slices_. We've lost perspective of what
`make_pbj_sandwich()` was about.

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

* a loaf type
* a loaf width
* the desired width of each slice

5. Define the procedure’s implementation

* Given a loaf and a width
* Cut the loaf as many times as posssible
* Collect the slices
* if there are at least two, return them
* otherwise `raise` an error and crash the program

6. Translate the procedure description into code

def get_two_slices_from_loaf(loaf_type, loaf_width, slice_width)
  slices = []
  slices_count = loaf_width / slice_width
  slices_count.times do
    slices << "slice of #{loaf_type}"
  end

  if slices.length >= 2
    return slices[0,2]
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
LOAF_TYPE = "light rye"
LOAF_WIDTH = 60

def get_two_slices_from_loaf(loaf_type, loaf_width, slice_width)
  slices = []
  slices_count = loaf_width / slice_width
  slices_count.times do
    slices << "slice of #{loaf_type}"
  end

  if slices.length >= 2
    return slices[0,2]
  else
    raise ArgumentError, "Could not make enough bread from the loaf!"
    # `raise` makes a program crash with an error
  end
end

def make_pbj_sandwich(loaf_type, loaf_width, peanut_butter, jelly, slice_width)
  bread1, bread2 = get_two_slices_from_loaf(loaf_type, loaf_width, slice_width)

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

make_pbj_sandwich(LOAF_TYPE, LOAF_WIDTH, "crunchy monkey brand peanut butter", "Belgian
forest-berry", 2)
```

The "shape" of the code has grown by only one line:

```text
* Get items
* Process input to fit into the two halves' groups
* Join half of the items with each other
* Join the other halves of the items together
* Join the two previous steps' outputs together
* Violà
```

The method `make_pbj_sandwich` is still focused on assembling slices
and ingredients. Its "spirit" is unchanged.

> **PRO-TIP**: Experienced developers might suggest that perhaps this
> method's name would be improved by renaming it to `assemble_finished_ingredients`.

## OPTIONAL: A Call for Classes

This `loaf` instance has _attributes_ (`length` and `type`). You might even see
some _behaviors_ that you might like the loaf to know how to do to itself. That
should suggest to you that perhaps a `loaf` is an instance of a `class` of some sort.
Improve this code to make a `loaf` an instance of a class.

We should be able to say:

```ruby
bad_for_teeth_loaf = Loaf.new("white sugar insulin shock bread", 50, 4)
make_pbj_sandwich(bad_for_teeth_loaf, "Mr. Sugar's peanut butter", "tooth-rot surprise jelly")
```

How might this `Loaf` instance be able to know if it were pre-sliced? How would
that change your implementation?

Developers are constantly adjusting demo code with silly examples to try to integrate
concepts. It's not enough to read and it's not enough to code, you have to _explore_
the code you write.

## Conclusion

Learning to move complexity out of one procedure into subroutines is a vital
tool for keeping your code readable and for ensuring that the core character,
purpose, or "shape" of your given procedure can be quickly grasped and
understood. Hiding complexity behind "abstractions" like subroutines is the
mark of a professional developer.
