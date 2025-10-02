moving to 2208 after Christmas break


### Abstract Debugger
Have large states that contain smaller states that could stepped into

## 0-CFA
2 maps
C Exp -> Abstract Value
epsilon Var -> Abstract Values


compute 3 things
Reachable set: Powerset(Expr)
Cache: Exp -> Val
Environment: Var -> Val

Takes in old versions and create new versions
know when done when old == new

\x. \y x 3 4

true all binding instances are unique
only number expressions

R0 = { e0 }
c = Bottom (empty map)
E = Bottom (empty map)

visit every expr that is reachable
if lambda add to cache that lambda evals to itself
if var ref then add to cache look it up in environment and put it in the cache
if application e0 needs to be in reachable if e0 is reachable then e1 is reachable