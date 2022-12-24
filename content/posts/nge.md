---
title: "Tricky algorithms and their purpose in interviews"
date: 2022-12-30T10:14:28+01:00
tags: ["programming", "algorithms"]
---

It's been almost 3 months since I have left Radix Trading and while I am contemplating my next steps in my career, I was also looking into some interesting companies who work in the field of HFT and see if they can be a fit to me, which means, some sort of algo prep aka leetcode grind.

Unless you have some sort of regular practice of these kind of problems time to time, it's really hard to get ideas in a constrained environment. I was never a big fan of pure algorithmic questions which might involve some sort of trickery in order to solve the issue. Worst case, this might even induce some sort of impostor syndrome and to doubt yourself whether you are good enough or not. Not a great feeling in my opinion.

I recently had a similar experience with a firm in Amsterdam where they asked me a problem which loosely gets translated to something below

```
Given a stream of integers, Find the next greater element for every current element? The algorithm needs to be optimal
```

The obvious solution you can think of is that for every current index, process the indices and stop at the moment when you find a greater element and repeat that again. The worst case time complexity for this problem is O(n^2)

As usual, the interviewer is not satisfied and he wants a better solution. I tried thinking hard but I couldn't come up with a better solution in that constrained fame. I started playing with all the data structures I know but that couldn't help much because the time constraint is definitely not helping. As expected, the interview didn't go well as expected and I forgot about it but today, I was reminscing the problem again and I sit down to solve it efficiently. Here's the solution

```
std::vector<int> solve(std::vector<int>& input) {
    std::vector<int> result;
    std::stack<int> stck;
    std::unordered_map<int, int> processed;

    for (int index = 0; index < input.size(); index++) {
        if (processed.find(index) != processed.end()) {
            result.push_back(input[processed[index]]);
        }
        else {
            int j = index + 1;
            while (input[j] < input[index]) {
                if (stck.empty()) {
                    stck.push(j);
                } else {
                    while (!stck.empty() && input[stck.top()] < input[j]) {
                        processed[stck.top()] = j;
                        stck.pop();
                    }
                    stck.push(j);
                }
                j = j + 1;
            }
            if (j >= input.size())
                result.push_back(input[index]);
            else
                result.push_back(input[j]);
        }
    }
    return result;
}
```

So, the idea is to use two auxillary data structures (stack and map) to store the already processed elements and to avoid processing them again. It took me around 10-15 minutes to come up with this idea without any help but now I am operating without any time constraint on my head which is usually my ideal state. This makes me think about these tricky questions in interviews and wonder whether they actually tell you something about the candidate you wanna interview. This problem is not intuitive and I wonder what qualities you actually learn about the candidate and definitely not a great way to know about my problem solving skills. Lots of scope for false negatives. 

I think this would be a nice reminder for myself in the future if I am in the interviewer seat. Never ask people questions which are unintuitive. Let's start with a small problem and build it out to the top andwork with them through their thought process. Otherwise, You are just hiring engineers who are great at performing well during these time constraints but not the actual skills your firm care about. My two cents and rant out
