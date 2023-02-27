---
title: "CRTP"
date: 2023-02-27T09:00:00+01:00
tags: ["programming", "c++"]
---

When dealing with low latency programming, Every nano / micro second counts and that means some of the language features which is acceptable to 99.9% of developer community won't cut out here. I am gonna talk about one of those features. <b>Virtual functions</b>. In C++, we achieve run-time polymorphism using virtual functions. Here's a simple example

```
#include <iostream>
#include <chrono>
#include <iostream>

using namespace std;

class Polygon {
    public:
        virtual double getArea() = 0;
};

class Square : public Polygon {
    private:
        double length;
    public:
        Square(double length) : length(length) {} 

        double getArea() {
            return length * length;
        }
};

int main() {
    Polygon* p = new Square(4);
    cout << p->getArea() << endl;
    return 0;
}
```

In a nutshell, We declare polygon interface and derive our square class through the interface. The major performance issue with virtual functions is vTable. Every object of an instantiated class stores a vPtr ( virtual pointer ) to this vTable which contains the list of method references. This work is done by the compiler. So, every time you access a method, we retrieve that vPtr and then find the relevant entry in the vTable and call that specific method. Acessing a random memory everytime you want to call an object is definitely noticeable in terms of performance especially if the object is in the hot path

Fortunately, there's a solution for this. CRTP (aka) Curiously Recurring Template Pattern. Instead of virtual functions, we leverage templates and static casting to determine which method needs to be called hence eliminating the use of vPtr and vTable. Here's the code

```
#include <iostream>
#include <chrono>
#include <iostream>

using namespace std;

template<class T>
class Polygon {
    public:
        double getArea() {
            return static_cast<T*>(this)->getArea();
        }
};

class Square : public Polygon<Square> {
    private:
        double length;
    public:
        Square(double length) : length(length) {}

        double getArea() {
            return length * length;
        }

};


int main() {
    Polygon<Square>* p = new Square(4);
    cout << p->getArea() << endl;
    return 0;
}
```

