class: center, middle
# <img src="media/rxjs.png" width="100" style="position: relative; top: 30px; margin-left: -50px"> RxJS
Reactive Extentions for JavaScript

---

### Agenda

- Reactive Programming

- RxJS basics

- Core operators

- Errors handling

- Advanced topics

---
class: center, middle

## Reactive Programming

---

### Reactive Programming

- Simplify the implementation of event-driven application

- Simple mechanism for working with asynchronous code

- Continuous sequences of events over time

???
Programmation reacive est un paradigm de programmation qui concerne les flux des donnees 
et propagation sync/async des event dans ces flux 

---

### RP, FP and OOP

**Object-Oriented programming**  
- Objects are central unit of work  
- State or data is held in objects properties.  
- Imperative programming with side effects  

--

**Functional programming**  
- Functions are the main unit of work  
- State is transient, never remains stored, flows through the streams
- Declarative programming, immutable, side-effect free(pure)

--

**Reactive programming**   
- "Continuous sequences of events over time" are main unit of work
- See data as a constantly flowing stream of change  
- Abstracting the notion of time
- RP <div style="display: inline-block; padding: 8px"><i class="fa fa-minus"></i><span style="font-size:0.7em; vertical-align: middle;">extend</span><i class="fa fa-arrow-right"></i></div> FP  

???


---
class: center, middle

## RxJs Basics

---

### RxJS

- Reactive programming model for JS  

--

- Rx = Observables + LINQ + Schedulers  

--

- Rx = loadash for async 


???
Microsoft's Rx terminology "Rx = Observables + LINQ + Schedulers" 

RxJS - "loadash for async" - Ben Lesh

Provide combining operations  

RxJs borrows lots of principles from FP, particularly:
_function chaining, lazy evaluation, and the notion of using an abstract data type to orchestrate data flows_.

---

### Rx & RxJS History

* **2007** Microsoft Volta project  
  
* **2010** Rx.Net  

* **2011** RxJS ported by Matthew Podwysocki from Rx.Net  

* **2012** Rx.Net Open Source  

* **2015** RxJs v4.0  

* **2017** RxJs v5.0 owned by Ben Lesh (Netflix)  

* **Now**  Rx is supported in more than 10 languages ([http://reactivex.io])  

---

### Components of an Rx stream

- Producer

--

- Data pipeline

--

- Consumer

--

```javascript
 Rx.Observable.from([1, 2, 3]) // Producer
 .filter(x => x % 2 !=0 ) 
 .map(x => x * 2) 
 .subscribe(console.log) // Consumer
```

???
- Producer: Producers are the source of our data. The observer pattern defines producers as the Subject;
in RxJs we call them Observables. TC-39 Observable spec.

- Data pipeline: RxJs operators
- Consumer: Streams only travel from the producer to the consumer, not the other way around.


---

### Rx stream example


```javascript
Observable
 .range(1, Number.POSITIVE_INFINITY) 
 .take(100)
 .subscribe(console.log);
```
   
???

Lazy evaluation. Streams sit idle until a subscriber(a consumer) is attached to it.
the paradigm you use should not change whether you’re dealing with 1, 100, or thousands of events. 
RxJS offers a single computing model to handle finite as well as infinite streams.

---

### Create an Observable

#### Observer API

- `next(value): void`   
Receives next value from upstream source.
- `error(exception): void`   
Receives an error notification from upstream source
- `complete(): void`   
Receives a completion notification from upstream source     

???

next(value): void 

This is the equivalent of update in the Observer pattern. When a single function is passed into subscribe() instead of an observer object, it maps to the observer’s next().

error(exception): void

This would indicate that it encountered an exception and will not be emitting any more messages to the Observer (subsequent calls to next are ignored). Generally, error objects are passed in, but you could customize this to pass other types as well.  

complete(): void

Subsequent calls to next (if any) are ignored.

---

### Create an Observable
#### Observable API

- `create(observer): Observable`   
Create a new Observable
- `subscribe(observer): Subscription`    
Subscribe an observer to upstream source
- `subscribe(next, error, complete): Subscription `    
Subscribe to upstream source

---
### Create an Observable

```javascript
const source$ = Rx.Observable.create(observer => {
  observer.next('Hello');
  observer.next('Rx');
  observer.complete();
});

source$.subscribe(val => console.log(val));
```

---
class: center, middle

## RxJS Core Operators

---

### Creation operators

- `Observable.of(1, 2, 3)`
- `Observable.from([1, 2, 3, 4])`
- `Observable.fromEvent(document, 'click')`
- `Observable.fromPromise(promise)`
- `Observable.empty()`
- `Observable.never()`
- `Observable.interval(1000)`

---

### Filtering

- `Observable.filter(x => x % 2 === 0)`
- `Observable.skip(2)`
- `Observable.distinctUntilChanged()`
- `Observable.take(5)`

---

### Transformation

- `Observable.map(x => x * x)`
- `Observable.bufferCount(5)`

---

### Aggregation

- `Observable.reduce((acc, val) => acc + val, 0)`
- `Observable.scan((acc, val) => acc + val, 0)`

---

### Combine

- `Observable.merge(clickOk$, clickApply$)`
- `Observable.combineLatest(data$, filter$, sort$, (d, f, s) => s(f(d))`
- `Observable.withLatestFrom(save$, data$)`
- `Observable.mergeMap(x => Observable.fromPromise())`
- `Observable.switchMap(x => Observable.fromPromise())`
- `Observable.forkJoin(clients$, accounts$)`

---

### Time-based

- `Observable.debounceTime(300)`
- `Observable.throttleTime(300)`

???

debounce = emits an event from an observable sequence only after a particular timespan (in milliseconds) has passed without it emitting any other item

handle user input (wait, wait, emit)

throttle = execute a function at most once every period of time 

(emit, wait(2000), emit, wait(200), emit)

---

### Errors Handling

- `Observable.catch(error => Rx.Observable.empty())`
- `Observable.retry(3)`
- `Observable.do(x => console.log(x))`
- `Observable.finally(x => cleanResources())`

---
class: center, middle

## RxJS Advanced topics

---

### Hot and Cold observables

It's all about the nature of data source

#### Cold
- Movies
- Create a new independent stream for each subscription
- Produce evens when an observer subscribes to it

```
    Rx.Observable.from(), of(), etc... 
```

--

#### Hot
- TV livestream
- Share values between subscriptions
- Produce events regardless of subscribers

```
 Rx.Observable.fromEvent(), fromPromise(), etc...
```

---

### Rx Subjects

- Subject = Observable + Observer   

- Subject has state, it keeps a list of observers   

- Subject are not reusable   

- Multicasting operators uses Subjects internally

???
Subject has a state. On the other hand, an Observable is really just a function that sets up observation.

Subject are not reusable. When a Subject completes or errors, it can no longer be used.


---
### Rx Subject API
 
#### Observable
- `next(value): void`
- `complete(): void`
- `error(exception): void`

#### Observer
- `subscribe(observer): Subscripton`

#### Subject only
- `asObservable: Observable`

---

### When to use Subjects 

```javascript
const subject = new Subject();
button.addEventListener(‘click’, () => 
    subject.next('click');
subject.subscribe(x => console.log(x));
```

--

```javascript
Observable.fromEvent(button, 'click');
```

???
TRY NOT TO DO THIS: What people usually first do with Subjects when they find them

---
### Share values with Subjects

```javascript
const tick$ = Rx.Observable.interval(1000);
const subject = new Rx.Subject();
tick$.subscribe(subject);

subject.subscribe(x => console.log('A:', x));
subject.subscribe(x => console.log('B:', x));
```

---

### Share values with multicast operators 

```javascript
const tick$ = Rx.Observable.interval(1000);
const subject = tick$.share();

subject.subscribe(x => console.log('A:', x));
subject.subscribe(x => console.log('B:', x));
```

???
To "share" the observable tick$ with two observers,
observer1 and observer2, we can pipe all notifications
through a Subject

---

### Multicast operators

- `Observable.share()`
- `Observable.publishReplay(1).refCount()`
- `Observable.publishLast().refCount()`

---

### Schedulers

- Time is managed using Schedulers

- Control when a subscripton starts

- Control when notification are published

--

- `Scheduler.queue` = immediate
- `Scheduler.asap` = `setTimeout`
- `Scheduler.async` = `setInterval`
- `Scheduler.animationFrame` = `requestAnimationFrame`

???
A scheduler consists of three main parts:
 A data structure that stores all of the actions queued to be executed
- An execution context that knows where the action will be executed: timer, interval,
immediately, callback, a different thread (for server-side Rx frameworks), etc.
- It has a virtual clock which provides notion of time for itself. This point will become
very important for testing.

---
class: center, middle

### Everything is a stream

---

### Links

- RxJS 5 repository and docs [https://github.com/ReactiveX/rxjs]
- Video courses egghead.io [https://egghead.io/browse/libraries/rxjs]
- Writing marble tests [https://github.com/ReactiveX/rxjs/blob/master/doc/writing-marble-tests.md]
- Ben Lesh at medium.com [https://medium.com/@benlesh]
- TC 39 Observable proposal [https://github.com/tc39/proposal-observable]

---