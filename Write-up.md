**What are rules and what is Clara?**

Rules are a logic-oriented declarative, relational way to deal with

* Lots of ever-changing "business logic"

* Logic needs to be easily understood by non-developers as it needs to conform to their needs

* Performance-oriented towards a state that advances incrementally with much of the state being derived from the last state, plus the need for incrementally-computed derived calculations (totals, logic, etc.)

That pretty much sums up dealing with ever-changing and evolving client-UX discoveries and requirements.

You can think of a rule engine as a combination of

1. A relational data store (like Datomic or Datascript) called a working memory that stores "facts"

2. RULES that operate over the store in any way imaginable:

* Match any fact or combination of facts (joins are automated) to trigger side-effects

    * Side-effects can be advancing the state atom or anything else so they serve as "action-handlers" or reducers

* Match any fact or combination of facts to insert derived computations (which can be chained)

    * Meaning they can serve as chained subscriptions or selectors

    * Including creating the "property" bag (view model, subscription, whatever you want to call it) for a component.

* Very performant at "recalculations"

Furthermore, these derived facts are under "truth maintenance" so they, and are truly reactive - when inputs change, they dynamically recompute forward instead of needing to be “requeried”.

So one coding idiom (rules) when applied to UI covers

* Store data

* Actions

* Action handlers (reducers, etc.)

* Derived data (selectors, etc.)

* Side-effects

* "Subscriptions" (hydration into props for a component plus direct notification)

We’ve been wanting to do this for years.

**What it’s not**

It’s not trying to solve the interface between client/server like om-next (though with a tuple-oriented rule-backed architecture, it might be a helpful building block in such).

It’s not trying to be general-purpose micro-framework that can be dropped in to other frameworks as a store - we don’t think the conceptual overhead of a rules engine is worth it unless it is pretty much doing all of the work.

it is just a highly opinionated but small, essentially the answer to the question "what if you just wanted a full-fledged rules engine to drive your UI without any other conceptual or code overhead?"

**Some things that annoy us we no longer have to do**

* Define a canonical object graph/tree to store data - we just stuff it into the state bag and "query over"

* Or manage it as imports which is much the same

* Agonize over what that graph should look like - match component tree? Table-oriented? (see, stuff into state bag and query over)

* Refactor a chained selector when one of its source data atoms becomes a selector (OK, we could have managed that with a little more discipline to wrap all state atoms in selectors)

* Switch between different code paradigm for actions vs. reducers vs. middleware vs. subscriptions/selectors.

**What could be next**

In theory this should be very performant over many micro-iterations and should be capable of maintaining logic over large graphs at even 30-60fps. So we want to use it for all of our logic-driven animation.

That’s banking on a RETE-oriented approach being more performant than a Knockout-computed-property approach (it should be for anything halfway complicated).

Other rendering pipelines - from pixi.js to something still DOM-oriented but custom (leveraging the fact that we can get precision information over what state/derived state exactly changed on each rule tick). React is plenty-fine for now.

Also, server-side running of the same or expanded rule-logic (multi-user "terminal" stuff)

**Once upon a time**

Mike has been wanting to wire up a rules engine to a UI for years.

Mike was early adopter of MVVM / reactivity and developed (now ancient and deprecated) GluJS as a MVVM to bind to Sencha to a differentially-computed view model.

The inspiration was the simplicity of spreadsheet formulas (and rules for that matter).

Knockout was out around the same time and essentially the same. The core model conception of both could be seen as a half-functional forward-chaining rules engine (if you had a rules background).

But Mike was always frustrated by the limitations of working with object-oriented data and view models required by Knockout and GluJs, instead of just working with relational data.

Experience with rule engines showed that they made things much easier by letting you put all data in a "bag" relatively unstructured and then sort, calculate, and chain as needed for your various outputs.

Plus rule engines are powerful enough to run declarative game logic in real-time either locally or to stream in the browser - and Mike wanted that badly.

But there was no good Rete-style rules engine in the browser.

Alex joined Mike to help write some hard-core code for a graphical story-telling engine in the browser, in addition to writing lots of nice-looking enterprise web apps.

Redux/React plus immutable structures helped clear up the conceptual space.

Digging behind Redux influences led Alex to jump into Clojure-land, with which he fell immediately in love and never came back. 

We found Clara, a very cool Rete rule engine that runs in the browser.

Alex put Clara inside of Reagent and here we are.

We think it’s pretty cool
