
# Motivation
It would be nice to point a static analyzer at a piece of code to figure what the values could be when it hit that point in the program. It would also be nice to analyze the behavior of a function in isolation, possibly even with mutually recursive functions.

# General Interface

User selects either a function or an expression in a function.
The Analyzer asks the user for actionable things to find out.
Once the user selects the attributes to learn about, the analyzer will go and find out if the actionable items selected are either true or possibly true.

# Design Architecture
The entire program should be dumped into a database to allow for quick querying of facts about the program at hand, this also allows for the caching of results

When we select a part of code, it should proceed in a bottom up fashion. This way we should be able to find all paths by simply looking at the call sites. The only issue this may have is if a function is passed as an argument or is anonymous. Main or whatever the equivalent to main would be the point that would be the stopping point. There also may need to be cases for functions that set up Unix signals which would require its own cases.

When we have hit the end of control flow, we should have gathered facts about the program. We then use a fixpoint solver to come back with either an affirmative answer, or a possible answer (meaning a potential false negative).


# Practical Design
We very much could have a separation of concerns. The parser and information dumper could be their own program since that part doesn't care about what we are learning. This means that we can use whatever language for both parts. The other part being a fixpoint solver that can solve for what the user wants to learn.

You might be able to split the Fixpoint Solver into two parts. The Frontend and Fixpoint Solver. This is due to the fact that the solver shouldn't care about the language of the program

## Information Dumper
We could use treesitter to provide a parser for any language to make it more agnostic to languages. Then you could at least provide a baseline library and database policy to work as a starting point. The rest could possibly be through a plugin system to add more features.

## Fixpoint Solver
This will use the database to learn facts about a given program for a certain line. Because there is a baseline of shared properties of programming languages, this could also use a baseline library and everything else could be a plugin of some kind. In fact, the solver very much could not depend on any language and all questions the user wants answer about a program could be in terms of some core primitives.
The solver should only solve for things related to the question at hand. The database just allows for storing of generic facts about a program. The solver would just query the database for facts related to questions at hand.

## Frontend
What the user uses to interact with the solver. It should allow the user to select some code and then allow the user to select things they wish to learn about. This would likely also use treesiter to provide the highlighting interface for the user to interact with. It would also have to know about the programming language in particular so that it could prompt the user with things to learn about that bit of code.