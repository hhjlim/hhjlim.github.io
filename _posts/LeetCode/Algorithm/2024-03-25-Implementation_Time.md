---
title: "(Algorithm) Implementation - Time"
categories:
  - LeetCode
  - Algorithm
tags:
  - Algorithm
  - Implementation
  - Python
---

## **(문제) 시각 :**
- 첫째 줄에 정수 N
- 00시 00분 00초 부터 N시 59분 59초 까지의 모든 시각 중에서 3이 하나라도 포함되는 모든 경우의 수를 출력

### 나의 초견
- 아 타이머를 구현하면 되겠구나 하고 빼는 방식으로 for문을 짜봄
- 좀 많이 기괴하긴한데 답은 잘나옴..

```python 
n = int(input())

def check_three(n):
    count = 0
    for n in range(n,-1,-1):
        for l in range(5,-1,-1):
            for k in range(9,-1,-1):
                for j in range(5,-1,-1):
                    for i in range(9,-1,-1):
                        if (i==3 or j==3 or k==3 or l==3 or n==3):
                            count += 1
    return count

print(check_three(n))
```

### 모범 답안
- c++ 나 java 스타일에 가까운.. 시간을 처리하는 방식이다.

```python
n = int(input())

def check_if_three(h,m,s):
    ## 몫 연산자(floor division)와 나머지 연산자 사용(모듈러)
    if(h%10 ==3 or m//10==3 or m%10==3 or s//10==3 or s%10 ==3):
        return True
    else:
        return False

def dbn_check_three(n):
    count =0
    for i in range(n+1):
        for j in range(60):
            for k in range(60):
                if(check_if_three(i,j,k)):
                    count += 1
    return count

print(dbn_check_three(n))
```

## Pythonic answer
- str 처리 방식을 이용해서, in 을 통해서 3이 있는지 없는지 간단하게 알 수 있다.
- in 키워드는 리스트, 튜플에도 사용가능해서 편리한것 같다.

```python
def dbn_check_str(n):
    count =0
    for i in range(n+1):
        for j in range(60):
            for k in range(60):
                if '3' in str(i)+str(j)+str(k):
                    count += 1
    return count
```

문제 출처 : [(이코테 2021 강의 몰아보기) 2. 그리디 & 구현](https://youtu.be/2zjoKjt97vQ?si=CcJzV6PhIw2xKbab&t=2253)