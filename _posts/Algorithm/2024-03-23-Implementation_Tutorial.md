---
title: "(Algorithm) Implementation Tutorial"
categories:
  - Algorithm
tags:
  - Implementation, Python
---

**(문제) 상하좌우:**
- 여행가 A는 N x N 크기의 정사각형 공간 위에 서 있습니다.
- 이 공간은 1 x 1 크기의 정사각형으로 나누어져 있습니다.
- 가장 왼쪽 위 좌표는 (1, 1)이며, 가종 오른쪽 아래 좌표는 (N, N)에 해당
- 상, 하, 좌, 우 방향으로 이동가능 시작 좌표는 항상 (1,1)
- 계획서에는 L R U D 중의 문자 하나가 공백을 기준으로 반복적으로 적혀있다.

### 나의 풀이..
- 눈으로 이동하는걸 보고 싶었기 때문에 print 하면서 해보았고, 
- 각각을 함수화 시켜서 구현하였다.
- 사실 글로벌로 선언하면 파라미터 전달하고 말고 할것도 없는데 답지와 호환 가능하게 하느라 조금 코드가 어지럽다..

```python 
def print_current_position(n,my_position):
    arr = [[0] * n for _ in range(n)] #list comprehension 을 사용해야함을 잊지 말자
    for i in range(n):
        if i == 0:
            continue
        else:
            for j in range(n):
                if j == 0:
                    continue
                else:
                    if (my_position[0]==j and my_position[1]==i):
                        print("M",end=' ')
                    else:
                        print(arr[i][j],end=' ')
        print()
    print()

def L(n,my_position):
    my_position[0] -=1
    print("Moving Left")
    if(my_position[0]<=0):
        print("Error Can't move left")
        my_position[0] +=1
    print_current_position(n,my_position)

def R(n,my_position):
    my_position[0] +=1
    print("Moving Rigt")
    if(my_position[0] >= n):
        print("Error Can't move Right")
        my_position[0] -=1
    print_current_position(n,my_position)

def U(n,my_position):
    my_position[1] -=1
    print("Moving Up")
    if(my_position[1]<=0):
        print("Error Can't move Up")
        my_position[1] +=1
    print_current_position(n,my_position)

def D(n,my_position):
    my_position[1] +=1
    print("Moving Down")
    if(my_position[1] >= n):
        print("Error Can't move Down")
        my_position[1] -=1
    print_current_position(n,my_position)

def main():
    n = int(input())
    movement = input().split()

    #dbn_answer(n, movement)

    my_position = [1,1]

    for move in movement:
        if move == "L":
            L(n,my_position)
        elif move == "R":
            R(n,my_position)
        elif move == "U":
            U(n,my_position)
        elif move == "D":
            D(n,my_position)

    print(my_position[1],my_position[0])
    
if __name__ == "__main__":
    main()
```

### 모범답안
- 사실 이렇게 적어두면 굉장히 직관적이고, 
- 추가로 대각선 방향이 추가 되었을때까지 대응 할 수 있다는 장점 까지 있다...

```python
def dbn_answer(n, movement):
    dbn_position = [1,1]
    dx = [0,0,-1,1]
    dy = [-1,1,0,0]
    movetypes = ['L','R','U','D']

    for move in movement:
        for i in range(len(movetypes)):
            if move == movetypes[i]:
                nx = dbn_position[0] + dx[i]
                ny = dbn_position[1] + dy[i]
        if nx < 1 or ny < 1 or nx >n  or ny > n:
            continue
        dbn_position[0] = nx
        dbn_position[1] = ny

    print(dbn_position[0],dbn_position[1])
```

또 dx dy 라고 적는게 공학(?)적이지 않는가.. 잘 숙지해야겠다.  
출력 결과물과 함께 마무리..

```
5   
R R R U D D
3 4

Moving Rigt
0 M 0 0 
0 0 0 0 
0 0 0 0 
0 0 0 0 

Moving Rigt
0 0 M 0 
0 0 0 0 
0 0 0 0 
0 0 0 0 

Moving Rigt
0 0 0 M 
0 0 0 0 
0 0 0 0 
0 0 0 0 

Moving Up
Error Can't move Up
0 0 0 M 
0 0 0 0 
0 0 0 0 
0 0 0 0 

Moving Down
0 0 0 0 
0 0 0 M 
0 0 0 0 
0 0 0 0 

Moving Down
0 0 0 0 
0 0 0 0 
0 0 0 M 
0 0 0 0 

3 4
```

문제 출처 : [(이코테 2021 강의 몰아보기) 2. 그리디 & 구현](https://youtu.be/2zjoKjt97vQ?si=CcJzV6PhIw2xKbab&t=2253)