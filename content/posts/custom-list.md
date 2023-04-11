---
title: "Custom List"
date: 2023-04-11T12:00:00+01:00
tags: ["programming", "c++"]
---

To continue with the tradition of implementing our own data structures, I have implemented list container with operations `push_back`, `push_front`, `pop_back` and `pop_front` each with constant time complexity. In order to make it more robust in terms of memory leak, UB, I have used `unique_ptr` instead of raw pointers wherever required. To keep things fun and also more educative, I deliberately made couple of bugs in terms of implementation and wondering whether readers can spot it out. If you can, please leave that in comments. And also, the whole list construction can be done using TMP ( template metaprogramming ) and maybe I will attempt that some other time

```
#include <memory>

template<typename T>
struct Node {
    T data;
    Node(const T& data): data(data), next(nullptr), prev(nullptr) {}
    std::unique_ptr<Node> next;
    Node* prev; // Either next or prev can be unique pointer but not both
};

template <class T>
class List {
private:
    std::unique_ptr<Node<T>> head{};
    Node<T>* tail;
    size_t cur_size;
public:
    List() : head(nullptr), tail(nullptr), cur_size(0) {}

    void push_front(const T& value) {
        auto temp = std::make_unique<Node<T>>(value);
        if (!head) {
            head = std::move(temp);
            tail = head.get();
        } else {
            head->prev = temp.get();
            temp->next = std::move(head);
            head = std::move(temp);
        }
        cur_size++;
    }

    void pop_front() {
        if (!head) {
            throw std::runtime_error("Empty list for pop_front()");
        }
        head = std::move(head->next);
        cur_size--;
    }

    T front() const {
        if (!head) {
            throw std::runtime_error("Empty list for front()");
        }
        return head->data;
    }

    void push_back(const T& value) {
        auto temp = std::make_unique<Node<T>>(value);        
        if (head) {
            temp->prev = tail;
            tail->next = std::move(temp);
            tail = tail->next.get();
        } else {
            head = std::move(temp);
            tail = head.get();
        }
        cur_size++;
    }

    void pop_back() {
        if (!tail) {
            throw std::runtime_error("Empty list for pop_back()");
        }
        tail = std::move(tail->prev);
        tail->next = nullptr;
        cur_size--;
    }

    T back() const {
        if (!tail) {
            throw std::runtime_error("Empty list for back()");
        }
        return tail->data;
    }

    void remove(const T& value) {
        auto current = head.get();
        while (current != nullptr) {
            if (current->data == value) {
                if (!current->prev) {
                    head = std::move(current->next);
                }
                else if (!current->next) {
                    tail = current->prev;
                    tail->next = nullptr;
                }
                else {
                    auto temp = current->next.get();
                    current->next->prev = current->prev;
                    current->prev->next = std::move(current->next);
                    current = temp;
                }
            }
            current = current->next.get();
        }
    }

    friend std::ostream& operator<<(std::ostream& os, const List& list) {
        auto temp = list.head.get();
        while (temp) {
            os << temp->data << " ";
            temp = temp->next.get();
        }
        return os;
    }

    size_t size() const {
        return cur_size;
    }
};
```