# Reordering 59 Million New York Times Publishing Assets Using Go and BadgerDB

TL;DR; using Go to solve some messy "medium-data" ordering and cleanliness
issues. Benefited from speed of Go and badger.

## Assets

One article:

* "article": headline, text, ...
* "image"
* "section"
* "person"
* "subject"

Others:

* Top-level: article, interactive, newsletter
* Tags
* Groupings
* Things: image, video, movie, theater, recipe, restaurant

Assets refer to each other.

## Publishing

"Published" in topological order.

Published onto the "monolog", a kafka stream.

Multiple publishers, multiple consumers (search, web, mobile)

So far, over 59 million assets.

## Ordering

Problems:

* after publishing old archives to the monolog ended up with chronological
  disorder
* data issues/bugs caused lack of topological sort

## Solution

Is this big data? No.

59 million records on 16GiB RAM laptop, so 300 bytes each.

Can we use a key-value DB on a local machine?

1. Step 1: insert and sort by timestamp
2. Step 2: find and resolve missing references
3. Step 3: delete duplicates

## Needs

* Fast
* In-memory cache

Chose Go and BadgerDB.

...
