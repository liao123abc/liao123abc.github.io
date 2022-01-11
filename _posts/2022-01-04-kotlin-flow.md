---
layout: post
title: "kotlin flow"
subtitle: "note"
date: 2022-01-04
author: "thomasliao"
published: false
header-img: "img/post-bg-2015.jpg"
tags: []
---

# flow

## livedata
- won't call when view is paused

```kotlin
//viewmodel
val nameLivedata: MutableLiveData<String> by lazy {
    MutableLiveData<String>("origin name-livedata")
}
//fragment
val nameObserver = Observer<String> { newName ->
    binding.nameLiveData.text = newName
}

```

```kotlin

//won't update when onPaused
mMainViewMode.nameLivedata.observe(viewLifecycleOwner, nameObserver)

//observe forever make it work
mMainViewMode.nameLivedata.observeForever(nameObserver)
```

## kotlin plain flow
- stateless
    - no value
- cold(declarative)
    - each collector will be a new flow created
    - not good for heavy job

## flow vs livedata
- livedata
   - 2017
   - observer pattern
   - lifecycle aware
   - require initial state
- flow
    - steep learning curve
        - simple things are harder and complex things are easier
    - Compose is coming
    - asynchronous programming
    - sequential
    - better way to handle stream of data
        - vs Rxjava
    - alternative for Rxjava
    - java cannot use flow&&stateflow&&sharedflow
- which one to use
    - flow
        - continuous data flow、complex asnyc operations 
    - livedata
        - service calls, state changes e.c.

## hot and cold
- flow is cold
- stateflow and sharedflow are hot
    - collecting from the flow doesn't trigger any producer code

## stateflow vs sharedflow
- stateflow--special kind of sharedflow
    - provide its initialState
    - always have value and only have a value
    - access its current state, like livedata
    - new collector only get the newest state
        - won't get the previous states
- sharedflow--configurable generalization of StateFlow
    - hot
        - shared between multiple collectors
    - has buffer(replay cache)
- State flow is a shared flow

```kotlin
// MutableStateFlow(initialValue) is a shared flow with the following parameters:
val shared = MutableSharedFlow(
    replay = 1,
    onBufferOverflow = BufferOverflow.DROP_OLDEST
)
shared.tryEmit(initialValue) // emit the initial value
val state = shared.distinctUntilChanged() // get StateFlow-like behavior
```

## fragment lifecycle and ViewLifeCycle
- Normal fragments should always use viewLifecycleOwner to trigger UI update
    - or lead to Memory Leak
        - when lifecycle Lifecycle is destroyed, job would be cancel
            - fragment might always there
            - view created all the time
- DialogFragment is the exception


bad
```kotlin
lifecycleScope.launchWhenStarted {
    launch {
        mViewModel.channelId.collect {
            if (it is Result.Success) {
                live_room_id_tv.text = "直播间ID：${it.value}" //fixme
            } else {
                KLog.i(TAG, "fail to get channel id: ${(it as? Result.Failure)?.errorMessage}")
            }
        }
    }
}
```

good
```kotlin
viewLifecycleOwner.lifecycleScope.launchWhenResumed {
    launch {
        mViewModel.channelId.collect {
            if (it is Result.Success) {
                live_room_id_tv.text = "直播间ID：${it.value}" //fixme
            } else {
                KLog.i(TAG, "fail to get channel id: ${(it as? Result.Failure)?.errorMessage}")
            }
        }
    }
}
```

## repeatOnLifecycle
- solve the problem of wasting resources
    - normally when the data change frequently
- update on the background might lead to crash

> static data might skip repeatOnLifecycle

```kotlin

```

> frequently change data should always use repeatOnLifecycle

```kotlin

```