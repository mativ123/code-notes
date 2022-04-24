# intro
---
an object designed to access elements in a container *(arrays, lists, std::vectors, e.t.c.)*. They *<u>Iterate</u>* trough a sequence of values and provides access to each one along the way. It is very consistent.
# types
---
## pointer as iterator
it is the simplest one. It works by having to variables. one for the beginning of the list *(which actually points to the point one after the last element)* and one for the end. it then *<u>iterates</u>* trough each element between the two points and the current element can be accessed by indirection *( \* )* through the pointer.
### example
```cpp
#include <array>
#include <iostream>

int main()
{
    std::array data{ 0, 1, 2, 3, 4, 5, 6 };

    auto begin{ &data[0] };
    // note that this points to one spot beyond the last element
    auto end{ begin + std::size(data) };

    // for-loop with pointer
    for (auto ptr{ begin }; ptr != end; ++ptr) // ++ to move to next element
    {
        std::cout << *ptr << ' '; // Indirection to get value of current element
    }
    std::cout << '\n';

    return 0;
}
```
## Standard library iterators
because iterators are so common there is functions for the beginning **and** the **end** *(from [[#pointer as iterator]])*: `std::begin()` and `std::end()` *(or `begin()` and `end()` from the `array` header)* from the `iterator` header.
### example
```cpp
#include <array>
#include <iostream>
#include <iterator> // For std::begin and std::end

int main()
{
    std::array array{ 1, 2, 3 };

    // Use std::begin and std::end to get the begin and end points.
    auto begin{ std::begin(array) };
    auto end{ std::end(array) };

    for (auto p{ begin }; p != end; ++p) // ++ to move to next element
    {
        std::cout << *p << ' '; // Indirection to get value of current element
    }
    std::cout << '\n';

    return 0;
}
```

# range-based for loops
---
range-based for loops work by automatically using [[#Standard library iterators]] and therefore does only work with types that support those. For example dynamic arrays doesn't work because it has no end.
## example
```cpp
#include <array>
#include <iostream>

int main()
{
    std::array array{ 1, 2, 3 };

    // This does exactly the same as the loop we used before.
    for (int i : array)
    {
        std::cout << i << ' ';
    }
    std::cout << '\n';

    return 0;
}
```