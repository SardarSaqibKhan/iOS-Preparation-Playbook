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
- We may encountered with stack overflow in stack memory.
  
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
