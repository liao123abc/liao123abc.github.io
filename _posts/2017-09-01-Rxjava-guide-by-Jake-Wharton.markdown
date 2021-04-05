---
layout: post
title:  "Rxjava Guide By Jake Wharton"
date:   2017-09-01 12:47:08 +0800
categories: Rxjava
catalog: true
tags:
    - Rxjava
---

“When you bow deeply to the universe, it bows back; when you call out the name of God, it echoes inside you.”
―Morihei Ueshiba

# from Jake Wharton
- usually do work when you start or stop listening.
- Sysnchronous or asynchronous.
- Single item, many items, or empty
    - single - network request
    - many - button click
    - empty - save file or database
- terminates with an error or succeeds to completion
- may never terminate
- just an implementation of the Observer pattern

### Rxjava
- a set of classes for representing sources of data
- a set of classes for listening to data sources
- A set of methods for modifying and composing the data

### Source
- Usually do work when you start or stop listening. 
- Synchronous or asynchronous. 
- Single item, many items, or empty.
- Terminates with an error or succeeds to completion. 
- May never terminate! 
- just an implementation of the Observer pattern

### sources
- Observable<T>
    - Emits 0 to n items
    - Terminates with complete or error.
    - Does not have backpressure.
- Flowable<T>
    - Emits 0 to n items
    - Terminates with complete or error.
    - Has backpressure

### Flowable vs Observable
- backpressure allows you to control how fast a source emits items.
- Rxjava 1.x added backpressure late in the design process
- All types exposed backupressure but not all sources respected it
- Backpressure, like interitance, must be designed for.

### Single
- Either succeeds with an item or errors. 
- No backpressure support.

### Completable
- Either completes or errors. Has no items! 
- No backpressure support. 

### Maybe
- Either succeeds with an item, completes with no items, or errors. - No backpressure support. 

### Flowable vs Observable

| —                             | Backpressure | No Backpressure |
| ----------------------------- | ------------ | --------------- |
| 0...n items, complete / error | Flowable     | Observable      |
| item / complete/error         | -            | Maybe           |
| item / error                  | -            | Single          |
| complete / error              | -            | Completable     |

### Source Specializations
- Encoding subsets of Observable into the type system
    - Single -item or error. think "scalar"
    - Completable - Complete or error, think "runnable".
    - Maybe - Item, complete, or error. think "optional"

### points ?
- create
- dispose

### Operators
- Manipulate or combine data in some way
- Manipulate threading in some way.
- Manipulate emissions in some way.


