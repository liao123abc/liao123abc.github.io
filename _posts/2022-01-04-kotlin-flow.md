---
layout: post
title: "kotlin flow"
subtitle: "note"
date: 2022-01-04
author: "thomasliao"
published: true
header-img: "img/post-bg-2015.jpg"
tags: []
---

# flow
- ReactiveX
    - JVM. Rx port for Java called RxJava appeared in 2013. Version 1.0 was released in 2014
    - back by thread
- kotlin flow
    - Kotlin programming language released coroutines in 2018 as a general language feature
    - back by coroutines


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
    - code inside flow builder not run until it's collected
    - is not activated untile any terminal operator(for instance collect)
- stateflow and sharedflow are hot
    - hot stream
    - only one instance of flow runs for all multiple collectors
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
- StateFlow does not emit duplicate value.
    - use sharedFlow

```kotlin
// MutableStateFlow(initialValue) is a shared flow with the following parameters:
val shared = MutableSharedFlow(
    replay = 1,
    onBufferOverflow = BufferOverflow.DROP_OLDEST
)
shared.tryEmit(initialValue) // emit the initial value
val state = shared.distinctUntilChanged() // get StateFlow-like behavior
```

```kotlin
//StateFlow.kt
    override suspend fun collect(collector: FlowCollector<T>): Nothing {
        val slot = allocateSlot()
        try {
            if (collector is SubscribedFlowCollector) collector.onSubscription()
            val collectorJob = currentCoroutineContext()[Job]
            var oldState: Any? = null // previously emitted T!! | NULL (null -- nothing emitted yet)
            // The loop is arranged so that it starts delivering current value without waiting first
            while (true) {
                // Here the coroutine could have waited for a while to be dispatched,
                // so we use the most recent state here to ensure the best possible conflation of stale values
                val newState = _state.value
                // always check for cancellation
                collectorJob?.ensureActive()
                // Conflate value emissions using equality
                if (oldState == null || oldState != newState) {
                    collector.emit(NULL.unbox(newState))
                    oldState = newState
                }
                // Note: if awaitPending is cancelled, then it bails out of this loop and calls freeSlot
                if (!slot.takePending()) { // try fast-path without suspending first
                    slot.awaitPending() // only suspend for new values when needed
                }
            }
        } finally {
            freeSlot(slot)
        }
    }
```



```kotlin
//SharedFlow.kt
    @Suppress("UNCHECKED_CAST")
    override suspend fun collect(collector: FlowCollector<T>): Nothing {
        val slot = allocateSlot()
        try {
            if (collector is SubscribedFlowCollector) collector.onSubscription()
            val collectorJob = currentCoroutineContext()[Job]
            while (true) {
                var newValue: Any?
                while (true) {
                    newValue = tryTakeValue(slot) // attempt no-suspend fast path first
                    if (newValue !== NO_VALUE) break
                    awaitValue(slot) // await signal that the new value is available
                }
                collectorJob?.ensureActive()
                collector.emit(newValue as T)
            }
        } finally {
            freeSlot(slot)
        }
    }
```


## fragment lifecycle and ViewLifeCycle
- Normal fragments should always use viewLifecycleOwner to trigger UI update
    - or lead to Memory Leak
        - when lifecycle Lifecycle is destroyed, job would be cancel
            - fragment might always there
            - view created all the time
- DialogFragment is the exception


> bad

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

> good

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

        // Create a coroutine
        lifecycleScope.launchWhenStarted {
            view_message_red_point.visibility = if (mViewModel.queryMessageRedPot()) View.VISIBLE else View.GONE

            launch {
                mViewModel.channelId.collect {
                    if (it is Result.Success) {
                        live_room_id_tv.text = "直播间ID：${it.value}" //fixme
                    } else {
                        KLog.i(TAG, "fail to get channel id: ${(it as? Result.Failure)?.errorMessage}")
                    }
                }
            }

            launch {
                mViewModel.userInfo.collect {
                    it?.apply {
                        user_name_tv.text = it.byNickName
                        anchor_id_tv.text = "主播ID：${it.yyId}" //fixme
                        avatar_iv.run {
                            imageService.load(this@PersonalFragment, it.portrait, this)
                        }
                    }
                }
            }

            launch {
                mViewModel.userExtInfo.collect {
                    if (it is Result.Success) {
                        focus_number_tv.text = it.value.followCount.toString()
                        fans_number_tv.text = formatFansCount(it.value.fanCount)
                    } else {
                        KLog.i(TAG, "fail to user ext info: ${(it as? Result.Failure)?.errorMessage}")
                    }
                }
            }
        }
    }

```

> frequently change data should always use repeatOnLifecycle

```kotlin
        // Create a new coroutine since repeatOnLifecycle is a suspend function
        lifecycleScope.launch {
            // The block passed to repeatOnLifecycle is executed when the lifecycle
            // is at least STARTED and is cancelled when the lifecycle is STOPPED.
            // It automatically restarts the block when the lifecycle is STARTED again.
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                // Safely collect from locationFlow when the lifecycle is STARTED
                // and stops collection when the lifecycle is STOPPED
                locationProvider.locationFlow().collect {
                    // New location! Update the map
                }
            }
        }

```



![wasting resource](https://miro.medium.com/max/2000/1*fmQRBPMPpnO7NAO2bg0GKw.png)



> configured to skip short resume

```kotlin
val result: StateFlow<Result<UiState>> = someFlow
    .stateIn(
        scope = viewModelScope, 
        started = WhileSubscribed(5000), 
        initialValue = Result.Loading
    )
```

![best performance](https://miro.medium.com/max/1400/0*AJokESYOHI4uxfWs)


## reference

[A safer way to collect flows from Android UIs](https://medium.com/androiddevelopers/a-safer-way-to-collect-flows-from-android-uis-23080b1f8bda)

[Reactive Streams and Kotlin Flows](https://medium.com/@elizarov/reactive-streams-and-kotlin-flows-bfd12772cda4)

[kotlin flow](https://developer.android.com/kotlin/flow)

[Migrating from LiveData to Kotlin’s Flow](https://medium.com/androiddevelopers/migrating-from-livedata-to-kotlins-flow-379292f419fb)