# **Mysz Standard Library (stdlib)**

Welcome to the official standard library for the Mysz programming language. This library provides essential building blocks, fundamental data structures, memory management primitives, basic input/output utilities, and panic handling capabilities.


> **Note**
> This project requires [mysz-lang/mysz-runtime](https://github.com/mysz-lang/mysz-runtime) to work. Please ensure it is available when using mysz-std

> **Note**
> This project is a work in progress. Function names, struct field names, and other API naming may change between updates.

## **Modules Overview**

The standard library consists of the following files:

* **string.mysz**: Low-level string manipulation and managed struct wrappers.  
* **array.mysz**: Generic, dynamically growing vector/array types.  
* **memory.mysz**: Core heap allocation and manual memory management primitives.  
* **io.mysz**: Basic console output formatting for primitive types and strings.  
* **panic.mysz**: Runtime error reporting and application termination.

## **Module Reference**

### **1\. Strings (string.mysz)**

Provides structures and functions to create, destroy, and compare managed string objects.

* **Structure**:
```mysz
    struct MyszString {  
        length: int,  
        data: ptr\<char\>,  
    };
```

* **Functions**:  
  * mysz\_string\_new(val: str): MyszString  
    * Allocates and initializes a new MyszString from a raw string literal.  
  * mysz\_string\_destroy(val: ptr\<MyszString\>)  
    * Frees the allocated memory occupied by a MyszString.  
  * mysz\_string\_compare(a: ptr\<MyszString\>, b: ptr\<MyszString\>)  
    * Compares the lengths and contents of two distinct MyszString objects.

### **2\. Arrays (array.mysz)**

Provides a generic, resizable contiguous array container (MyszArray\<T\>).

* **Structure**:
```mysz
    struct MyszArray\<T\> {  
        length: int,  
        capacity: int,  
        elementSize: int,  
        data: ptr\<T\>,  
    };
```

* **Functions**:  
  * MyszArray\_init\<T\>(arr: ptr\<MyszArray\<T\>\>)  
    * **Public Helper**: Dynamically initializes the array, automatically calculating the element size using sizeof(T).  
  * mysz\_array\_init\<T\>(arr: ptr\<MyszArray\<T\>\>, elementSize: int)  
    * **Low-Level Initialization**: Manually initializes an array by specifying the explicit byte size of the type.  
  * mysz\_array\_reserve\<T\>(arr: ptr\<MyszArray\<T\>\>, minCapacity: int)  
    * Ensures the array has enough space for at least minCapacity elements, growing the underlying buffer if necessary.  
  * mysz\_array\_push\<T\>(arr: ptr\<MyszArray\<T\>\>, element: ptr\<T\>)  
    * Appends a new element (via pointer) to the end of the array.  
  * mysz\_array\_destroy\<T\>(arr: ptr\<MyszArray\<T\>\>)  
    * Frees all allocated elements and the underlying buffer from the heap.

### **3\. Memory Management (memory.mysz)**

Low-level operations for allocating, resizing, and freeing heap memory.

* **Functions**:  
  * mysz\_alloc\<T\>(size: int): ptr\<T\>  
    * Allocates size bytes of raw memory on the heap.  
  * mysz\_calloc\<T\>(count: int, size: int): ptr\<T\>  
    * Allocates heap memory for count elements of size bytes each, and automatically zero-initializes the allocated memory.  
  * mysz\_realloc\<T\>(val: ptr\<T\>, size: int)  
    * Resizes an active heap allocation to a new size.  
  * mysz\_free\<T\>(val: ptr\<T\>)  
    * Frees an active heap allocation back to the system.

### **4\. Input / Output (io.mysz)**

Basic functions to display values on the standard output console.

* **Functions**:  
  * print\_str(val: str)  
    * Prints a raw string literal.  
  * print\_String(val: ptr\<MyszString\>)  
    * Prints a managed MyszString.  
  * print\_int(val: int)  
    * Prints an integer value.  
  * print\_bool(val: bool)  
    * Prints a boolean value (true or false).  
  * print\_char(val: char)  
    * Prints a single character.

### **5\. Panic Handling (panic.mysz)**

Enables clean crash reporting and intentional runtime termination.

* **Functions**:  
  * mysz\_panic(message: str)  
    * Halts program execution immediately and prints a critical crash message to the screen.