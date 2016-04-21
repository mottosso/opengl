### Building a Dynamic Library

> Source: [Albertech Blog](http://albertech.blogspot.co.uk/2014/12/how-to-compile-dll-using-clexe.html)

Here's a minimal source and command line for building a Windows dynamic link library, or DLL. I like to use it as a quick template for larger projects, without all the noise you get by creating a similar project in Visual Studio.

**helloworld.cpp**

```cpp
#include <stdio.h>
#include <wchar.h>

extern "C" {

__declspec(dllexport)
void print_hello(const wchar_t* u) {
    wprintf(L"hello: %s\n", u);
}

} // extern "C"
```

This is very trivial as you can see. It has a single function that expects an array of wide chars, which it outputs.
A couple notes on the source.

`extern "C" {}` informs the compiler that we want the function names to be preserved. That is, to not ["mangle"](https://en.wikipedia.org/wiki/Name_mangling#Simple_example) the names as is done for C++ code.

This way, when we do..

```cmd
dumpbin.exe /exports helloworld.dll
```

..we will see and can call the function name as we typed it:

```cpp
          1    0 00001000 print_helo
```

Also, we use `__declspec(dllexport)` so we do not need to use a `.def` file to export the function.

<br>
<br>
<br>

### Compile

```cmd
cl.exe /D_USRDLL /D_WINDLL helloworld.cpp /MT /link /DLL /OUT:helloworld.dll
```

More details about build settings for a DLL can be found [here](http://msdn.microsoft.com/en-us/library/aa235516%28v=vs.60%29.aspx).

<br>
<br>
<br>

### Bonus Round

And here is how you'd call `print_hello()` from Python. (What's the use of a DLL unless you have some way of using it, am I right?) Pretend we have a file `helloworld.py` in the same directory as our compiled `helloworld.dll`:

```python
import ctypes

lib = ctypes.cdll.LoadLibrary('helloworld.dll')

# Our 'ctypes' wrapper around the DLL function -- this is where we
# convert Python types to C types and call the DLL function.
def print_hello(w):
    func = lib.print_hello
    func.argtypes = [ctypes.c_wchar_p]
    p = ctypes.c_wchar_p(w)
    func(p, len(w))

print_hello('my name is ...')

```