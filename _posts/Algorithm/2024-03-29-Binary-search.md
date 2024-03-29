---
title: "(Algorithm) Binary search"
categories:
  - Algorithm
tags:
  - Binary search, Python
---

## 이진탐색을 Python으로 구현해 보자

- Index 를 반환하므로, 결과값에 +1 해주면 7번째 요소임을 알 수 있다.
- 마지막 Index(end)는 len()함수를 쓰면 리스트의 개수가 나오므로, -1 하여 인덱스를 맞춰준다.

```python
def binarySearchRecursive(arr,target,start,end):
    if start > end:
        return None
    mid = (start+end)//2
    if arr[mid]==target:
        return mid
    elif target > arr[mid]:
        return binarySearchRecursive(arr,target,mid+1,end)
    else:
        return binarySearchRecursive(arr,target,start,mid-1)

def binarySearchIterative(arr,target,start,end):
    while(start <= end):
        mid = (start+end)//2
        if arr[mid]==target:
            return mid
        elif target > arr[mid]:
            start = mid + 1
        else:
            end = mid -1
    return None

arr = [1,2,3,4,5,6,7,8,9,10]
target = 7

print(binarySearchRecursive(arr,target,0,len(arr)-1)) # 출력 : 6
print(binarySearchIterative(arr,target,0,len(arr)-1)) # 출력 : 6
```
