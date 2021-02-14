---
title: 'Exploring Strategy Pattern'
date: '2021-02-14'
meta:
  name: 'Exploring Strategy Pattern'
  content: 'Design Patterns, Strategy Pattern, Ruby'
---

I have been trying to implement design patterns into my arsenal lately. I have seen it being used at multiple places, but I was never able to implement it to solve any problem on my own and I am trying to change that fact now. Today's Design pattern is `Strategy Pattern`

## Strategy Pattern

**Strategy** is a behavioral design pattern that lets you define a family of algorithms, put each of them into a separate class, and make their objects interchangeable.

## Uses

Where can I use this in my real production applications:

- Authentication
- Payment
- Anything that can be done in multiple ways

Sky is the limit here. Do let me know if you have some special ideas.

## Example

Instead of trying to do something special, I would try and implement a solution which can sort using different algorithms.

According to the pattern, I would be needing a context and a strategy. A context is where I would be storing the strategy to solve the problem. Strategy is just a way to solve the problem.

Going by the defintion, Here is my solution

```ruby
class SortContext
  attr_accessor :strategy
  
  # Initialize the items array
  def initialize(strategy)
    @strategy = strategy
  end

  # @param [Array] items
  #
  # @return [Array]
  def sort(items)
    strategy.execute(items)
  end
end
```

This is the context, which will take in a strategy and call it's method. Cool thing is the context has no idea how the method has been implement. We can now replace it with any strategy we want. Let's create an abstract Strategy class.

```ruby
class Strategy
  # abstract
  #
  # @return [Array]
  def execute(items)
    raise NotImplementError, "#{self.class} has not implemented method #{__method__}"
  end
end
```

This is the method which has to be implemented in the different strategies that we will have. Let's start with the inbuilt sort method strategy.

```ruby
class InbuiltSortStrategy < Strategy
  # @param [Array] items
  #
  # @return [Array]
  def execute(items)
    items.sort
  end
end
```

I know this feels like cheating. If you look at it now, it feels like it was better off in the same class. Bear with me for a moment lets try and implement this.

```ruby
inbuiltSort = SortContext.new(InbuiltSortStrategy.new)
inbuiltSort.sort([4,2,5,1])

# [1,2,4,5]
```

As you can see, The code is working as expected. Now let's introduce a new sorting strategy which reverse sorts the array of items.

```ruby
class ReverseSortStrategy < Strategy
  # @param [Array] items
  #
  # @return [Array]
  def execute(items)
    items.sort { |x,y| -(x <=> y) }
  end
end
```

Notice how we didn't even have to touch our old code in order to introduce a new change ? This is how our classes should be implemented. Now inorder to sort the items in reverse, just create a new context with this strategy.

```ruby
reverseSort = SortContext.new(ReverseSortStrategy.new)
reverseSort.sort([4,2,5,1])

# [5, 4, 2, 1]
```

To make things interesting, let's implement a merge sort strategy:

```ruby
class MergeSortStrategy < Strategy
  # @param [Array] items
  #
  # @return [Array]
  def execute(items)
    mergeSort(items)
  end

  def mergeSort(items)
    # if array only has one element or fewer there is nothing to do
    if items.length <=1 
      items
    else 
      # dividing and then merge-sorting the halves
      mid = items.length/2
      first_half = merge_sort(items.slice(0...mid))
      second_half = merge_sort(items.slice(mid...items.length))
      merge(first_half, second_half)
    end 
  end

  def merge(left_array, right_array)
    sorted_array = []
    # If either array is empty we don't need to compare them
    while !left_array.empty? && !right_array.empty? do
      # we are shifting out the compared/used values so we don't repeat
      if left_array[0] < right_array[0]
        sorted_array.push(left_array.shift)
      else   
        sorted_array.push(right_array.shift)
      end
    end 
    #concat appends elements of another array to an array
    return sorted_array.concat(left_array).concat(right_array)
  end
end
```

Now creating a new context with this strategy:

```ruby
mergeSort = SortContext.new(MergeSortStrategy.new)
mergeSort.sort([4,2,5,1])

# [1, 2, 4, 5]
```

Voilà! New changes added without disturbing the existing code. Our context has no idea what magic goes on in the sort method, there is no coupling in the context and strategy here. This is the most interesting thing about this strategy.

Now this was just a small example, which might have made any difference as the changes where very small and you could easily distinguished the parts of the code. But imagine a huge system, where you've thousands of lines of code and multiple people are working on it. Every person responsible for a strategy could work without worrying about conflicts and breaking other people changes while integration.

I will be working on something concrete to demonstrate this in a better way.