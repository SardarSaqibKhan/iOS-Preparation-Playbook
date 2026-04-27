# iOS-Preparation-Playbook

## 📋 Table of Contents

- [Computed Property vs Lazy Variable](#computed-property-vs-lazy-variable)
- [Closures](#what-are-the-closures-in-swift)
- [Struct vs Class](#struct-vs-class)
- [Heap vs Stack](#heap-vs-stack)
- [UIKit Lifecycle](#uikit-lifecycle)
- [GCD](#gcd)
- [Async/Await](#asyncawait)
- [Thread Safety](#thread-safety)
- [Networking](#networking)
- [Persistence](#persistence)
- [Testing](#testing)
- [Security](#security)
- [Modularization](#modularization)


## Computed property VS Lazy variable

Computed properties recalculate their value every time they’re accessed, while lazy properties compute once on first access and then store the result.

### Computed Property:
A computed property does not store a value. Instead, it calculates and returns a value every time it is accessed.

Key Points: 
- Does not store any value
- Recomputed every time you access it
- By default, it is read-only, we only can read the value.
- Becomes writable if you add a set.
- Can impact performance if computation is heavy
- Can be defined in extensions

```swift 
struct Circle {
    var radius: Double
    
    var diameter: Double {
        return radius * 2
    }
}

var circle = Circle(radius: 15)
print(circle.diameter) // 30
```

Making it writable:

```swift 
var diameter: Double {
    get {
        return radius * 2
    }
    set {
        radius = newValue / 2
    }
}

circle.diameter = 40
print(circle.radius) // 20
```


### Lazy Property:
A lazy variable is a stored property whose initial value is not calculated until the first time it is accessed

Key Points:
- Stored property (keeps its value in memory)
- Initialized only on first access
- Not recomputed after initialization
- Must be declared with var (not let)
- Cannot be used in extensions (only stored properties allowed in main type)
- Useful for expensive setup or deferred work

```swift
class DataLoader {
    lazy var data: [Int] = {
        print("Loading data...")
        return [1, 2, 3]
    }()
}

let loader = DataLoader()

print("Before access")
print(loader.data) // "Loading data..." printed once
print("After first access")
print(loader.data) // No print, reused value
```
Output:
```swift 
Before access
Loading data...
[1, 2, 3]
After first access
[1, 2, 3]
```

### Question related to this: 
###  What are the memory implications of lazy vs computed properties?
###  Can a lazy property be a constant (let)? 
No must be var, as the value will change run time we have to define it as var 

## What are the closures in swift?

closures are self contain block of code that can be pass around as functiona paramter.



### Question related to this: 
###  What does it mean self contain block of functionality?
###  How it can be passed around?

-------------------------------------------------------------------------------------------------------------------------------------
###  Difference between escaping and non-escaping closures? 
- By default all closures are non-escaping
- For escaping closure we have keyword @escaping and we need to insert before closure.
- escaping closure will stay in memory even after function body get executed, its helpful when function depends on outside response.
- for async calls we have to use the @escaping
- 



-------------------------------------------------------------------------------------------------------------------------------------
###  How closures are different than function? 
- function has name but closure does't 
- 


-------------------------------------------------------------------------------------------------------------------------------------
###  Why even we need closure what are the alternative ?
- yes, closure basically a mechanism to return call back, to alternative for this is to we can achieve through protocols, notificatoin center, etc.


-------------------------------------------------------------------------------------------------------------------------------------
###  What is a retain cycle in closures?
Retain cycle happens when two refrence object point to each other in strong manner, in closure when we use object inside closure in strong way it hold it that can create the retain cycle, to avoid this we can use the weak and unowned refernce of self.

-------------------------------------------------------------------------------------------------------------------------------------
###  What is trailing closures ?
When a closure is the last parameter of a function, you can write it outside the parentheses.that means when we can omit the parameter name, basically swift facalitate developers to write cleand code thats why trailing closures exist.

```Swift
func perform(action: () -> Void) {
    action()
}

// Without trailing closure 
perform(action: {
    print("Hello")
})

// With trailing closure
perform {
    print("Hello")
}
```

###  What if a function has more than one argument of closures ?
well, in that case the first closure will be the trailing and after that will be with name.

```Swift
func fetchData(success: () -> Void,failure: () -> Void) {

}

fetchData {
    print("Success")
} failure: {
    print("Error")
}
```
-------------------------------------------------------------------------------------------------------------------------------------


## Protocols


## Struct vs Class

## Heap vs Stack

## UIKit Lifecycle

## GCD

## Async/Await

## Thread Safety

## Networking

## Persistence

## Testing

## Security

## Modularization
