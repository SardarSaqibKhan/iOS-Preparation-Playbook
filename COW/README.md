
## Copy On Write (COW)

- Copy-on-write (COW) is a technique used to optimize memory usage.
- Value types like arrays, sets, dictornaires default use COW when assigning to another variable.
- It does't create duplicate copy instantly on assigning, until we mutation happens.
- It saved operation cost on assigning value type to another variable/object
- When share data get modified then it will create the seperate copy.

#### Advantages:

- Copy-on-Write (COW) helps avoid unnecessary memory copying
- Arrays, dictionaries, and sets in Swift delay copying until modification.
- COW improves performance during assignments
- Saves processing cost for large data structures
- Making assignment operations very fast O(1) without this it will be O(n)

```swift
func print(address o: UnsafeRawPointer ) {
    print(String(format: "%p", Int(bitPattern: o)))
}

var array1: [Int] = [0, 1, 2, 3]
var array2 = array1

//Print with just assign
print(address: array1) //0x600000078de0
print(address: array2) //0x600000078de0

//Let's mutate array2 to see what's
array2.append(4)

print(address: array2) //0x6000000aa100
```
