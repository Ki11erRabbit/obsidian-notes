Build a simple prototype for an abstract debugger for either Go or Javascript


Try to implement a 0 CFA for a small language
- google to learn more


#### 0 CFA
```
h(x, y, z):
	if x < 5
	| y + 2
	| z - 3

g(a, b):
	w := h(a, 2 + a, b+2)
	if w > 1
	| ...
	| ...

g(arg1, arg2)
```

dataflow analysis - make a control flow graph

- can look at it in one step (because it is simple) (not higher order)

CFA: control flow graph is constructed on the fly


A 0 CFA is a kind of interpreter
Don't write one for a huge language
write it for lambda calculus
then slowly add features