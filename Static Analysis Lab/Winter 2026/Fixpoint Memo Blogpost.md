For blog audience
cs programers who at least know what monads
couldn't tell you what a fixed point is off the top of their head or how monads can do with it

bring up fixed points, datalog, first and follow sets of a context free grammar, ask llm for more examples

bring up parsing and abstract interpretation and go into more detail later
fixpoint is a set of facts (for datalog)
talk about efficiency of our approach

brainstorm ideas that are needed to understand this
talk about mechanics, named computations, and dependencies emerge from computation

keep it accessible and introduce monads and continuations at the end

# A Small yet powerful tool for your toolbelt

We have a small library that we use in our lab that enables us to do our work efficiently. This library can be used for all sorts of things from parsing, and abstract interpretation to implementing Datalog or performing type inference. The library provides a framework for working with fixpoint computation with memoization.

## What is a fixpoint?
A fixpoint (or fixed point) of a function is a value that maps to itself. For example for the function `f(x) = x^2`, the fixpoint of this function is `0` and `1` since `x = x^x` for those situations. You can think of a fixpoint as a stable solution for a problem where input doesn't change output.

### What is a fixpoint solver
A fixpoint solver's job is to find this stable solution for a given equation because many fixpoints of functions are not trivial to produce. The solver's job is to apply a function on a given input until the sequence of output converges. In other words, the limit of this iteration is the fixpoint of the function for the given input. 

### Where fixpoint solvers pop up in computer science
They are primarily used in static analysis techniques in data flow analysis where we want to know what values a variable may hold, it powers type inference since the compiler needs to figure out what type of a value is so that the code can be compiled correctly, and it is used in abstract interpretation where we want to prove certain program properties. In our lab we have come up with a novel use of a fixpoint solver for parsing. We well show this off in a later section.

## A high level understanding of our library
Before we go into examples or how the library itself. It is best to gain an intuition for how our library works.
Earlier it was mentioned that a fixpoint solver is iterative. So we know that we have some kind of looping structure. For input we will have some state. This state is a mapping from an identifier to a set of possible values. In general, we know when a solver is done because either we don't learn any new mappings or we don't learn any new possible values. Then it is a matter of seeing if the desired value is in the right mapping.

### The Naive Approach
The naive approach is to loop over our mappings and see if we can make progress. New facts emerge from evaluation and then we keep on iterating until no more progress can be made.

#### Downsides to the naive approach
The issue with the naive approach is that we end up doing the same amount of work multiple times with each pass of the fixpoint solver. This gives us a rough time complexity of O(n^2) but is really O(n \* m) with 'n' being the rate of convergence (amount of iterations) and 'm' being the cost of the fixpoint function. This isn't great. Luckily, our approach exploits some useful observations about how the solver behaves.
While we can speed up the doing extra work by memoizing the work already done, this still doesn't handle the fact that we are still doing some work that we have already solved.

### Our Approach
There is a useful observation about how data flows through a fixpoint solver. You end up getting a dependency graph. We exploit this by storing callbacks in a state's value in addition to a list of possible values. The callbacks are invoked whenever a new possible value is learned of. This means that whenever we make progress, we only notify whoever needs to know about the new values. This improves performance tremendously. When used in conjunction with memoization, we ensure only meaningful work is accomplished.


## How our library works
Our library uses a monad to handle modularity. We will not discuss what a monad is in this blogpost. This just means that as long as you have higher order functions in your language, you can implement our algorithm.
Our memoizing fixpoint monad is composed with a state monad.