# Minimal Interface of an Abstract Debugger
#### Stepping
Each of these operations should be able to take an integer to allow for quick traversal of a function.
- Step Into - Executes the next step of execution. Enters into function if the step is a function
- Step Over - Executes the next step of execution. Skips over function if the step is a function
- Step Back - Reverses the execution state. Enters into a function if the previous step was a function
- Jump Back - Reverses the execution state. Skips functions if the previous step was a function
- Walk To - Goes to a particular step in execution
- Select Branch - Allows the user to pick which branch of execution to view
- Step Out - Completes the current function's execution.
#### Display
Useful for viewing the current state of the execution
- Print - Outputs a variable's possible values as a list of constraints
- List - outputs all current constraints on a variable and where the constraint can be found
- Location - outputs the current step in execution
- Dump - dumps the current state into something like graphvis

#### Modification
These are to alter user restrictions on a given variable/value
* Restrict - puts further restrictions on a variable to decrease state space
* Loosen - reduces a restriction on a variable to increase state space

#### Breakpoints
Breakpoints are a staple of debuggers and not having them would be absurd.
- Breakpoint - normal debugging breakpoint. stop's execution when we reach it
- Conditional Breakpoint - breakpoint when a certain constraint is followed by a variable
- Assert Breakpoint - breakpoint when a certain constraint is not followed by a variable. This allows for being able to find out if callers of a function call it correctly. That is to say, if they followed all restrictions that the library requires (no null, only positive, etc)
- Watches - we stop execution when a certain constraint becomes true. These should be scoped to a function.



