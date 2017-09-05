---
layout: post
title: Project Euler - Problem 2
categories: [알고리즘-Euler]
tags: [알고리즘, 파이썬]
description: 
---

피보나치 수열의 각 항은 바로 앞의 항 두 개를 더한 것이 된다. 1과 2로 시작하는 경우 이 수열은 아래와 같다.

```
1, 2, 3, 5, 8, 13, 21, 34, 55, 89, ...
```

이 때 짝수이면서 4백만 이하인 모든 항을 더하면 얼마가 되는지 구하는 문제다. 

[Project Euler 사이트의 2번 문제 보러가기](http://euler.synap.co.kr/prob_detail.php?id=2)

<br>

### 내가 짠 코드 

```python
"""
Problem 2
"""
def problem2(a, b):
    result = 0
    # 피보나치는 b의 값을 나열한 것.
    while b <= 4000000:
        # a는 피보나치수열을 완성하기 위해 도와주는 역할
#         0, 1 = 1, 1
#         1, 2 = 2, 3
#         2, 3 = 3, 5
#         3, 5 = 5, 8
        a, b = b, a + b
        if b % 2 == 0:
            result += b
    return result
    
# 461732
```

<br>

### 풀이

문제에서 제한한 대로 4백만 이하의 범위 내에서 반복문으로 피보나치 수열을 구한 후 짝수항만 더하는 조건에 만족하도록 코드를 작성하였다.

<br>

