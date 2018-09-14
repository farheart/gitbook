# Send Python dict to C/C++ function by SWIG

## Python to C

### Example-1

#### Codes

```c
/* File: example.i */
%module example
%{
    /* Put header files here or function declarations like below */
    extern double My_variable;
    extern int fact(int n);
    extern int my_mod(int x, int y);
    extern char *get_time();
    extern char *getTime();
%}

extern double My_variable;
extern int fact(int n);
extern int my_mod(int x, int y);
extern char *get_time();
extern char *getTime();
```

```c
/* File : example.c */
#include <time.h>
double My_variable = 3.0;

int fact(int n) {
    if (n <= 1) return 1;
    else return n*fact(n-1);
}

int my_mod(int x, int y) {
    return (x%y);
}

char *get_time()
{
    time_t ltime;
    time(&ltime);
    return ctime(&ltime);
}

char *getTime() {
    time_t tVal;
    time_t* p = &tVal;
    time(p);
    return ctime(p);
}
```

#### Compile
```bash
# Create example_wrap.c and example.py using swig
$ swig -python example.i

# Compile .c to .o file
$ gcc -c example.c example_wrap.c -I/usr/local/anaconda3/envs/django/include/python3.6m

# Link .o to .so library
$ gcc -bundle -undefined dynamic_lookup -L/usr/local/anaconda3/envs/django/lib example.o example_wrap.o -o _example.so

```

#### Python test
*Note*: Since we compile it using **conda - django - Python3.6**, before test, need to activate the conda env first
```bash
$ conda activate django
$ python
```
```python
# --------------------------------------
# Test example module
# --------------------------------------
import example
print(example.fact(5))      # 120
print(example.my_mod(5,3))  # 2
print(example.get_time())   # current time
print(example.getTime())    # current time
```

## Python to C++

### Example-2
This example uses only one `.i` file, and implement the function inside the `.i` file.
#### Codes

```c

// File: test.i
%module test

%include "std_string.i"
%include "std_map.i"

namespace std {
    %template(map_string_string) map<string, string>;
}

void foo(const std::map<std::string, std::string> &val);

%{
#include <iostream>
#include <string>
#include <map>

using namespace std;

void
foo(const map<string, string> &val)
{
    map<string, string>::const_iterator i = val.begin();
    map<string, string>::const_iterator end = val.end();
    while (i != end) {
        cout << i->first << " : " << i->second << endl;
        ++i;
    }
}
%}
```

#### Compile
```bash
# The following cmd creates test.py, test_wrap.cxx
$ swig -python -c++ test.i

# Compile and link to _test.so
$ g++ -fPIC -shared -undefined dynamic_lookup -I/usr/local/anaconda3/envs/django/include/python3.6m -L/usr/local/anaconda3/envs/django/lib -o _test.so test_wrap.cxx
```

#### Python test
```python
# Python test
import test

m = {'a' : 'b', 'c' : 'd'}
test.foo(m)

# do dict to map converting first, not necessary
x = test.map_string_string(m)
test.foo(x)
```


#### Reference
<https://stackoverflow.com/questions/9041192/how-does-swig-wrap-a-mapstring-string-in-python>


### Example 3

#### Codes

```c
// File: wy_test.i
%module wy_test

%include "std_string.i"
%include "std_map.i"

namespace std {
    %template(map_string_float) map<string, double>;
	
    // Tuple / pair as key of dict / map
    %template(map_pair_string_to_float) map<pair<string, string>, double>;
    
    // %template(map_pair_int_to_int) map<pair<int, int>, int>;
}

void tryStr_Float(const std::map<std::string, double> &val);
void tryStrTuple_Float(const std::map<std::pair<std::string, std::string>, double> &val);
// void tryIntTuple_Int(const std::map<std::pair<int, int>, int> &val);

%{
#include <iostream>
#include <string>
#include <map>

using namespace std;

void
tryStr_Float(const map<string, double> &val)
{
    map<string, double>::const_iterator i = val.begin();
    map<string, double>::const_iterator end = val.end();
    while (i != end) {
        cout << i->first << " : " << i->second << endl;
        i++;
    }
}

void
tryStrTuple_Float(const map<pair<string, string>, double> &val) {
    map<pair<string, string>, double>::const_iterator i = val.begin();
    map<pair<string, string>, double>::const_iterator end = val.end();
    while (i != end) {
        cout << "(" << (i->first).first << "," << (i->first).second <<  ") : " << i->second << endl;
        i++;
    }
}
%}
```

#### Compile
```bash
$ swig -python -c++ wy_test.i
$ g++ -fPIC -shared -undefined dynamic_lookup -I/usr/local/anaconda3/envs/django/include/python3.6m -L/usr/local/anaconda3/envs/django/lib -o _wy_test.so wy_test_wrap.cxx
```

#### Test
```python
# Python test
import wy_test

m1 = {k:k * 0.5 for k in range(5)}
wy_test.doStr_Float(m1)

m2 = {('x{}'.format(i), 'y{}'.format(i)): i for i in range(5)}
wy_test.doStrTuple_Float(m2)
```