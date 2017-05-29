What's pattern matching?

One way to think about it is code mimicking the data it's trying to represent.
The code can match the data. It can even capture parts of it, or the whole thing if you'd like.

Consider a fact: `[1 2 3]`.

Now compare it to our pattern matching rules syntax.
Is `[[4 5 6]]` a match? No
Is `[[2 3 1]]` a match? No
Is `[[1 2 3]]` a match? Yes
Is `[[1 2 (+ 1 2)]]` a match? Yes (Code runs in the value slot, so `(+ 1 2)` will resolve to 3)

Great job! That's pattern matching.

About eav tuples

Eav tuples (perhaps more appropriately called "triplets"), are 3 arity tuples with an entity-id in first position, an attribute of that entity in the second position, and a value for that attribute (for that entity) in the third position. The attribute (outside of any contrived examples you may see here) is always a keyword. The value can be anything. The eid depends on a few things, like how you want to id your facts, whether your facts are id'd already, and whether you would like Precept to treat your fact as a `:global` or `:transient`.

Match on a value

Fact: [1 :horse/color "white"]

[[_ :horse/color "white"]]
We're ignoring the first slot





