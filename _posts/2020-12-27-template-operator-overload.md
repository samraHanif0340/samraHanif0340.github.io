---
title: "C++ template operator overload for template class"
tags: 
  - template 
categories:
  - cpp   
last_modified_at: 2020-12-27
---

An example code to perform template operator overload for a template class in C++ is provided.

Run and consider the output of the [example](https://gist.github.com/Adaickalavan/3bb8646af32e64fb255b85d09c1d3373) below. 

```cpp
#include <iostream>
#include <vector>

using std::ostream;
using std::vector;
using std::cout;

template<class T>
class List {
    private:
        std::vector<T> vec;
    public:
        void push_back(T t){
        vec.push_back(t);
        };

        template<class U>
        friend ostream& operator<<(ostream& os, const List<U>& L );
};

template<class T>
ostream& operator<<(ostream& os, const List<T>& L ){  
    for (T it : L.vec){
        os << it << " .. ";
    }
    return os;
}

int main(void){  
    List<int> L;
    L.push_back(1);
    L.push_back(2);
    L.push_back(3);
    std::cout << L;

    return 0;
}  
```
The expected output is:

```bash
1 .. 2 .. 3 .. 
```