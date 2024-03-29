---
title: "(Python) Built-in function (List & String) "
categories:
  - Python
tags:
  - Python, built-in function
---

# list 와 문자열에 유용하게 사용할 수 있는 몇가지 built-in 함수들을 정리해보자

## 1. sort() 그리고 sorted()

#### sort()
- 'sort()' 메소드 는 'list' 객체의 메소드. 즉 리스트에만 사용가능
- list 를 직접 정렬함 -> 원본 리스트가 변경됨
- 반환값 : 'None'

```python
my_list = [3, 1, 2]
my_list.sort()
print(my_list)  # 출력: [1, 2, 3]
```

#### sorted()
- iterable 객체에 사용 가능 (list, tuple, 문자열 등)
- 제공된 iterable 로 정렬된 새로운 리스트를 생성
- 반환 값 : 정렬된 새로운 리스트

```python
my_list = [3, 1, 2]
sorted_list = sorted(my_list)
print(sorted_list)  # 출력: [1, 2, 3]
print(my_list)  # 출력: [3, 1, 2], 원본 리스트는 변경되지 않음
```

- sorted() 함수에서 'key' 인자를 사용해서도 정렬할 수 있음.
- 가령 key=len 인자를 사용하면, 문자의 길이 순으로 정렬 가능
- key=abs 를 사용하면 절대값에따라 정렬

- **속도** : 일반적으로 sort() 메소드가 sorted() 함수보다 빠름
- **메모리** : 메모리 사용 측면에서도 sort() 메소드가 더 효율적


## 2. enumerate()
- 입력 : iterable 객체, start index(기본으로 0 이다.)
- 출력 : 각 요소를 순회하면서 'index' 'value' 쌍을 포함하는 enumerate 객체

이건 예시가 이해가 더 빠를것이다.

```python
fruits = ['apple', 'banana', 'cherry']
for index, fruit in enumerate(fruits):
    print(index, fruit)
```

```
0 apple
1 banana
2 cherry
```


## 3. split()
- **문자열**에 사용할 수 있는 내장 메소드로, 지정된 구분자에 따라 분할하고, 분할된 각 부분을 포함하는 리스트를 반환
- 쉼표나, 특정 문자혹은 문자열을 기준으로도 분할할 수 있다.

```python
text = "Hello, world! Welcome to Python."
splitted_text = text.split()  # 기본적으로 공백을 기준으로 분할
print(splitted_text) 
#출력 : ['Hello,', 'world!', 'Welcome', 'to', 'Python.']
```

```python
text = 'abide'
splitted_text = text.split('bid')
print(splitted_text)
#출력 : ['a', 'e']
```

추가로 또 적고 싶은 내용이 있으면 정리할건데,  
같은 페이지에 계속 amend 하면서 정리해 나가는것이 효율적인진 모르겠지만.. 일단은 포스트를 써본다.