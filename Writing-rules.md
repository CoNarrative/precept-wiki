Precept incorporates the following aspects of [Clara's DSL](http://www.clara-rules.org/docs/expressions/):
#### Boolean expressions
```clj
[:or [?e :attr ?v]
     [?e :attr2 ?v]]
...
[:exists [?e :attr ?v]]
[:exists [:attr]]
...
[:not [_ :attr 42]]
```

- Test expressions
- Accumulators

