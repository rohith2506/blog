---
title: "Back to fun programming"
date: 2022-04-30T15:14:28+01:00
tags: ["programming", "maths"]
---

In order to break the spell of ever lasting procrastination in terms of side projects I want to do, I am resorting to small steps of solving fun problems which brings me the joy of applying programming to fun math problems. I am planning to do this time to time from now on.

The problem I have decided to solve for today is this one ( https://projecteuler.net/problem=102 ). In short, We need to find which triangles contains the origin.

Imagine a triangle with (A,B,C) as coordinates and let's say P is a point inside the triangle boundaries, P is said to be interior point of triangle if it satisfies

Area(P,A,B) + Area(P,B,C) + Area(P,A,C) = Area(A,B,C)

where Area = Area of triangle which is defined as |x1(y2-y3) + x2(y3 - y1) + x3(y1 - y2)|/2

Here's the solution in c++

```
double calculate_area(Point p1, Point p2, Point p3)
{
    int x1 = p1.x, x2 = p2.x, x3 = p3.x;
    int y1 = p1.y, y2 = p2.y, y3 = p3.y;
    return abs(x1 * ( y2 - y3 ) + x2 * ( y3 - y1 ) + x3 * ( y1 - y2 )) / 2.0;
}

bool contains_origin(std::vector<int> points)
{
    Point p_a = Point { points[0], points[1] };
    Point p_b = Point { points[2], points[3] };
    Point p_c = Point { points[4], points[5] };
    Point p_o = Point { 0, 0 };

    double a1 = calculate_area(p_a, p_b, p_o);
    double a2 = calculate_area(p_b, p_c, p_o);
    double a3 = calculate_area(p_a, p_c, p_o);
    double total_area = calculate_area(p_a, p_b, p_c);

    return (a1 + a2 + a3 == total_area);
}
```
