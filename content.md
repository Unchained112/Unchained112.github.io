# C++ and Algorithms for Interview

### Small points

- `x++` increments the value of variable `x` **after** processing the current statement. `x+=i++;` add `i` to `x` then increase `i` by 1. 
- `++x` increments the value of `x` **before** processing the current statement. `x+=++i;` increase `i` and add `i+1` to `x`.
- Array is passed by reference. `array = &array[0]`.
- Reference must be initialized using a variable of the same type.
- Enumerate example:
```
enum Suit_t {A, B, C, D};
const string Name[] = {"A", "B", "C", "D"};
enum Suit_t x = C;
std::cout << Name[x] << std::endl;
```

### Function pointer

For functions that have precisely the same type signature, a function pointer allow us to call them the same way from a syntactic point of view.

Example:

```
int min(int a, int b);
// EFFECTS: returns the smaller of a and b.

int max(int a, int b);
// EFFECTS: returns the larger of a and b.

int (*foo)(int, int);

// Then we can let foo = min or max and call
foo(5, 3); //for example
```

### Memory
 - Memory leak is caused when not recycling an allocated dynamic object.
 - Check memory leak: `valgrind --leak-check=full ./program <args>`
 - The space for objects created via `new` comes from heap.
 - Stack is for function calls.
 - ![Memory segmentation](./images/virtual-memory.jpg)

### Struct and class
 - The default copy constructor is shallow copy, which works when there is not points in the member of that data type.  
 
 - The sizeof for a struct is not always equal to the sum of sizeof of each individual member. This is because of the padding added by the compiler to avoid alignment issues. Padding is only added when a structure member is followed by a member with a larger size or at the end of the structure.
 
    - Different compilers might have different alignment constraints as C standards state that alignment of structure totally depends on the implementation.
    - Example: (output: "Size of struct 24")
```
int main(){
    struct A {
        // sizeof(int) = 4
        int x;
        // Padding of 4 bytes
  
        // sizeof(double) = 8
        double z;
  
        // sizeof(short int) = 2
        short int y;
        // Padding of 6 bytes
    };
  
    printf("Size of struct: %ld", sizeof(struct A));
  
    return 0;
}
```
![Size of struct](./images/struct_sizeof_ep.png)

  - The size of the class is similar to the struct with little difference.
    - Empty class has size 1 (byte).
    - The virtual function pointer has size of 4 bytes.
    - The size of sub-class is equals to the size of all members plus the size of its parent class. (Note that they share the virtual function pointers) 
  - Notice on subclass method:
```
PosIntSet s; // sub class instance of class Intset
IntSet *p = s; // this points precisely to s
IntSet &r = s; // r is declared to be “reference to an IntSet
// Apparent type: the declared type of the reference. (IntSet)
// Actual type: the real type of the referent. (PosIntSet)
// Actual type: the real type of the referent. (PosIntSet)
```
 - Virtual function and virtual table
 - ![Vatble](./images/vtable.png)
 - Note that the constructor function cannot be a virtual function since when instantiating an object, it allocates no memory for that and then there would be no virtual table.

### Standard Template Library (STL)



### C++ 11 new features

### Reference
  1. Problem Solving with C++ (8th Edition), by Walter Savitch, Addison Wesley Publishing (2011)
  2. https://www.geeksforgeeks.org/
 
