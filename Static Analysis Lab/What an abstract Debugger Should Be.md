This contains some ideas on what an abstract debugger should be.
For example, what dials and nobs should the user be able to use?

## Before Learning About Abstract Debuggers
### Nobs and Dials that should be available
#### Basic debugger features
* Breakpoints/Conditional Breakpoints
* stepping through and stepping over
* stepping backwards and stepping through
* be able to view the state of variables
- ability to change the state of variables during execution
- ability to skip blocks of code.

#### Abstract Debugger Features
* change and explore branches concurrently
* alter state in branches
* With time travel, ability to explore multiple different timelines concurrently
* visualize state space
* be able to set boundaries on uncountably infinite types (really anything like strings and collections)
  * From what I remember from abstract interpreters is that Strings and Lists completely blow up the state space if there are minimal restrictions on them.
  * Therefore, we should be able to manually limit them to tune the state space. Of course this makes it possible for there to be holes in proof of the program. However, it should allow for us to target particular parts of a program. This would allow for skipping over parsers if we know that they only accept well formed syntax.
* Setting restrictions of any type to limit search space. Same problems and reasoning can be found for the previous bullet
* Way to export the current state as a svg or graphviz file

## After Learning About Abstract Debuggers

An abstract debugger should be as accessible as a normal debugger. A little training is all that a user should be able to use such a tool.

I think it would be cool if you could turn on and off abstract debugging in a debugging context. I don't know if it would be possible but it might ease new users into using the tool.