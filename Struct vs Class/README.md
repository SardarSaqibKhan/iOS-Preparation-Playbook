## Struct vs Class

Structs and classes in Swift are types used to group related data and behavior together into a signle unit.

#### Struct
A struct is a value type used to group related data and behavior together into a single unit.

- Structs are value types.
- Its store on stack and automatically manage by system, on the bases of LIFO order.
- Structs are faster than class because its stored on stack and each thread has its own stack while there is only one heap for all app, so fetching is cheaper.
- Data types stucts, touples, enum are value types.
- By default its thread safe, because each object has its own copy of data so there is no race condition.

#### Class
A class is a reference type used to define objects that can share and mutate a single instance across multiple references.

- Classes are refernce types.
- Its store on heap memory, ARC manage the allocation and deallocation of class object.
- Classes are slower than stuct
- Class, Actor, closures are refernce type.
- By default classes are not thread safe, developer have to manage the thread safty using operation queue or GCD or alternatively using Actors.

 #### Differences: 
| Struct      | Class      |
|-------------|------------|
| Value types | Refernce types |
| Store on stack | Store on heap |
| Automatically allocated and deallocated | ARC manage allocated and deallocated|
| No Inheritance | Allow Inheritance |
| Initializing is't required | Initializing is requried |
| Faster access | Slower access compared to stack |
| Has no deinit | Has deinit |
| Thread safe | By default not thread safe |
| Can't typecaset | Can by typecast (down cast and upcast) |

#### Resemblances:
 - Both represent data models in Swift
 - Both can implement methods and custom initializer
 - Both can implement one or more protocols
 - They can be both extended in their types(Swift extensions)
 - Both can use access control (like public, private).


