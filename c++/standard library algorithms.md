# intro
---
standard library algorithms are algorithms that live in the `algorithms` library. They can be used on tasks like finding values in arrays or sorting them. They are good to use because they are much faster to write and there is a significantly smaller chance of making dumb errors.
# types
---
## `std::find`
`std::find` finds the first occurrence of a value in a container. it takes 3 parameters:
1. an [[iterators|iterator]] to the starting element in the container.
2. an [[iterators|iterator]] to the ending element in the container.
3. a value to search for.
it returns an iterator pointing to the element *(if it is found)* or the end of the container *(if the element is not found)*.
### example
```cpp
#include <algorithm>
#include <array>
#include <iostream>

int main()
{
    std::array arr{ 13, 90, 99, 5, 40, 80 };

    std::cout << "Enter a value to search for and replace with: ";
    int search{};
    int replace{};
    std::cin >> search >> replace;

    // Input validation omitted

    // std::find returns an iterator pointing to the found element (or the end of the container)
    // we'll store it in a variable, using type inference to deduce the type of
    // the iterator (since we don't care)
    auto found{ std::find(arr.begin(), arr.end(), search) };

    // Algorithms that don't find what they were looking for return the end iterator.
    // We can access it by using the end() member function.
    if (found == arr.end())
    {
        std::cout << "Could not find " << search << '\n';
    }
    else
    {
        // Override the found element.
        *found = replace;
    }

    for (int i : arr)
    {
        std::cout << i << ' ';
    }

    std::cout << '\n';

    return 0;
}
```
**Sample run when the element is found:**
>Enter a value to search for and replace with: 5 234
13 90 99 234 40 80

**Sample run when the element isn’t found:**
>Enter a value to search for and replace with: 0 234
Could not find 0
13 90 99 5 40 80
## `std::find_if`
`std::find_if` iterates trough container and finds a value that matches some condition. it works similarly to `std::find` but instead of a value it takes a callable object like a function or a pointer.
### example
```cpp
#include <algorithm>
#include <array>
#include <iostream>
#include <string_view>

// Our function will return true if the element matches
bool containsNut(std::string_view str)
{
    // std::string_view::find returns std::string_view::npos if it doesn't find
    // the substring. Otherwise it returns the index where the substring occurs
    // in str.
    return (str.find("nut") != std::string_view::npos);
	//explanation of std::stringview to be added
}

int main()
{
    std::array<std::string_view, 4> arr{ "apple", "banana", "walnut", "lemon" };

    // Scan our array to see if any elements contain the "nut" substring
    auto found{ std::find_if(arr.begin(), arr.end(), containsNut) };

    if (found == arr.end())
    {
        std::cout << "No nuts\n";
    }
    else
    {
        std::cout << "Found " << *found << '\n';
    }

    return 0;
}
```
**Output:**
>Found walnut
## `std::count` and `std::count_if`
works exactly the same as [[#std find|std::find]] and [[#std find_if|std::find_if]] but instead of return the value it finds it returns how many occurrences there was.
### example
```cpp
#include <algorithm>
#include <array>
#include <iostream>
#include <string_view>

bool containsNut(std::string_view str)
{
	return (str.find("nut") != std::string_view::npos);
}

int main()
{
	std::array<std::string_view, 5> arr{ "apple", "banana", "walnut", "lemon", "peanut" };

	auto nuts{ std::count_if(arr.begin(), arr.end(), containsNut) };

	std::cout << "Counted " << nuts << " nut(s)\n";

	return 0;
}
```
**Output:**

>Counted 2 nut(s)

## `std::sort`
You can use `std::sort`  to sort in ascending or descending order, but you can add a function as a third parameter to sort in any order you like.

### example
```cpp
#include <algorithm>
#include <array>
#include <iostream>

bool greater(int a, int b)
{
    // Order @a before @b if @a is greater than @b.
    return (a > b);
}

int main()
{
    std::array arr{ 13, 90, 99, 5, 40, 80 };

    // Pass greater to std::sort
    std::sort(arr.begin(), arr.end(), greater);

    for (int i : arr)
    {
        std::cout << i << ' ';
    }

    std::cout << '\n';

    return 0;
}
```
**Output:**
>99 90 80 40 13 5
### extra
Instead of writing your own function for `std::sort` you can use `std::greater` which is part of the `functional` header.
#### example
```cpp
std::sort(arr.begin(), arr.end(), std::greater{}); // use the standard library greater comparison
// Before C++17, we had to specify the element type when we create std::greater
std::sort(arr.begin(), arr.end(), std::greater<int>{}); // use the standard library greater comparison
```
## `std::for_each`
`std::for_each` takes a sequence of values in a container and applies a specifies a function to each of them. Even though `std::for_each` might be longer then a simple [[iterators#range-based for loops|range-based for loop]] there are benefits to it. For example there is less room for mistakes like:
* implicit conversion
* forget the ampersand[^2] and it would have no effect

[^2]:what the *&* sign is called

you can also skip the first item with `std::next` like so:
```cpp
std::for_each(std::next(arr.begin()), arr.end(), doubleNumber);
```

### example
```cpp
#include <algorithm>
#include <array>
#include <iostream>

void doubleNumber(int& i)
{
    i *= 2;
}

int main()
{
    std::array arr{ 1, 2, 3, 4 };

    std::for_each(arr.begin(), arr.end(), doubleNumber);

    for (int i : arr)
    {
        std::cout << i << ' ';
    }

    std::cout << '\n';

    return 0;
}
```
**Output:**
>2 4 6 8

# order of execution
---
something to remember is that not all algorithms ensure a certain order of execution. the only one that does is:
* [[#std for_each]]
* `std::copy`
* `std::copy_backward`
* `std::move`
* `std::move_backward`
which all ensures sequential order of execution.
# extra
---
this isn't all of the algorithms. you can find the rest here: [algorithms library](https://en.cppreference.com/w/cpp/algorithm)

[[lambdas|Lambdas]] are perfect to use for the algorithms that uses functions because the required functions will often be very simple.