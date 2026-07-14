# **Mysz Standard Library (stdlib)**

Welcome to the official standard library for the Mysz programming language. This library provides essential building blocks, fundamental data structures, memory management primitives, basic input/output utilities, and panic handling capabilities.


> **Note**
> This project requires [mysz-lang/mysz-runtime](https://github.com/mysz-lang/mysz-runtime) to work. Please ensure it is available when using mysz-std

> **Note**
> This project is a work in progress. Function names, struct field names, and other API naming may change between updates.

## **Modules Overview**

The standard library consists of the following files:

| Module | File | Description |
|--------|------|-------------|
| **vec** | vec.mysz | Generic, dynamically growing vector types |
| **collections** | collections.mysz | Hash map and hash set implementations |
| **hash** | hash.mysz | FNV-1a hashing function for any type |
| **io** | io.mysz | Console output formatting for primitive types |
| **memory** | memory.mysz | Core heap allocation and manual memory management |
| **panic** | panic.mysz | Runtime error reporting and application termination |
| **string** | string.mysz | Low-level string manipulation and managed structs |

## **Module Reference**

### **1. Vector (vec.mysz)**

Provides a generic, resizable contiguous vector container (`Vec<T>`).

**Structure:**

```mysz
struct Vec<T> {
    length: uint,
    capacity: uint,
    elementSize: uint,
    data: ptr<T>,
};
```

**Functions:**

| Function | Description |
|----------|-------------|
| `Vec_init<T>(arr: ptr<Vec<T>>)` | Initializes an array, setting length/capacity to 0 and data to NULL |
| `Vec_reserve<T>(arr: ptr<Vec<T>>, minCapacity: uint)` | Ensures capacity for at least `minCapacity` elements, growing if needed |
| `Vec_push<T>(arr: ptr<Vec<T>>, element: ptr<T>)` | Appends an element (via pointer) to the end of the array |
| `Vec_destroy<T>(arr: ptr<Vec<T>>)` | Frees the underlying buffer from the heap |

**Example:**

```mysz
use src::array;

var vec: Vec<int>;
Vec_init::<int>(&vec);
Vec_reserve::<int>(&vec, 10);

var value: int = 42;
Vec_push::<int>(&vec, &value);

Vec_destroy::<int>(&vec);
```

---

### **2. Collections (collections.mysz)**

Provides generic hash map and hash set implementations with open addressing and linear probing.

#### **2.1 HashMap (`HashMap<K, V>`)**

**Structure:**

```mysz
struct HashEntry<K, V> {
    key: K,
    value: V,
    is_occupied: bool,
};

struct HashMap<K, V> {
    buckets: Vec<HashEntry<K, V>>,
    length: uint,
};
```

**Functions:**

| Function | Description |
|----------|-------------|
| `HashMap_init<K, V>(map: ptr<HashMap<K, V>>)` | Initializes a new hash map with 16 buckets |
| `HashMap_insert<K, V>(map: ptr<HashMap<K, V>>, key: K, value: V)` | Inserts a key-value pair, updating if key already exists |
| `HashMap_get<K, V>(map: ptr<HashMap<K, V>>, key: K): V` | Retrieves the value for a key, panics if not found |
| `HashMap_remove<K, V>(map: ptr<HashMap<K, V>>, key: K)` | Removes a key-value pair if it exists |

**Example:**

```mysz
use src::collections;

var map: HashMap<int, str>;
HashMap_init::<int, str>(&map);
HashMap_insert::<int, str>(&map, 1, "one");
var value = HashMap_get::<int, str>(&map, 1);
```

#### **2.2 HashSet (`HashSet<K>`)**

**Structure:**

```mysz
struct HashSetEntry<K> {
    key: K,
    is_occupied: bool,
};

struct HashSet<K> {
    buckets: Vec<HashSetEntry<K>>,
    length: uint,
};
```

**Functions:**

| Function | Description |
|----------|-------------|
| `HashSet_init<K>(set: ptr<HashSet<K>>)` | Initializes a new hash set with 16 buckets |
| `HashSet_insert<K>(set: ptr<HashSet<K>>, key: K)` | Inserts a key if not already present |
| `HashSet_contains<K>(set: ptr<HashSet<K>>, key: K): bool` | Returns true if the key exists in the set |
| `HashSet_remove<K>(set: ptr<HashSet<K>>, key: K)` | Removes a key if it exists |

**Example:**

```mysz
use src::collections;

var set: HashSet<int>;
HashSet_init::<int>(&set);
HashSet_insert::<int>(&set, 42);
if (HashSet_contains::<int>(&set, 42)) {
    str_print("Found 42!\n");
};
```

---

### **3. Hashing (hash.mysz)**

Provides a single FNV-1a hash function that works with any type.

**Functions:**

| Function | Description |
|----------|-------------|
| `hash_bytes<T>(key: ptr<T>): uint` | Hashes any type into a 64-bit unsigned integer using FNV-1a |

**Example:**

```mysz
use src::hash;

var x: int = 42;
var h: uint = hash_bytes::<int>(&x);
```

---

### **4. Input / Output (io.mysz)**

Basic functions to display values on the standard output console.

**Functions:**

| Function | Description |
|----------|-------------|
| `str_print(val: str)` | Prints a raw string literal |
| `String_print(val: ptr<String>)` | Prints a managed `String` |
| `int_print(val: int)` | Prints a signed 64-bit integer |
| `uint_print(val: uint)` | Prints an unsigned 64-bit integer |
| `bool_print(val: bool)` | Prints `true` or `false` |
| `char_print(val: char)` | Prints a single character |
| `ptr_print(val: ptr<void>)` | Prints a pointer address in hexadecimal |

**Example:**

```mysz
use src::io;

str_print("Hello, ");
int_print(42);
char_print('\n');
```

---

### **5. Memory Management (memory.mysz)**

Low-level operations for allocating, resizing, and freeing heap memory.

**Functions:**

| Function | Description |
|----------|-------------|
| `malloc<T>(size: uint): ptr<T>` | Allocates `size` bytes of raw memory on the heap |
| `calloc<T>(count: uint, size: uint): ptr<T>` | Allocates and zero-initializes memory for `count` elements |
| `realloc<T>(val: ptr<T>, size: uint): ptr<T>` | Resizes an active heap allocation to a new size |
| `mfree<T>(val: ptr<T>)` | Frees an active heap allocation back to the system |

**Example:**

```mysz
use src::memory;

var data = malloc::<int>(16 * sizeof(int));
mfree::<int>(data);
```

---

### **6. Panic Handling (panic.mysz)**

Enables clean crash reporting and intentional runtime termination.

**Functions:**

| Function | Description |
|----------|-------------|
| `mysz_panic(message: str)` | Halts program execution immediately and prints a critical crash message |

**Example:**

```mysz
use src::panic;

if (something_went_wrong) {
    mysz_panic("Something went terribly wrong!");
};
```

---

### **7. Strings (string.mysz)**

Provides structures and functions to create, destroy, and compare managed string objects.

**Structure:**

```mysz
struct String {
    length: int,
    data: ptr<char>,
};
```

**Functions:**

| Function | Description |
|----------|-------------|
| `String_new(val: str): String` | Allocates and initializes a new `String` from a raw string literal |
| `String_destroy(val: ptr<String>)` | Frees the allocated memory occupied by a `String` |
| `String_compare(a: ptr<String>, b: ptr<String>): int` | Compares two `String` objects, returning 0 if equal |

**Example:**

```mysz
use src::string;

var s = String_new("Hello, World!");
String_destroy(&s);
```

---

## **Examples**

### **Using Vectors**

```mysz
use src::vec;

var vec: Vec<int>;
Vec_init::<int>(&vec);
Vec_reserve::<int>(&vec, 10);

var value: int = 42;
Vec_push::<int>(&vec, &value);

Vec_destroy::<int>(&vec);
```

### **Using Hash Maps**

```mysz
use src::collections;

var map: HashMap<int, str>;
HashMap_init::<int, str>(&map);
HashMap_insert::<int, str>(&map, 1, "one");

var result = HashMap_get::<int, str>(&map, 1);
```

### **Using Hash Sets**

```mysz
use src::collections;

var set: HashSet<int>;
HashSet_init::<int>(&set);
HashSet_insert::<int>(&set, 42);

if (HashSet_contains::<int>(&set, 42)) {
    str_print("Found!\n");
};
```