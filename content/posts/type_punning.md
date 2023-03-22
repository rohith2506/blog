---
title: "Type punning"
date: 2023-03-20T16:00:00+01:00
tags: ["programming", "c++"]
---

Truth be told, I hate raw pointers. Using them is a double edged sword. It can give you power but also destroys you ( metaphorically ;) ). The power comes from the idea that you have such strong control over memory management but also it provides a sneaky way to avoid strong typing C++ usually provides. Enough talk, let's jump into code

For example, let's take this simple example

```
#include <iostream>

struct Entity {
    int foo;
    int bar;
};

int main() {
    const Entity& e{1, 2};

    e.foo = 3;

    std::cout << e.foo << std::endl;

    return 0;
}
```

As you can expect, compiler will throw a big warning that you cannot modify `foo` variable in `e` as it was marked as `const`. But what if there's a way to override the strong type system and can modify `foo`. Here's how you do it


```
#include <iostream>

struct Entity {
    int foo;
    int bar;
};

int main() {
    const Entity& e{1, 2};

    int* temp = (int *)(&e);
    temp[0] = 3;

    std::cout << e.foo << std::endl;

    return 0;
}
```

This infamous technique is called type punning. We just accessed the pointer of the struct and directly modified the memory hence nullifying the usage of `const`. There are some actual usecases where you might need type punning but in general, you should avoid using them otherwise you would end up with memory corruption issues which is never good
