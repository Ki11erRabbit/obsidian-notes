CakeML
Look up Cones

## Compare OOP to Functional Programming

|         | run | analyze | compile | pretty print | lower ast |
| ------- | --- | ------- | ------- | ------------ | --------- |
| literal |     |         |         |              |           |
| add     |     |         |         |              |           |
| funcall |     |         |         |              |           |
| ref     |     |         |         |              |           |
| lambda  |     |         |         |              |           |
### OOP
```java
public interface Expression {
	run()
}

public class Literal implements Expression {
	@Override
	run()
}
```
make a class that implements the interface for each 

Easier to add a new row of features. We don't need to interact with any older code

Harder to add a new column. Have to touch every class

objects are defined by its behavior
object are defined by its destruction by how we interact to it. 
Codata is defined by how it is destructed
### Functional
```haskell
data Expr
	| literal (value)
	| addE(lhs, rhs)
	| ...
	

run :: Expr -> Value
analyze :: Expr -> Result
```

Harder to add a new row of features. We need to interact with all old code to expand on it.

Easier to add a new column. Simply implement a new function.

data is inspected

Data is defined by the way it was constructed

Functions are Codata.


Are functions special cases of objects, or are objects special cases of object?

## Software Design Patterns
Macro expressible: can't influence the rest of the program, doesn't add to the expressive power of the language.

Software design patterns are examples of a language that lacks expressive power.

Scheme R5RS opening