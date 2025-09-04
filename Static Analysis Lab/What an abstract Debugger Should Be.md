This contains some ideas on what an abstract debugger should be.
For example, what dials and nobs should the user be able to use?

## Nobs and Dials that should be available
#### Basic debugger features
* Breakpoints/Conditional Breakpoints
* stepping through and stepping over
* stepping backwards and stepping through
* be able to view the state of variables
- ability to change the state of variables during execution
- ability to skip blocks of code.

#### Abstract Debugger Features
* change and explore branches
* alter state in branches
* With time travel, ability to explore multiple different timelines
* visualize state space
* be able to set boundaries on uncountably infinite types (really anything like strings and collections)
  * From what I remember from abstract interpreters is that Strings and Lists completely blow up the state space if there are minimal restrictions on them.
  * Therefore, we should be able to manually limit them to tune the state space. Of course this makes it possible for there to be holes in proof of the program. However, it should allow for us to target particular parts of a program. This would allow for skipping over parsers if we know that they only accept well formed syntax.
* 