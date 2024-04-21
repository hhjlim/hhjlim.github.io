---
title: "(Python) - List Comprehension "
categories:
  - Python
tags:
  - Python
  - List Comprehension
---

# 리스트 컴프리헨션(List Comprehension)
- 반복문과 조건문을 사용하여 새로운 리스트를 만들때 사용
- 여러 줄의 코드를 한줄로 표현 가능
- 기본 구조는 아래와 같음

```python
[표현식 for 항목 in 반복가능객체 if 조건문]
```

## 예시 1) 2차원 리스트의 초기화
- False 로 초기화 되어 있는 1차원 리스트를 만들고 싶다면 아래 처럼 할 수 있다

```python
n = 5
new_list = [False] * n
# 결과 : [False, False, False, False, False]
``` 

- 아 그럼 2차원 리스트를 만들려면 아래와 같이 하면 되겠구나?

```python
rows, cols = 3 , 3
new_list = [[0] * rows] * cols
# 결과 : [[0, 0, 0], [0, 0, 0], [0, 0, 0]]
``` 

- 아 이제 new_list[0][1] 에 값을 넣어보자

```python
new_list[0][1] = "Hello"
print(new_list)
# 결과 : [[0, 'Hello', 0], [0, 'Hello', 0], [0, 'Hello', 0]]
``` 

- 이 처럼 실제로 새로운 리스트를 여러번 생성하는 것이 아니라, 동일한 리스트([0, 0, 0])에 대한 참조를 여러 번 복사하기 때문에 위와 같은 출력이 나온다.  

- 올바른 초기화는 아래 처럼 리스트 컴프리헨션을 사용한다.

```python
# 리스트 컴프리헨션으로 2차원 리스트를 초기화
new_list = [[0 for _ in range(rows)] for _ in range(cols)]
```
  
<br>

## 예시 2) 딕셔너리 활용
- 딕셔너리의 메소드를 사용하여, 특정 조건을 만족하는 딕셔너리의 키를 리스트로 만드는 예제
- 차근 차근 뜯어보자..

```python
# 예제 딕셔너리
dic = {'a': 1, 'b': 3, 'c': 4}

# 리스트에 특정 조건을 만족하는 값을 찾아, 키를 저장
list = [k for k, v in dic.items() if max(dic.values()) == v]
```

### items()
- dic.items() 메소드를 호출하면, 딕셔너리 객체의 반복자(iterator)를 반환한다.
- 즉 딕셔너리의 키('k') 와 값('v') 를 순회할 수 있도록 한다.

```python
dic = {'a': 1, 'b': 3, 'c': 4}
print(dic.items())

# 출력 : dict_items([('a', 1), ('b', 3), ('c', 4)])
```

### values()
- values() 는 딕셔너리의 모든 '값'을 표함하는 뷰 객체를 반환한다.
- 이 객체를 이용해서, 리스트 또는 다른 반복 가능한 형태로 변환이 가능하다.

```python
dic = {'a': 1, 'b': 3, 'c': 4}
print(dic.values())
# 출력 : dict_values([1, 3, 4])
```

이제 다시 문제의 리스트를 봐보자.

```python
dic = {'a': 1, 'b': 3, 'c': 4}

list = [k for k, v in dic.items() if max(dic.values()) == v]
# 아 이거는 k를 담는 리스트 컴프리헨션 인데..
# k(키), v(값) 항목에 대해, 
# 반복가능 객체 dict_items([('a', 1), ('b', 3), ('c', 4)]) 겠지 여기서 반복문을 돌릴거고..
# 근데 이제 값(v)이 최대인 조건을 만족하는 키만 출력 하니까..
# 근데 이제 k 니까 키 값만 리스트에 담겠다는 거구나..
print(list)
# 결과 : ['c']
```

- ['c'] 라는 리스트가 출력되는것을 확인 할 수 있다.
- 조건을 만족하는 키가 여러 개라면 아래 처럼 여러개가 저장된다. 반복문을 돌면서 k(키) 를 리스트에 넣는거니까!

```python
dic = {'a': 1, 'b': 3, 'c': 4, 'd': 4}
list = [k for k, v in dic.items() if max(dic.values()) == v]
print(list)
# 결과 : ['c', 'd']
```