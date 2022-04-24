# intro
---
command line arguments are simply just arguments you can pass to a program when calling it from the terminal *(command prompt if you use windows)*.
# how to
---
To use command line arguments you have to add `int argc` and `char* argv[]` to `int main()` like so:
```cpp
int main(int argc, char* argv[])
```
you can also do `char** argv` but don't because you can't tell that `argv` isn't a array by just looking at it.
* `argc` is an int that holds how many arguments there is.
* `argv` is an array of c-style strings[^1] that holds the actual values

[^1]: definition of c-style string t.b.a.

Command line arguments are always passed as strings so you have to convert, like so:
```cpp
#include <iostream>
#include <sstream> // for std::stringstream
#include <string>

int main(int argc, char* argv[])
{
	if (argc <= 1)
	{
		// On some operating systems, argv[0] can end up as an empty string instead of the program's name.
		// We'll conditionalize our response on whether argv[0] is empty or not.
		if (argv[0])
			std::cout << "Usage: " << argv[0] << " <number>" << '\n';
		else
			std::cout << "Usage: <program name> <number>" << '\n';

		return 1;
	}

	std::stringstream convert{ argv[1] }; // set up a stringstream variable named convert, initialized with the input from argv[1]

	int myint{};
	if (!(convert >> myint)) // do the conversion
		myint = 0; // if conversion fails, set myint to a default value

	std::cout << "Got integer: " << myint << '\n';

	return 0;
}
```