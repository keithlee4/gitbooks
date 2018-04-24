# Observable & Observer

Observable and observer are the two most basic roles in RxSwift. Since everything in FRP should be an event stream, there are two important concepts we should be aware of.

* An **Observable **is something which emits notifications of change.
* An **Observer** is something which **subscribes to an observable. **It will be notified when the observable emit new event.

### Event

An event is something that is sent from observable, it has three types:

1. **.Next\(Value\)** means an event of new element.
2. **.Completed** means the end of the event stream
3. **.Error** means an event of error, it will end the stream as well.

Below is a simple chart of event flow\([source](http://rxmarbles.com/#timer)\)

![](../.gitbook/assets/simple-event-flow%20%282%29.png)

The most easy way to custom a observable is `Observable.create()` .

Example:

```text
let simpleStream = Observable<Int>.create {
    observer -> Disposable in
    observer.onNext(0)
    observer.onNext(1)
    observer.onNext(2)
    observer.onCompleted()
    
    return Disposables.create()
}

let subscription = simpleStream.subscribe(
    onNext: {
        value in
        print(value)
    },
    onCompleted: {
        print("Completed")
    }
)

let disposeBag = DisposeBag.init()
```

There are many common way to create observables. 

### Empty

`empty()` is an empty sequence, it emit only `.Completed` event.

```text
let emptySequence: Observable<Int> = Observable.empty()
let subcription = emptySequence.subscribe { 
    event in print(event)
}

//Completed
```

### Never

`never()` is a sequence has no element and emit no events.

```text
let neverSequence: Observable<Int> = Observable.never()
let subscription = neverSequence.subscribe {
    _ in print("This block is never called")
}
```

### Just

`just(value)` is a sequence which contains only one element, it will emit `.Completed` event right after  the `.Next(value)` event.

```text
let singleElementSequence = Observable<Int>.just(0)
let subscription = singleElementSequence.subscribe {
    event in print(event)
}

//Next(0)
//Completed
```

### Of/From

`of(input...)` and `from(sequenceInput)`  is sequence which take a sequence of elements as input and transform it to a sequence of events.

```text
let sequenceOfElements_of = Observable<Int>.of(0,1,2,3)
let subscription = sequenceOfElements_of.subscribe {
    event in print(event)
}

let sequenceOfElements_from = Observable<Int>.from([0,1,2,3])
let subscription = sequenceOfElements_from.subscribe {
    event in print(event)
}

=========Both subscriptions output are=========
//Next(0)
//Next(1)
//Next(2)
//Next(3)
//Copmleted
```

### Create

`create((AnyObserver<Element>) -> Disposable)` is used to customize the logic of event emitting. 

```text
let customizedSequence = Observable<Int>.create {
    observer -> Disposable in
    observer.onNext(1)
    observer.onNext(100)
    observer.onNext(0)
    observer.onCompleted()
    return Disposables.create()
}

let subsription = customizedSequence.subscribe {
    event in print(event)
}

//Next(1)
//Next(100)
//Next(0)
//Completed
```


