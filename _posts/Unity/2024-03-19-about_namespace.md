---
title: "Unity - Global Namespace"
categories:
  - Unity
tags:
  - C#
---

Unity 환경에서 Script를 추가하다 보면, 다른 스크립트 파일에 있는 클래스를 사용하고 싶을때가 있다  

예를 들면 아래 처럼 namespace를 이용해서 다른 파일에 정의된 클래스를 사용할 수 있다.  

## 1.namespace를 이용하는 방법
```c#
// a.cs
namespace MyGame
{
    public class Aclass
    {
        // 클래스 구현
    }
}
```

```c#
// b.cs
using MyGame; // a.cs에서 정의된 Aclass의 네임스페이스

public class Bclass : MonoBehaviour
{
    void Start()
    {
        Aclass myAclass = new Aclass(); // Aclass 인스턴스 생성
        // ...
    }
}
```

## 2.Global namespace
하지만 놀랍게도 Unity 에서는 namespace 를 사용하지 않아도, 스크립트 간에 서로 정의된 클래스를 사용할 수 있었는데, 이것이 가능했던 이유는  Unity는 기본적으로 Global namespace를 사용하기 때문이라고 한다.

'global::' prefix 로 네임스페이스 충돌 문제를 해결하는 간단한 예제로 마무리한당  

```c#
// 사용자 정의 UnityEngine 네임스페이스와 Debug 클래스
namespace UnityEngine
{
    public class Debug
    {
        public static void Log(string message)
        {
            // Unity의 기본 Debug.Log를 호출하려면 global:: 접두사를 사용
            global::UnityEngine.Debug.Log("Custom Debug: " + message);
        }
    }
}

// 메인 게임 스크립트
public class GameScript : MonoBehaviour
{
    void Start()
    {
        // 사용자 정의 Debug.Log 메서드 호출
        UnityEngine.Debug.Log("Game started");

        // Unity의 기본 Debug.Log 메서드 호출
        global::UnityEngine.Debug.Log("Unity's Debug Log");
    }
}
```