# C++ Tips

## gcc五分钟系列

> Ref: <https://lexdene.gitbooks.io/gcc-five-minutes/content/>

* 声明和定义的本质区别是什么?
**声明没有为符号分配存储空间、定义会为符号分配存储空间。**

* 第十一节：查看汇编代码

    ```bash
    g++  -s  main.cpp
    ```


## Caution to define `inline` functionss in header files (`.h`)

`C++` does not allow for duplicated definition, that is why we should not define functions in header files (`.h`) -- since a header file could be included (`#include`) by multiple `.cpp` files, which will cause duplicated definition for the same funtion.

There is one exception for the inline function defined in header files. In such case, C++ will only keep one function and eliminate others, if the header file was included by mutliple `.cpp` files. H

However, the left inline function is very unlikely to be the correct one, hence it may cause problem.

Summary, do not define inline function in header files if the function will rely on context.

> Ref: <https://stackoverflow.com/questions/10876930/should-one-never-use-static-inline-function>