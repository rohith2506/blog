---
title: "Design and implementation of own vector"
date: 2023-03-21T16:00:00+01:00
tags: ["programming", "c++"]
---

In order to understand the internals of C++ more and more, I am working on some small projects where we try to reimplement some of the standard libraries provided by the language. We start with simple yet powerful vectors. They are contiguous and homogenous arrays which will be resized as they keep growing. Insert time complexity is O(1) while search and remove would be O(n). Here's the simple implementation of Custom Vector class

```
#include <iostream>
#include <memory>

template<typename T>
class Vector {
private:
    T* buffer = nullptr;
    size_t capacity = 0;
    size_t current_index = 0;

public:
    Vector() = default;

    Vector(size_t capacity) {
        buffer = new T[capacity];
    }

    Vector(const Vector<T>& that) {
        delete[] buffer;
        capacity = that.capacity;
        current_index = that.current_index;
        buffer = new T[capacity];
        for (size_t index = 0; index < capacity; index++)
            buffer[index] = that.buffer[index];
    }

    Vector<T>& operator=(const Vector<T>& that) {
        delete[] buffer;
        capacity = that.capacity;
        current_index = that.current_index;
        buffer = new T[capacity];
        for (size_t index = 0; index < capacity; index++)
            buffer[index] = that.buffer[index];
        return *this;
    }

    Vector(size_t capacity, T initial) : capacity(capacity) {
        buffer = new T[capacity];
        for (size_t index = 0; index < capacity; index++)
            buffer[index] = initial;
        current_index = capacity;
    }

    ~Vector() {
        delete[] buffer;
    }

    T& operator[](size_t index) {
        if (index > current_index) {
            throw std::invalid_argument("index must be less than vector size");
        }
        return buffer[index];
    }

    void push_back(const T& element) {
        std::cout << element << std::endl;
        if (current_index == capacity) {
            if (capacity == 0)
                reserve(8);
            else
                reserve(2*capacity);
        }
        buffer[current_index] = element;
        current_index += 1;
    }

    void reserve(size_t capacity) {
        if (capacity > current_index) {
            T* temp = new T[capacity];
            for (size_t index = 0; index < this->capacity; index++)
                temp[index] = buffer[index];
            delete[] buffer;
            this->capacity = capacity;
            this->buffer = temp;
        }
    }

    T pop() {
        if (current_index > 0) {
            T value = buffer[current_index - 1];
            current_index = current_index - 1;
            return value;
        } else {
            throw std::out_of_range("index out of range");
        }
    }

    size_t size() {
        return current_index;
    }
};
```