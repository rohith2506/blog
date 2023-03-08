---
title: "Any Struct"
date: 2023-03-08T16:00:00+01:00
tags: ["programming", "c++"]
---

Recently, I was asked in an interview how would you design a struct which implements `std::any` in c++. To give more context, This is the input and we need to implement `get` and `set` functions. Caveat is that we should not use templates from c++ on the `struct`

```
struct Any {

    template<typename T> void set(T value) {
    }

    template<typename T> T get() {
    }
};

int main() {
    Any a;
    a.set(1);

    std::cout << a.get<int>() << std::endl;
    
    // should print 1


    a.set(3.14f);
    std::cout << a.get<float>() << std::endl;

    // should print 3.14

    return 0;
}
```

I was unable to come up with a solution during the interview as I found it quite tricky but I think this is a good example of leveraging polymorphism. we will define `VariableTypeBase` base class and `VariableType` template class. We will use `VariableTypeBase` class pointer to store the value at `set` and static cast that to `VariableType` while running `get`. Here's the code

```
#include <iostream>

class VariableTypeBase {
};

template<typename T>
class VariableType : public VariableTypeBase {
public:
    T value;
    VariableType(T value): value(value) {
    }
};

struct Any {
    VariableTypeBase* val;

    template<typename T> void set(T value) {
        val = new VariableType<T>(value);
    }

    template<typename T> T get() {
        return static_cast<VariableType<T>*>(val)->value;
    }
};

int main() {
    Any a;
    a.set(1);

    std::cout << a.get<int>() << std::endl;

    a.set(3.14f);
    std::cout << a.get<float>() << std::endl;

    return 0;
}
```