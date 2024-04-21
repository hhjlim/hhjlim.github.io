---
title: "(Algorithm) Implementation - String Reordering"
categories:
  - Algorithm
tags:
  - Algorithm
  - Implementation
  - Python
---

### **(문제) 문자열 재정렬 :**
- 첫째 줄에 하나의 문자열 S
- 알파벳은 알파벳끼리 모아서 앞에 정렬 + 숫자는 숫자끼리 전부다 더해서 문자열의 맨끝에 출력

### 나의 초견
- 일단 isalpha() 혹은 isalnum() 함수의 존재를 몰랏기 때문에.. data를 전부다 숫자랑 일일이 비교하고,
- 숫자가 아닌 값은 모두 알파벳으로 간주하였다.

```python 
data = input()

data_str=''
data_int=0

for i in data:
    if(i=='1' or i =='2' or i =='3' or 
       i =='4' or i =='5' or i =='6' or 
       i =='7' or i =='8' or i =='9' or i =='0'):
        data_int += int(i) # 숫자는 int 로 변환해서 모두 합치기
    else:
        data_str += i # str 끼리는 붙여지니까 잘 붙여준다.

# 문자열을 정렬할 리스트가 하나 필요함
data_sort = list(map(str,data_str))
data_sort.sort()

# 최종 결과를 담을 리스트
data_str_sort=''
for x in data_sort:
    data_str_sort += x

print(data_str_sort+str(data_int))
```

### 모범 답안
- isalpha() 함수 혹은 isalnum() 함수를 적절하게 다음에는 잘 사용해보도록 하자.
- join() 함수도 적절하게 잘 사용하자. 리스트 들을 한번에 합치기 가능

```python
data = input()

result=[]
num =0

for i in data:
    if i.isalpha(): #여기서 == true 같은 걸 안붙이는게 pythonic 한 문법이다..!
        result.append(i)
    else
    num += int(i)

result.sort()

if (num != 0):
    result.append(str(num))

print(''.join(result))

```


문제 출처 : [(이코테 2021 강의 몰아보기) 2. 그리디 & 구현](https://youtu.be/2zjoKjt97vQ?si=CcJzV6PhIw2xKbab&t=2253)