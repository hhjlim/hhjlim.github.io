---
title: "(Algorithm) Implementation - Knight Movements"
categories:
  - Algorithm
tags:
  - Algorithm
  - Implementation
  - Python
---

### **(문제) 왕실의 나이트 :**
- 첫째 줄에 8 x 8 좌표 평면상에서 현재 나이트가 위치한 곳의 좌표를 나타내는 두 문자로 구성된 문자열
- 나이트가 이동할 수 있는 경우의 수를 출력

### 나의 초견
- 지난번의 지도 찾기 문제와 유사하게, 8가지 움직임을 표현하면 되겠다고 생각

```python 
data = input()

x,y=8,8

# Night Movements
dx = [ 1,-1, 2, 2, 1,-1,-2,-2]
dy = [-2,-2,-1, 1, 2, 2, 1,-1]

data_mapping = {'a':1,'b':2,'c':3,'d':4,'e':5,'f':6,'g':7,'h':8}

def check_8ways(x,y):
    count = 0
    for i in range(8):
        nx = x + dx[i]
        ny = y + dy[i]
        if(nx <= 0 or nx > 8 or ny <=0 or ny >8):
            continue
        else:
            count += 1
    return count

print("result : ",check_8ways(data_mapping[data[0]],int(data[1])))
```

### 모범 답안
- 크게 다른건 없었지만, 알파벳을 숫자로 맵핑하는 부분을 아스키 코드를 이용하여 구현 하였다.
- 즉 아래 값은 65 - 65 + 1 이므로 1이 맵핑된다. 
- b 는 66 이므로 (66 - 65 + 1) 2 가 맵핑된다.

```python
# ord() 함수는 문자를 대응하는 유니코드 코드 포인트로 변환한다.
data_trim_x = ord(data[0])-ord(`a`) + 1
```


문제 출처 : [(이코테 2021 강의 몰아보기) 2. 그리디 & 구현](https://youtu.be/2zjoKjt97vQ?si=CcJzV6PhIw2xKbab&t=2253)