# Maps and Sets

Let's dive into the common methods for `Map` and `Set` and how they differentiate from `Array` and `Object`.

-----

## Map Methods

`Map` provides specific methods to handle its key-value pairs efficiently.

### Common `Map` Methods

* `new Map()`: **Creates** an empty Map.
* `map.set(key, value)`: **Adds** a new key-value pair or **updates** the value if the key already exists.
* `map.get(key)`: **Retrieves** the value associated with the `key`. Returns `undefined` if the key isn't found.
* `map.has(key)`: **Checks** if a `key` exists in the Map. Returns `true` or `false`.
* `map.delete(key)`: **Removes** a key-value pair by its `key`. Returns `true` if the element was successfully removed, `false` otherwise.
* `map.clear()`: **Removes** all key-value pairs from the Map.
* `map.size`: A **property** (not a method) that returns the number of key-value pairs in the Map.
* `map.keys()`: Returns an **iterator** of the Map's keys.
* `map.values()`: Returns an **iterator** of the Map's values.
* `map.entries()`: Returns an **iterator** of `[key, value]` pairs.
* `map.forEach(callbackFn)`: **Executes** a provided function once for each key-value pair.

<!-- end list -->

```javascript
const myMap = new Map();
myMap.set('name', 'Alice');
myMap.set('age', 30);

console.log(myMap.get('name')); // Alice
console.log(myMap.has('age'));  // true
myMap.delete('age');
console.log(myMap.size);      // 1
myMap.forEach((value, key) => console.log(`${key}: ${value}`)); // name: Alice
```

-----

## Set Methods

`Set` provides methods to manage its collection of unique values.

### Common `Set` Methods

* `new Set()`: **Creates** an empty Set.
* `set.add(value)`: **Adds** a `value` to the Set. If the value already exists, nothing happens.
* `set.has(value)`: **Checks** if a `value` exists in the Set. Returns `true` or `false`.
* `set.delete(value)`: **Removes** a `value` from the Set. Returns `true` if the element was successfully removed, `false` otherwise.
* `set.clear()`: **Removes** all values from the Set.
* `set.size`: A **property** (not a method) that returns the number of unique values in the Set.
* `set.keys()`: Returns an **iterator** of the Set's values (for compatibility with Map, though values are both keys and values in a Set).
* `set.values()`: Returns an **iterator** of the Set's values.
* `set.entries()`: Returns an **iterator** of `[value, value]` pairs (for compatibility, both key and value are the same in a Set).
* `set.forEach(callbackFn)`: **Executes** a provided function once for each value.

<!-- end list -->

```javascript
const mySet = new Set();
mySet.add(1);
mySet.add(5);

console.log(mySet.has(1));    // true
mySet.delete(5);
console.log(mySet.size);    // 1
mySet.forEach(value => console.log(value)); // 1
```

-----

## How They Differ from Array and Object in Brief

| Feature       | Map                                                    | Set                                             | Object                                                                 | Array                                                                  |
| :------------ | :----------------------------------------------------- | :---------------------------------------------- | :--------------------------------------------------------------------- | :--------------------------------------------------------------------- |
| **Data Type** | Collection of key-value pairs                          | Collection of unique values                     | Collection of key-value pairs                                          | Ordered collection of values                                           |
| **Keys** | Can be **any data type** (objects, numbers, strings, etc.) | Values are the "keys" (unique)                  | **Strings** (or Symbols) only                                          | Integer indices (0, 1, 2, ...)                                         |
| **Uniqueness**| Keys are unique                                        | **Values are unique** | Keys are unique                                                        | Values can be duplicated                                               |
| **Order** | **Maintains insertion order** | **Maintains insertion order** | No guaranteed order (historically, now more predictable but not strict) | **Guaranteed order** (by index)                                        |
| **Size** | `map.size` property                                    | `set.size` property                             | Must be calculated (e.g., `Object.keys(obj).length`)                   | `array.length` property                                                |
| **Iteration** | Directly iterable (`for...of`), `forEach`              | Directly iterable (`for...of`), `forEach`       | Indirectly iterable (`for...in`, `Object.keys()`), no direct `forEach` | Directly iterable (`for...of`), `forEach`, `map`, `filter`, `reduce` |
| **Use Case** | Storing data where diverse keys are needed, or order matters. | Storing unique items, removing duplicates.      | Storing structured data with known string keys.                        | Storing ordered lists of items, good for collections of similar data.  |

**In essence:**

* **`Map` vs. `Object`**: `Map` is more flexible with key types, guarantees insertion order, and is generally better for frequently adding/removing key-value pairs or when non-string keys are needed. Objects are simpler for static, known string-keyed data.
* **`Set` vs. `Array`**: `Set` enforces uniqueness of values, making it ideal for maintaining a collection of distinct items or for easily filtering duplicates from an existing array. Arrays are for ordered collections where duplicates are allowed and indices are important.
