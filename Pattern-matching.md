## What's pattern matching?

You might initially approach pattern matching as code that resembles the data it represents.

In our case, our pattern matching syntax is nearly identical to our data structures. This allows values to be extracted and matched according to their position in the data structure.

Data, or "facts", in Precept are 3-arity tuples comprised of an entity id (eid), attribute, and value:
```
[entity-id attribute value]
     |        |        |
     e        a        v
```
The entity id, or eid, is typically a UUID, the attribute is always a keyword, and the value can be anything. Ultimately ids are in the hands of users. Precept reserves the eid `:transient`. Any fact using this for its eid is automatically removed at the end of every session firing. 

Each position of the tuple is sometimes referred to as a "slot".


***

Here's an example fact with an eid of 123, `:horse/color` in the attribute slot, and "white" in the value slot:
```clj
[123 :horse/color "white"]
```

Here are some expressions that would match it.
```clj
[[?e :horse/color "white"]] ;; bind to the eid of a fact whose attribute is `:horse/color` and whose value is "white"
[[_ :horse/color ?v]] ;; bind to the value of a fact with the attribute `:horse/color`
[[_ :horse/color]] ;; equivalent to "when there is a fact with attribute `:horse/color`" or (if thing-with-horse-color-attr ...
[[?e :all "white"] ;; capture the eid of any fact with the value "white"
[?my-fact <- [_ :horse/color "white"]] ; when there's a :horse/color white, bind the whole fact to a variable called ?my-fact
```
Symbols that begin with a `?` are variables. They are declared as soon as you write them, and will become bound to the value in slot they occur. In our example, `?e` will be 123, `?v` will be "white" and so on. There are no restrictions on variable names other than they must be symbols and start with `?`. We are simply using `?e` and `?v` here by convention.

All facts in Precept descend from the attribute `:all`. This allows us to match eids or values of facts irrespective of their attribute.

# Joins
Say you have some facts about countries:
```clj
[[1 :country/name "England"]
[1 :country/capital "London"]
[1 :country/name "France"]
[2 :country/capital "Paris"]]
```

Here's how you would find England's capital:
```clj
[[?e :country/name "England"]]
[[?e :country/capital ?capital]]
```
Because the "country" entities each have the same eid, we can "join" on that eid in order to find another fact for that entity.

If you wanted to insert a label for each country capital, you could write a rule like the following:
```clj
(rule make-country-capital-labels
  [[?e :country/name ?name]]
  [[?e :country/capital ?capital]]
  =>
  (insert! [?e :country/label (str ?name ", " ?capital)]))
```
This rule will operate for every fact that matches it, meaning in our case it will create label facts for both France and England. If we had more facts that matched these conditions, it would generate labels for them as well.



