The primary loop of using an abstract debugger should follow this:
1. select term/function
2. choose things to learn about the the code
3. debugger finds out results that the user can explore
# Selection
You should be able to select a term for analysis
#### Function
This tells the debugger to look only analyze the function as its own unit

#### Term
This tells the debugger to analyze the paths that lead to this term. We should be able to see multiple paths to this term.

# Things to learn about
This is things about the program. This includes things like null pointer dereferences, early termination, exception throws, etc. This can differ from language to language.

# Results
The results should be listed out in relation with the things that the user wanted to learn about. They may be conditional if they are based on possible facts that are runtime dependent (out of memory, missing file, etc). If the user can cover possible runtime dependent behaviors then the conditional results may not appear.
Results may be false negatives (meaning behaviors may be possible).

If there are multiple paths to the term, then there should be a way to explore each path and the results should also be bound to a path.