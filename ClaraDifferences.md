## Different usage means different data modeling
Clara (by default) operates over records or maps.
This makes a lot of sense in ordinary back-end business contexts in which the goal is to "reason" over a large body of facts and come quickly to a "decision".
Typically, that can be done additively. There is no need to edit the initial "ground facts". All you need to do is add additional downstream consequences in a logical chain until evaluation is complete.
There need not be any notion of a particular entity changing over the course of the reasoning pass.

Dealing with a persistent session that models ever-progressing mutable state is at the opposite end of the spectrum.
Here, we want to know *what exactly* changed in the smallest increments possible. 
This is for two reasons, one minor and one deeper:

Minor: We want to avoid loops that are often triggered by "modify" operations of templates/records/facts in rules. In a map representation of an entity with 10 attributes, when one attribute changes, Clara refires *all* rules that touch *any* of the other 9 attributes.
Older rules frameworks like Drools solve this by making objects "property-reactive". This could be addressed in the future at the engine level.

Deep: We want to be able to track *what exactly* changed or is being changed. Even if a true modify with "property-reactive" were implemented, it would not allow us to know about changes deeper than the entity level. We'd only be able to know that one of the entity's attributes changed.

## EAV

It is the "change of ground facts over time" consideration that we chose to go with an entity-attribute-value tuple way of modeling data.
This not only gives us the right level of change granularity, it's now also a familiar way of modeling data within Clojure thanks to Datomic and Datascript.
N-tuples can offer even more concision in some cases, but e-a-v tuples are pragmatic for most applications. 


## Performance implications
We've yet to discover any performance ramifications that result from using this data model with Clara. 
Since Clara performs joins in parallel, it may not be not able to leverage "exit early" conditional testing that likely takes place when conditions are tested against a single underlying data structure.
Our approach was to select a data model that fit a language and way of thinking designed for programmers. The machine's ability to deal with it is--and should be--secondary. When the preferences that enable programmers to be the most productive are found to be nonperformant, and there's no practical implementation we can add that allows them to be, then and only then should we be open to concecessions in this area. Until then, we plan to maintain singular focus on improving "performance" by allowing developers to write software declaratively.
