# Starting a Calculations Engine for Evaluating Dynamic Expressions at Scale

## Grammar

* Literals `1`, `21`, `1.1`, `3.4`
* Prefix Expresisons: `-1`
* Infix Expressions: `1+2`
* String literal: `kg`
* Variable expression: `plot[Moisture percentage]` (describes observations
  which can be assigned)
* Call expressions

## Calculations Engine

Calculations API:

```
                 (1 - plot[Moisture percentage] * plot[Total Mass])
plot[yield] = -------------------------------------------------------
              prefer (plot[Harvested area], plotSet[Hardvested Area])
```

Observations Consumers: Finds all calculations which the event observation can
be assigned to a variable

Transactions API: Tracks calculation attempt states

Resolvers: finds needed entities (plot, plotSet) and creates new transactions

Executors: find observations, assign to variables, evaluate formula

Recursive process: outputs of calculations can trigger updates of other
formulae.

## Throughout Limits

Bottlenecks are finding observations from external sources.

...
