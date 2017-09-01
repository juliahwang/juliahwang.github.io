---
layout: post
title: Project Euler - Problem 1
categories: [알고리즘-Euler]
tags: [알고리즘, 파이썬]
description: 
---

10보다 작은 자연수 중에서 3 또는 5의 배수는 3, 5, 6, 9 이고, 이것을 모두 더하면 23이다. 이 때, 1000보다 작은 자연수 중에서 3 또는 5의 배수를 모두 더한 값을 구하는 문제다.

[Project Euler 사이트의 1번 문제 보러가기](http://euler.synap.co.kr/prob_detail.php?id=1)

<br>

### 내가 짠 코드 

```python
"""
Problem 1
"""

a = 1000
def problem1(a):
    num_list = []
    for i in range(a):
        if i % 3 == 0 or i % 5 == 0:
        # if not (i % 3 and i % 5):
            num_list.append(i)
    result = 0
    for num in num_list:
        result += num
    return result

problem1(a)
# 233168
```

<br>

### 풀이 

먼저 매개변수로 주어진 수의 범위 안에서 3의 배수이거나 5의 배수인 수를 `num_list`에 추가한다. 리스트에 모두 추가된 후에는 다시 반복문으로 초기값이 0인 결과변수인 `result`에 하나씩 더하여 총합을 리턴하도록 구현했다.

<br>
