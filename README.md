# iOS-Preparation-Playbook

## 📋 Table of Contents

- [Computed Property vs Lazy Variable](#computed-property-vs-lazy-variable)
- [Closures](#closures)
- [Protocols](#protocols)
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

## Closures

closures are self contain block of code that can be pass around as functiona paramter.

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
