---
title: "(Algorithm) Greedy Introduction"
layout: single
categories:
  - Algorithm
tags:
  - Greedy, Python
---

**(문제) 1이 될 때까지 :**
- 어떠한 수 N이 1이 될 때까지 다음의 두 과정 중 하나를 반복적으로 선택하여 수행
- 단, 두번째 연산은 N이 K로 나누어 떨어질 때만 선택할 수 있습니다.
1. N에서 1을 뺍니다
2. N을 K로 나눕니다.
- N과 K가 주어질 때 N이 1이 될 때까지 1번 혹은 2번의 과정을 수행해야 하는 최소 횟수를 구하는 프로그램 작성

## 나의 접근
- 아래 처럼 문제에서 주어지는 그대로 작성 하였고, 시간 복잡도는 O(n) 이다.  

```python
print('input two numbers : ')
n,k=list(map(int,input().split()))

counter1 = 0

def to_one(n,k):
    global counter1
    while(n!=1):
        if(n%k==0):
            n=n/k
            counter1+=1
        else:
            n-=1
            counter1+=1
    return counter1

print("My answer : ",to_one(n,k))
```

## 시간 복잡도를 단축 시킨 해답
- 시간 복잡도를 O(logn) 으로 단축시킨 해답은 아래와 같다. 

```python
def to_one_log(n,k):
    global counter2
    while(True):
        target = (n//k)*k
        counter2 += n-target
        n=target
        if(n<k):
            break
        counter2 += 1
        n = n/k
    counter2 += (n-1)
    return int(counter2)
```

아이디어은 N에서 1을 빼야하는 횟수를 target 을 계산하여 알 수 있기때문에,  
미리 이를 계산하여, 한번에 counter에 추가하는 접근 방법이다.

솔직히 이런 접근을 어떻게 처음부터 생각할까? 라는 생각이 들었고..  
문제를 더 많이 풀어봐야 이러한 테크닉이 생길 수 있을 것같다. 파이팅!

문제 출처 : [(이코테 2021 강의 몰아보기) 2. 그리디 & 구현](https://youtu.be/2zjoKjt97vQ?si=zkHOFrDdLCFlHqPK&t=689)