## Different use case, different data model
In most contexts, rule engines reason over a large body of facts and come to a decision. This tends to result in sessions that have two states: the one with the initial facts, and the one with the answer. After that, you're done. There may be a new problem to solve, but it's not typically dependent upon the previous decision, so state isn't carried forward. Instead, a new session is created with the new initial state.
Information from the outside world is usually added once at the beginning of the session. The fact that "Patient A has a temperature of 98.6 F" doesn't change. This would like a user never changing `:visbility-filter` from its initial state of `:all` to `:done`. (Ostensibly, it would be equivalent to _nothing_ changing.) Ideally, facts in a session aren't modified. Instead, new facts are added. "When their temperature is high, the patient should see the doctor. When they have a cough, they should see the doctor. If any fact about seeing the doctor exists, recommend the patient see the doctor." These systems build up knowledge by progressively adding facts. Anything that is removed or modified is typically  the result of `insert!` (insert-logical). When the rules are done firing, you have your answer ("Patient A is healthy"), and you start over with a clean slate and insert then next group of facts from the outside world.


Dealing with a persistent session that models ever-progressing mutable state is at the opposite end of the spectrum. Because a user may stipulate the equivalent of "Patient A's temperature is 98.6. Actually now it's 98.7," Precept's use case is at the opposite end of the spectrum. We have facts coming in from the outside world all the time.
Here, we want to know *what exactly* changed in the smallest increments possible. 
This is for two reasons, one minor and one deeper:

Minor: We want to avoid loops that are often triggered by "modify" operations of templates/records/facts in rules. In a map representation of an entity with 10 attributes, when one attribute changes, Clara refires *all* rules that touch *any* of the other 9 attributes.
Older rules frameworks like Drools solve this by making objects "property-reactive". This could be addressed in the future at the engine level.

Deep: We want to be able to track *what exactly* changed or is being changed. Even if a true modify with "property-reactive" were implemented, it would not allow us to know about changes deeper than the entity level. We'd only be able to know that one of the entity's attributes changed.

## EAV
By default, Clara operates over records or maps.
This makes sense given their use case.

It is the "change of ground facts over time" consideration that we chose to go with an entity-attribute-value tuple way of modeling data.
This not only gives us the right level of change granularity, it's now also a familiar way of modeling data within Clojure thanks to Datomic and Datascript.
N-tuples can offer even more concision in some cases, but e-a-v tuples are pragmatic for most applications. 


## Performance implications
We've yet to discover any performance ramifications that result from using this data model with Clara. 
Since Clara performs joins in parallel, it may not be not able to leverage "exit early" conditional testing that likely takes place when conditions are tested against a single underlying data structure.
Our approach was to select a data model that fit a language and way of thinking designed for programmers. The machine's ability to deal with it is--and should be--secondary. When the preferences that enable programmers to be the most productive are found to be nonperformant, and there's no practical implementation we can add that allows them to be, then and only then should we be open to concecessions in this area. Until then, we plan to maintain singular focus on improving "performance" by allowing developers to write software declaratively.
