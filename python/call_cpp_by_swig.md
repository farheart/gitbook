# Python call C++ function by SWIG

This note shows how to use `SWIG` to enable **python** invoking `C++` codes, which might be wanted if high-efficiency is needed, such as create `cplex` model

## What is `SWIG`
`SWIG` is an interface compiler that connects programs written in C and C++ with scripting languages such as Perl, Python, Ruby, and Tcl. It works by taking the declarations found in C/C++ header files and using them to generate the wrapper code that scripting languages need to access the underlying C/C++ code. 
> 


## Install SWIG

> Ref: <https://blog.csdn.net/LIANGJIANGLI/article/details/72832154> (Mac Swig 3.0.12 安装)
* Download, Extract 
* `./configure --prefix=/Users/y0w02p1/wy/programs/swig // 指定安装目录`
    * 如果想让默认安装的话，可以直接执行 `./configure`
* `make  // compiling`
* `make install  // do install`
* Configurate path: `nano ~/.bash_profile`
    ```
    export SWIG_HOME=/Users/y0w02p1/wy/programs/swig
    export PATH=${PATH}:${SWIG_HOME}/bin
    ```
* Test: `$ swig -version` 显示版本号说明安装成功！

## Compile `.cpp` using `SWIG`


```bash
$ swig -python example.i
$ gcc -c example.c example_wrap.c -I/usr/local/anaconda3/envs/django/include/python3.6m
$ gcc -bundle -undefined dynamic_lookup -L/usr/local/anaconda3/envs/django/lib example.o example_wrap.o -o _example.so
```


### To address the issue in compiling

> Ref: <https://stackoverflow.com/questions/25882150/python-h-not-found-using-swig-and-anaconda-python>

You can use the command `python-config --cflags` to get the full set of recommended compilation flags:

```bash
$ python-config --cflags
-I/Users/myuser/anaconda/include/python2.7 -I/Users/myuser/anaconda/include/python2.7 -fno-strict-aliasing -I/Users/myuser/anaconda/include -arch x86_64 -DNDEBUG -g -fwrapv -O3 -Wall -Wstrict-prototypes
```

Let `distutils` figure out all the compiling and linking options for you.
```python
# setup.py

from distutils.core import setup, Extension

example_module = Extension('_example', sources=['example.c', 'example.i'])

setup(name='example', ext_modules=[example_module], py_modules=["example"])
```

Then you can run:
```bash
$ python setup.py build_ext --inplace
```





## Reference

* SWIG Official 
<http://www.swig.org/exec.html> (What is SWIG)
<http://www.swig.org/tutorial.html> (Tutorial)

* 利用 SWIG 对 C++ 库进行 Python 包装
<https://segmentfault.com/a/1190000013219667>

* 如何实现 C/C++ 与 Python 的通信？
<https://www.zhihu.com/question/23003213/answer/56121859>
    > 本文分4个部分C/C++ 
    > * 调用 Python （基础篇）— 仅讨论Python官方提供的实现方式Python 
    > * 调用 C/C++ （基础篇）— 仅讨论Python官方提供的实现方式C/C++ 
    > * 调用 Python （高级篇）— 使用 CythonPython 
    > * 调用 C/C++  （高级篇）— 使用 SWIG

