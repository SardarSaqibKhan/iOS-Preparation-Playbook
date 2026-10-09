# Combine Framework
# Fundamentals:
### What is Combine, and why was it introduced by Apple?
Combine is Apple's reactive programming framework, introduced in 2019. It provides a way to work with streams of values and events, especially when values are delivered over time or asynchronously. Before Combine, we commonly used closures, delegates, KVO, and NotificationCenter to handle callbacks and changes. Combine provides a unified publisher-subscriber model where publishers produce values and subscribers receive those values and perform the required actions.

### What are the three main concepts in Combine?
The three main concepts are Publisher, Subscriber, and Subscription.

1- Publisher: Publisher produces and sends values over time.

2- Subscriber: Subscriber receives those values and performs an action.

3- Subscription: Subscription represents the connection between them and allows us to cancel it.

### What is a Publisher?
Publisher is a protocol with two associated types: Output and Failure, and it has a .receive(subscriber:) method used to connect a subscriber to the publisher.
it produces and sends values downstream to its subscribers.

### What is a Subscriber?
A Subscriber is a protocol with two associated types: Input and Failure. It receives values from a Publisher and performs actions on those values. It also receives a subscription through receive(subscription:)

### What is a Subscription?
A Subscription represents the connection between a Publisher and a Subscriber. It manages the subscription and provides a way to cancel it. When the AnyCancellable representing the subscription is deallocated, the subscription is cancelled.

###  What is the difference between a Publisher and a Subscriber?
A Publisher produces and sends values downstream, while a Subscriber receives those values and performs actions.

### What is a stream of values in Combine?
A stream of values is a sequence of values produced by a publisher and delivered to its downstream subscribers over time.

### What is sink and what does it return? 
sink is used to subscribe to a Publisher and handle the values and completion events. It provides two closures: receiveValue for handling emitted values and receiveCompletion for handling successful completion or failure. sink returns an AnyCancellable, which represents the subscription and can be used to cancel it.

### What is completion?
Completion tells us that a Publisher has finished sending values. In sink, the receiveCompletion closure is called when the Publisher finishes, either successfully with .finished or because of an error with .failure.

### What is demand/backpressure?
Demand is the number of values a Subscriber is ready to receive from a Publisher. Backpressure is the mechanism that allows the Subscriber to control the flow of values when the Publisher produces values faster than the Subscriber can process them.

### Hot vs Cold Publishers
A cold publisher starts producing values when a subscriber subscribes, and each subscriber can trigger its own execution. A hot publisher produces values independently of subscribers, so a subscriber may miss values that were emitted before it subscribed.

### What does AnyPublisher do, and why would you use it?
AnyPublisher is a type-erased publisher type in Combine, AnyPublisher hides the specific publisher type and exposes only the publisher's output and failure types.
```Swift
func makePublisher() -> AnyPublisher<Int, Never> {
[1, 2, 3, 4, 5].publisher
.filter { $0 % 2 == 0 }
.map { $0 * 10 }
.eraseToAnyPublisher()
}

/* Explanation:
1. publisher: Creates a publisher that emits 1, 2, 3, 4, 5.
2. filter: Keeps only even numbers (2, 4).
3. map: Multiplies each value by 10 (20, 40).
4. eraseToAnyPublisher(): Hides the complex concrete publisher type created by chaining filter and map, returning AnyPublisher<Int, Never>.

Output: 20 40

Why use AnyPublisher?
It hides the complex publisher type and provides a simpler, consistent return type. Int = Output type Never = Failure type (cannot fail with an error) */
Without eraseToAnyPublisher():
The concrete type is a nested publisher type: Publishers.Map<Publishers.Filter<...>, Int> The exact type depends on the upstream publisher type.

```


### What is eraseToAnyPublisher()?
eraseToAnyPublisher() is used for type erasure. When we apply operators to a Publisher, the resulting publisher can have a complex concrete type. eraseToAnyPublisher() hides that concrete type and exposes the publisher as AnyPublisher<Output, Failure>.

### What is AnyCancellable?
AnyCancellable is type-erased, which hold the subscription and let developer to cancel it when ever it need to be. 

### Why do we need to retain an AnyCancellable?
first and for most reason is to keep alive the subscription and then also for cancel the subscription.

### What happens if an AnyCancellable is deallocated?
When an AnyCancellable is deallocated, it automatically cancels the subscription.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Operators:
What is the difference between map and compactMap?
What is the difference between map and flatMap?
What does filter do?
What is the difference between combineLatest and zip?
### What does debounce do, and where would you use it?
debounce is use to add the delay between the values that publisher will publish.  

### What is the difference between debounce and throttle?
debounce: 
Wait until values stop coming.
For example, in a search field, if the user types character by character, we don't want to make a server request for every character. With debounce, we wait until the user stops typing for the specified time, then pass the latest value downstream.

throttle:
Controls how often values are passed downstream.
For example, if we receive frequent updates from a WebSocket, we can use throttle to control how often we process those updates and update the UI.

### What does removeDuplicates do?
it prevent consecutive(one after another.) duplicate value from being sent downstream.
it basically compare the current value with previous value and if its same it remove that. `
```Swift
1 → 2 → 1 → 1

Output:
1 → 2 → 1
```


What is the difference between prefix, first, and dropFirst?
What does switchToLatest do, and what problem does it solve?
What is the difference between catch and retry?

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Subjects & Publishers
### What is a PassthroughSubject?
passthrough subject pass the current value, does't persist the value, so in case new subscriber subscribe it, it will not get the current values. 

### What is a CurrentValueSubject?
opposite of passthrough it persist the value and pass downstream, if new subscriber subscribe it, then it will get the current value as well, 
basically its giving subscriber chance to catch up. 
I.e 
subscriber1 is subscribed to publisher and publisher pass values 1 -> 2  ->3 
now another subsciber which is subscriber2 subscribed it, it will get the current value in this case it will get 3 because its the latest one. 

### What is the difference between PassthroughSubject and CurrentValueSubject?
PassthroughSubject does not store the values it publishes. A new subscriber only receives values published after it subscribes. CurrentValueSubject stores its current value, so a new subscriber immediately receives the current value and then receives future values.


### When would you choose a Subject instead of creating a custom Publisher?

### What happens to a new subscriber when it subscribes to a CurrentValueSubject?
When a new subscriber subscribes to a CurrentValueSubject, it immediately receives the subject's current value, 
followed by any future values that are published. 

### What happens when a Publisher sends .finished?
When a Publisher sends .finished, it means the publisher has completed successfully and will not emit any more values. The subscriber receives the completion event, and the subscription is then terminated.

### What happens when a Publisher sends a failure?
When a Publisher sends .failure, it means the operation has completed unsuccessfully. The publisher will not emit any more values, and the subscriber receives the completion event along with the error. The subscription is then terminated.


What is the difference between a cold and hot publisher?

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Threading & Scheduling
Combine uses Schedulers to control where work happens.

### What is the difference between subscribe(on:) and receive(on:)?
subscribe(on:): Controls where the upstream work starts
receive(on:): Controls where the subscriber receives values

```Swift
publisher
    .subscribe(on: DispatchQueue.global())
    .receive(on: DispatchQueue.main)
```
This ensures background work runs off the main thread while UI updates occur on the main thread.

### How would you make sure that UI updates happen on the main thread?
I use .receive(on: DispatchQueue.main) to ensure that values are delivered to the downstream subscribers on the main thread, so UI updates happen safely on the main thread.
```Swift
publisher
    .receive(on: DispatchQueue.main)
    .sink { value in
        self.label.text = value
    }
```

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Senior-level:
How does backpressure/demand work in Combine?
What is Subscribers.Demand?
How does Combine handle cancellation?
What causes a Combine subscription to be cancelled automatically?
How can a Combine pipeline create a retain cycle?
How would you avoid a retain cycle when using sink?
What is the difference between sink and assign?
How would you test a Combine pipeline?
How would you expose a Combine publisher from a ViewModel without allowing external code to send values?
When would you choose Combine vs async/await / Swift Concurrency?
Recommended Practice Order
Publisher → Subscriber → Subscription
Subjects and CurrentValueSubject / PassthroughSubject
sink → AnyCancellable → cancellation
Basic operators: map, filter, compactMap, removeDuplicates
Combining publishers: combineLatest, zip, merge
Asynchronous operators: flatMap, switchToLatest, debounce, throttle
Threading: subscribe(on:) vs receive(on:)
Error handling: catch, retry, replaceError
Memory management and retain cycles
Advanced topics: demand, backpressure, custom publishers, testing
Real-world architecture and ViewModel usage
Combine vs Swift Concurrency
