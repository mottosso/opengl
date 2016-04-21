### Building a library

Starting from the bottom up, we'll build a library and then use that library in the next example.

We will have 3 files in this layout.

```
| Layout              | Description
|---------------------|----------------------------------
| root/               |
|   include/          |
|     lib.h           | 1. Definition of a sample function
|   src/              |
|     lib.cpp         | 2. Declaration of function
|     main.cpp        | 3. Use of library
|   build/            |
|     ...             | Output goes here
```

Here is the content of each file.

**lib.h**

```cpp
namespace mynamespace
{
    void helloWorld();
}
```

**lib.cpp**

```cpp
#include <iostream>
#include "lib.h"

namespace mynamespace
{
    void helloWorld()
    {
        std::cout << "Hello World!" << std::endl;
    }
}
```

**main.cpp**

```cpp
#include "lib.h"

int main(void)
{
    mynamespace::helloWorld();
    return(0);
}
```

<br>
<br>
<br>

#### Compile

Before compiling anything, you will need to setup your environment.

**term.bat**

```bat
set PATH=C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE;%PATH%
call "c:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\vcvarsall.bat" x86
cmd /K cls
```

> Exchange `12.0` with `14.0` for Visual Studio 2015.

**build-lib.bat**

This will output `build/lib.lib`.

```bat
@echo off
pushd build

cl^
  -Zi^
  -EHsc^
  -I "..\include"^
  -c^
  ..\src\lib.cpp &&^
lib^
  lib.obj

popd
```

**build.bat**

This will use `build/lib.lib` to compile a console application.

Notice the similarities between compilation, and differing second command; from `lib.exe` to `link.exe`.

```bat
@echo off
pushd build

cl^
  -Zi^
  -EHsc^
  -I "..\include"^
  -c^
  ..\src\main.cpp &&^
link main.obj^
  lib.lib^
  /LIBPATH:"..\build"

popd
```

<br>
<br>
<br>

### Running

Now that you are done, you can run your program.

```bat
> build\main.exe
Hello World!
```

This program is:

1. Calling a function defined in `lib.h`.
2. Which is defined in `lib.cpp`
3. And provided to our application via `lib.lib`

You will note that if you excluded `lib.lib` from your `build.bat` script, an error is thrown.

```bat
main.obj : error LNK2019: unresolved external symbol "void __cdecl mynamespace::hello
World(void)" (?helloWorld@mynamespace@@YAXXZ) referenced in function _main
main.exe : fatal error LNK1120: 1 unresolved externals
```