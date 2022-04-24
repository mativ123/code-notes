# intro
---
Sometimes you have to do something where you have to use a function. For example when using a [[standard library algorithms|algorithm]] like [[standard library algorithms#std find_if|std::find_if]]. If this is a very simple operation it wouldn't be worth it to create an entire function. With lambdas you can create functions ***inside*** other functions.
# syntax
---
a lambda definition looks like this:
```cpp
[ captureClause ] ( parameters ) -> returnType
{
    statements;
}
```
capture clauses are explained in this [[#captures|section]].
you actually don't need to add any parameters or return type so it could also look like this:
```cpp
[]() {};
```
If return-type is omitted the return-type will automatically deducted which requires there only being one type of variable being returned in the function. For example this wouldn't work:
```cpp
#include <iostream>

int main()
{
  auto divide{ [](int x, int y, bool bInteger) { // note: no specified return type
    if (bInteger)
      return x / y;
    else
      return static_cast<double>(x) / y; // ERROR: return type doesn't match previous return type
  } };

  std::cout << divide(3, 2, true) << '\n';
  std::cout << divide(3, 2, false) << '\n';

  return 0;
}
```
because the lambda `divide` returns both a int and a double.
# simple example
---
```cpp
#include <algorithm>
#include <array>
#include <iostream>
#include <string_view>

int main()
{
  constexpr std::array<std::string_view, 4> arr{ "apple", "banana", "walnut", "lemon" };

  // Define the function right where we use it.
  const auto found{ std::find_if(arr.begin(), arr.end(),
                           [](std::string_view str) // here's our lambda, no capture clause
                           {
                             return (str.find("nut") != std::string_view::npos);
                           }) };

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

# types
---
You can store a lambda as a variable. For example like this:
```cpp
// Good: Instead, we can store the lambda in a named variable and pass it to the function.
auto isEven{
  [](int i)
  {
    return ((i % 2) == 0);
  }
};

return std::all_of(array.begin(), array.end(), isEven);
```
lambdas doens't have a type. A lambda initialized with `auto` is called a *generic lambdas*. You can always use different types like you normally would with functions.
# Standard library function objects
---
the header `functional` comes with several basic callable objects like for example [[standard library algorithms#extra|std::greater]]
# captures
---
Inside lambdas you can only use variables that is in the global scope or is known at compile time. Therefore we use the capture clause to give the lambda access to certain variables. Here is an example:
```cpp
#include <algorithm>
#include <array>
#include <iostream>
#include <string_view>
#include <string>

int main()
{
  std::array<std::string_view, 4> arr{ "apple", "banana", "walnut", "lemon" };

  std::cout << "search for: ";

  std::string search{};
  std::cin >> search;

  // Capture @search                                vvvvvv
  auto found{ std::find_if(arr.begin(), arr.end(), [search](std::string_view str) {
    return (str.find(search) != std::string_view::npos);
  }) };

  if (found == arr.end())
  {
    std::cout << "Not found\n";
  }
  else
  {
    std::cout << "Found " << *found << '\n';
  }

  return 0;
}
```
## mutable capture
Captures are a `const` copy of the original variable. If you want to change the variable you need to mark the lambda as `mutable` like so:
```cpp
#include <iostream>

int main()
{
  int ammo{ 10 };

  auto shoot{
    // Added mutable after the parameter list.
    [ammo]() mutable {
      // We're allowed to modify ammo now
      --ammo;

      std::cout << "Pew! " << ammo << " shot(s) left.\n";
    }
  };

  shoot();
  shoot();

  std::cout << ammo << " shot(s) left\n";

  return 0;
}
```
**Output:**
>Pew! 9 shot(s) left.
Pew! 8 shot(s) left.
10 shot(s) left

**<p style="color: red;">Warning!</p>**
Because the lambda is a variable values inside it is consistent in every call of the lambda as you can see in the example earlier. 
## capture by reference
Because the capture is a copy the lambda can't actually change the actual variable. except if you add a ampersand[^1]. This is called *capturing by reference*. Example:
```cpp
#include <iostream>

int main()
{
  int ammo{ 10 };

  auto shoot{
    // We don't need mutable anymore
    [&ammo]() { // &ammo means ammo is captured by reference
      // Changes to ammo will affect main's ammo
      --ammo;

      std::cout << "Pew! " << ammo << " shot(s) left.\n";
    }
  };

  shoot();

  std::cout << ammo << " shot(s) left\n";

  return 0;
}
```
**Output:**
>Pew! 9 shot(s) left.
9 shot(s) left

[^1]: what the sign *&* is called

## default captures
If you capture many variables it would be annoying to manually type in each capture and you could accidentally miss some. It would therefore be better to use *default captures* which are a list which are automatically made by:
* adding a `=` sign inside the capture clause
* adding a ampersand[^1] inside the capture clause to make it capture references.
Example:
```cpp
#include <array>
#include <iostream>

int main()
{
  std::array areas{ 100, 25, 121, 40, 56 };

  int width{};
  int height{};

  std::cout << "Enter width and height: ";
  std::cin >> width >> height;

  auto found{ std::find_if(areas.begin(), areas.end(),
                           [=](int knownArea) { // will default capture width and height by value
                             return (width * height == knownArea); // because they're mentioned here
                           }) };

  if (found == areas.end())
  {
    std::cout << "I don't know this area :(\n";
  }
  else
  {
    std::cout << "Area found :)\n";
  }

  return 0;
}
```
You can still manually add captures like you normally would by separating with comma.
## defining new variables
We can define new variables for the lambda by defining a variable inside the capture clause without specifying the type.
Note: if the lambda is [[#mutable capture|mutable]] this variable will also not be a `const`.
# extra
---
Lambdas are not actually functions but are actually something called <u>functors</u> which are objects that can be called functions.