# iOS-Preparation-Playbook

## 📋 Table of Contents

- [Computed Property vs Lazy Variable](#computed-property-vs-lazy-variable)
- [Closures](#what-are-the-closures-in-swift)
- [Struct vs Class](#struct-vs-class)
- [Stack vs Heap Memory](#stack-vs-heap-memory)
- [UIKit Lifecycle](#uikit-lifecycle)
- [GCD](#gcd)
- [Async/Await](#asyncawait)
- [Thread Safety](#thread-safety)
- [Networking](#networking)
- [Persistence](#persistence)
- [Testing](#testing)
- [Security](#security)
- [Modularization](#modularization)
- [Copy On Write (COW)](#COW/README.md)


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

####  What are the memory implications of lazy vs computed properties?
####  Can a lazy property be a constant (let)? 
No must be var, as the value will change run time we have to define it as var 

## What are the closures in swift?

closures are self contain block of code that can be pass around, in other words Closures are like functions without names that you can assign to variables, pass into functions, or return from functions.

```swift
// Closure = “block of code you can store” example
// no name (unlike a function)
// stored in a variable
// can be called later
let greet = {
    print("Hello")
}

greet() // Hello

// “Self-contained block of code” example
// it has input, logic, output just like a function, but no name
let add = { (a: Int, b: Int) -> Int in
    return a + b
}

print(add(2, 3)) // 5

// “Pass into a function” example
func performAction(action: () -> Void) {
    action()
}

performAction {
    print("Button tapped")
}

// “Return from a function”
func makeGreeter() -> () -> String {
    return {
        return "Hello!"
    }
}

let greeter = makeGreeter()
print(greeter()) // Hello!
```

-------------------------------------------------------------------------------------------------------------------------------------
####  Difference between escaping and non-escaping closures? 
- By default all closures are non-escaping
- For escaping closure we have keyword @escaping and we need to insert before closure.
- escaping closure will stay in memory even after function body get executed, its helpful when function depends on outside response.
- for async calls we have to use the @escaping
- 

#### What exactly does ‘capturing values’ mean?

When you use variables inside a closure, Swift captures those variables from the surrounding scope and holds onto them.

```swift
// capturing values in action.
func makeCounter() -> () -> Int {
    var count = 0
    return {
        count += 1
        return count
    }
}

let counter = makeCounter()
print(counter()) // 1
print(counter()) // 2
```
- count is a local variable inside makeCounter function.
- The closure captures count and keeps it alive even after the function returns.
- That’s why calling counter() still increments the same count.

#### How do closures cause memory leaks
Retain cycle happens when two refrence object point to each other in strong manner, in closure when we use object inside closure in strong way it hold it that can create the retain cycle, to avoid this we can use the weak and unowned refernce of self.

```swift
class ViewController: UIViewController {
    var name = "Profile Screen"
    
    func loadData() {
        fetchData {
            return "Loaded for \(self.name)"
        }
    }
    
    func fetchData(completion: @escaping () -> String) {
        let result = completion()
        print(result)
    }
    
    deinit {
        print("ViewController deallocated")
    }
}
```
- Here, the closure captures self (the view controller). If self also holds a reference to the closure, boom → retain cycle → memory leak.
- To avoid the memroy leaks we can use self as weak or unowned.

#### Why do I need [weak self] inside closures?

- To avoid the memory leaks
- Use [weak self] or [unowned self] to break cycles.
- Use weak, unless you’re 100% sure self won’t be nil

```swift
// weak
- weak means the closure doesn’t own self.
- If self is gone, it becomes nil

fetchData { [weak self] in
    print("Loaded for \(self.name)")
}

// unowned
- unowned means no ownership, but never optional.
- Use when you’re sure self will outlive the closure.
- Using unowned incorrectly → crash if self is gone

fetchData { [unowned self] in
    print("Loaded for \(self.name)")
}
```
-------------------------------------------------------------------------------------------------------------------------------------
####  How closures are different than function? 
- function has name but closure does't 
- 


-------------------------------------------------------------------------------------------------------------------------------------
####  Why even we need closure what are the alternative ?
- yes, closure basically a mechanism to return call back, to alternative for this is to we can achieve through protocols, notificatoin center, etc.

-------------------------------------------------------------------------------------------------------------------------------------
####  What is trailing closures ?
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

####  What if a function has more than one argument of closures ?
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

## Stack vs Heap Memory

Stack memory is a sort of memory allocation that the OS continuously manages and uses to store local variables in a LIFO order. On the other hand, heap memory is a type of dynamic memory allocation used for storing objects and data structures that require a longer lifespan than stack memory.

#### Heap

- Heap is dynamic memory allocation and deallocation system for storing reference types
- Its allocate memory on run time.
- ARC manage the memory allocation and deallocation.
- Data types like **Arrays**, **Dictionaries**, **Sets**  or even large **Structures** store on heap especailly when its grow, and swift provide the copy on write behavirour for these data types.
- Closures and captured variables are also store on heap, especailly @escaping closures.
- Global Access: Heap memory can be accessed globally, making it suitable for storing data that needs to be accessed throughout the application

#### Stack

- Stack is static memory allocation system manage in LIFO fashion 
- Its faster then heap 
- Its allocate memory on compile time
- The stack keeps track of method calls and the corresponding execution flow
- All local variables of function are stored in stack.
- The size of stack is fixed and its determined on compile time.
  
| Stack Memory | Heap Memory |
|-------------|------------|
| Stores temporary data used by functions and procedures | Stores data not tied to a specific function or procedure |
| Follows LIFO (Last In First Out) structure | No specific structure |
| Automatically allocated and deallocated | Dynamically allocated and deallocated at runtime |
| Limited and fixed in size | Can grow and shrink dynamically |
| Easy to manage | More complex to manage |
| Faster access | Slower access compared to stack |
| Used for small, short-lived data | Used for large, complex data structures |
| Typically used by `structs` | Typically used by `classes` |

## UIKit Lifecycle

## GCD

## Async/Await

## Thread Safety

## Networking

## Persistence

## Testing

## Security

## Modularization
