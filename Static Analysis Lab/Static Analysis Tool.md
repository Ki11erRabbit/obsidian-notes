
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