---
title: "Template metaprogramming"
date: 2023-04-01T16:00:00+01:00
tags: ["programming", "c++"]
---

Template metaprogramming(TMP) has been introduced in C++11 as a programming technique that perform computations at compile time. This is one of the topics which is quite alien to me and truth be told, I have been struggling to understand some of the stuff. So, In order to gain some proficiency in this topic, I have decided to solve some tasks ranging difficulty from beginner to intermediate such that I become comfortable but also this can be used as reading material for others


##### 1. Design a container which takes a type T and size N at compile time and creates either on stack or heap. If the container size is less than predefined max size, we should create it on stack or otherwise, we should create it on heap


```
#include <iostream>
#include <vector>
#include <array>

constexpr int MAX_SIZE = 1024;

template<typename T, size_t n>
class MyContainer {
public:
    using type = std::conditional_t<n >= MAX_SIZE, std::vector<T>, std::array<T, n>>;
};

int main() {
    MyContainer<int, 1023>::type o;
    MyContainer<float, 1025>::type o2;
    o[0] = 1;
    o2.push_back(1);
    return 0;
}
```

##### 2. Design a fibonacci function which evaluates at compile time

```

#include <iostream>
#include <type_traits>

template<size_t i, class Enable = void>
struct Fibonacci
{
    static constexpr size_t value = 0;
};

template<size_t i>
struct Fibonacci<i, std::enable_if_t<i == 1>>
{
    static constexpr size_t value = 1;
};

template<size_t i>
struct Fibonacci<i, std::enable_if_t<(i > 1)>>
{
    static constexpr size_t value = Fibonacci<i-2>::value + Fibonacci<i-1>::value;
};

int main() {
    std::cout << Fibonacci<41>::value << std::endl;
    return 0;
}

```


##### 3. Write a simple TMP function to check whether the type is pointer or not

```
#include <iostream>
#include <type_traits>

template<typename T>
struct is_pointer {
    static constexpr bool value = false;
};

template<typename T>
struct is_pointer<T*> {
    static constexpr bool value = true;
};

int main() {
    std::cout << is_pointer<int*>::value << std::endl;
    return 0;
}
```


##### 4. Write a TMP function which computes factorial of a given integer

```
#include <iostream>
#include <type_traits>

template<size_t n, class Enable = void>
struct Factorial {
    static constexpr size_t value = 1;
};

template<size_t n>
struct Factorial<n, std::enable_if_t<(n > 1)>> {
    static constexpr size_t value = n * Factorial<n - 1>::value;
};

int main() {
    std::cout << Factorial<40>::value << std::endl;
    return 0;
}
```


##### 5. Write a TMP function that determines if a given integer is a prime number or not

```
#include <iostream>
#include <type_traits>

template<size_t N, size_t D>
struct IsPrimeHelper {
    static constexpr bool value = (N % D != 0) && IsPrimeHelper<N, D-1>::value;
};

template<size_t N>
struct IsPrimeHelper<N, 2> {
    static constexpr bool value = true;
};

template<size_t N>
struct IsPrime {
    static constexpr bool value = IsPrimeHelper<N, N/2>::value;
};


int main() {
    std::cout << IsPrime<11>::value << std::endl;
    return 0;
}
```

##### 6. Write a TMP function that takes two arguments and returns the maximum out of those. We should compare two convertible types too if possible

```

#include <iostream>
#include <type_traits>

template<typename T1, typename T2>
std:econditional_t<std::is_convertible_v<T1, T2>, T2, T1> max(T1 a, T2 b) {
    return (a > b)
        ? static_cast<std::conditional_t<std::is_convertible_v<T1, T2>, T2, T1>>(a)
        : static_cast<std::conditional_t<std::is_convertible_v<T1, T2>, T2, T1>>(b);
}

int main() {
    std::cout << max(1, 2) << std::endl;
    std::cout << max(1, 2.0) << std::endl;
    return 0;
}

```

##### 7. Write a TMP function which calculates the rank of an array

```
#include <iostream>
#include <type_traits>


// non array variables
template<typename T>
struct Rank {
    inline static constexpr int value = 0;
};

// arr[]
template<typename T>
struct Rank<T[]> {
    inline static constexpr int value = 1u + Rank<T>::value;
};

// This is the generic one will will recurse through stuff
template<typename T, size_t N>
struct Rank<T[N]> {
    inline static constexpr int value = 1u + Rank<T>::value;
};

int main() {
    std::cout << Rank<char[2][3]>::value << std::endl;
    std::cout << Rank<char>::value << std::endl;
    std::cout << Rank<int*[3][4][5][6]>::value << std::endl;
    return 0;
}
```

##### 8. Assume the vector is sorted, Remove all the duplicate elements from the vector

```
#include <iostream>
#include <type_traits>
#include <vector>

template<int... Ts>
struct Vector {};


template<typename InVector, typename OutVector = Vector<>>
struct uniq;

// First and second elements are same
template<int i, int... tail, int... OutVecElements>
struct uniq<Vector<i, i, tail...>, Vector<OutVecElements...>> {
    using type = typename uniq<Vector<i, tail...>, Vector<OutVecElements...>>::type;
};

// First and second elements are not the same
template<int i, int... tail, int... OutVecElements>
struct uniq<Vector<i, tail...>, Vector<OutVecElements...>> {
    using type = typename uniq<Vector<tail...>, Vector<OutVecElements..., i>>::type;
};

// When the input vector is empty
template<typename OutVector>
struct uniq<Vector<>, OutVector> {
    using type = OutVector;
};

int main() {
    static_assert(std::is_same_v<typename uniq<Vector<1,2,2,3,4>>::type, Vector<1,2,3,4>>);
    return 0;
}
```

That's it for now. I still think that there are some knowledge gaps for me in this topic and I will revisit it time to time
