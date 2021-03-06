---
layout: post
author: "Su-Hyeok Kim"
comments: true
categories:
  - unity
---

Unity 프로젝트는 여러 종류의 데이터를 디렉토리로 판단해 컴파일 한다. 반대로 이 디렉토리 구조를 통해 Unity 의 컴파일 시스템을 알아볼 수도 있다. 디렉토리 구조 자체는 간단하나 숨겨진 뜻은 조금 복잡하다. 그러니 한번 알아보기로 하자.

<!-- more -->
## Unity 프로젝트 구조

일반적인 Unity 프로젝트 디렉토리 구조는 아래와 같다.

> - _Assets_
> - _library_
> - _Pacakges_
> - _ProjectSetting_

Assets 디렉토리는 스크립트, 리소스, 플러그인 등 프로젝트에서 쓰이는 대부분의 데이터를 모아놓은 디렉토리다. 개발은 거의 Assets 폴더안에서 이루어진다. 또한 이 디렉토리 안에서도 여러 종류의 특별한 디렉토리 이름이 있다.

Pacakges 디렉토리는 Unity 에서 지원하는 여러 모듈들의 참조 정보를 가지고 있다. 단지 파일하나만 있기 때문에 무시하기 쉬운데, 참조하는 패키지가 존재한다면 반드시 필요하다. Package Manager 에서 설정하면 그떄 패키지를 컴파일하는 모습을 볼 수 있다.

ProjectSetting 디렉토리는 프로젝트의 여러 옵션들을 관리하는 파일을 가지고 있다. 안을 들여다보면 _Edit -> Project Settings_ 에서 볼 수 있는 이름들과 같은 파일들이 있다.

Library 디렉토리는 위에서 말한 Assets 디렉토리와 ProjectSetting 디렉토리의 모든 데이터를 Unity 가 바로 가져다 쓸 수 있도록 Unity 만의 데이터로 변형한 결과물이다. Unity 에서는 이 결과물들로 대부분의 컴파일을 처리한다.

일정한 정보들은 대부분 하나의 파일로 처리하는 것으로 보이며, _metadata_ 와 _ShaderCache_ 는 파일을 16진수의 32자리 해쉬로 매칭하여 쌍을 이룬다. 512비트로 보인다. 스크립트들의 컴파일된 결과는 _SciptAssemblies_ 에 _dll_ 로 저장된다. _2018_ 이전 버젼까지는 에디터와 런타임 두가지 분류로 _dll_ 을 묶었으나 _2018_ 버젼부터는 사용자 임의로 나누는게 가능해졌다. 일반적으로 _Project_ 창에서 보이는 데이터들은 _assetDatabaseX_ 파일에 저장되는 것으로 생각해볼 수 있다. 에디터 코드에서 조금이라도 건드려 본 경험이 있으면 _AssetDatabase_ 라는 클래스 이름에서 유추가 가능하다.

이 디렉토리의 데이터는 실제 데이터가 아닌 메타 데이터다. 즉 프로젝트에서 당장 없어도 되는 파일이다. 파일을 옮길 때 디스크 크기를 신경써야 한다면 이 디렉토리는 지워도 된다. 그리고 VCS 에 프로젝트를 등록할 때는 Libary 디렉토리는 올리면 안된다. 혼자서 쓸때는 상관없지만 여러명이서 사용할 때는 Asset, ProjectSetting 디렉토리의 데이터와 Library 의 데이터가 달라질 수 있다. 만약 다르다면 실행 시에 오류가 나거나, 다시 컴파일할 수도 있다.

## Assets 안의 특별한 이름들

위에서도 언급했지만 Assets 디렉토리는 게임의 실질적인 데이터들이 들어가 있는 디렉토리다. 그래서 Unity 에서는 에셋들을 관리하기 위해 Assets 디렉토리 안에서도 데이터를 구분하도록 몇개의 디렉토리 이름을 예약해 놓았다. 예약된 이름들은 무조건 정해진 동작을 하니 정해진 동작과 같은 목적이 아니라면 디렉토리 이름을 다르게 설정해주어야 한다.

#### "Standard Assets"

Unity 의 예제 프로젝트들을 실행하면 대부분의 프로젝트들은 Standard Assets 디렉토리가 존재한다. 예제마다 항상 보이는 이 디렉토리는 일반적인 에셋과 다른 분류를 위해 만들어진 이름이다. 이는 Unity 에서 무료로 제공하는 에셋을 저장하는 디렉토리로써, Unity 에디터내에서 빠르게 가져올 수 있느 _Asset_ 이다.

<br/>
![](/images/import_standard.png){: .center-image}
</center>
<br/>

_Standard Assets_ 에서는 꽤 많은 코드들과 리소스들을 제공하기 때문에 사용자가 필요하면 직접 가져다 쓸 수 있는 유용한 무료 애셋들이다.

_Standard Assets_ 의 디렉토리를 나눈 이유는 기능상, 분류상 이유가 있는것으로 보인다. 기능상으로는 [Unity Compile Order](https://docs.unity3d.com/Manual/ScriptCompileOrderFolders.html) 에서 이유를 찾을 수 있다. _Script_ 의 _Compilation_ 우선순위를 바꾸어 주는것으로 보인다. 이는 _Standard Assets_ 폴더의 내용들이 공통적인 것들 이기 때문으로 생각해볼 수 있다.

#### "Streaming Assets"

Unity 에서 특정 플랫폼으로 빌드할 때, 모든 에셋들을 밖에서 접근하지 못하도록 여러개의 바이너리 파일로 묶는다. 그런데 이렇게 변환을 하지 않고 해당 플랫폼에서 파일의 원본이 필요한 경우도 존재하기 마련이다. 보통은 해당 파일을 스트리밍할 떄 필요한 경우가 많다.(영상) 또한 이외에도 빌드한 바이너리 상태에서도 값의 수정이 필요할 경우에 사용되는 경우도 있다.(Config)

그래서 필요한 디렉토리가 Streaming Assets 다. 이름 자체도 스트리밍을 염두해두고 만든 듯 싶다. 이 디렉토리의 파일을 접근하는 방법은 언어에서 제공하는 API를 사용하거나, [WWW](https://docs.unity3d.com/ScriptReference/WWW.html) 를 통해 접근할 수도 있다. 플랫폼 별로 경로가 다르니 유의하길 바란다.

#### "Resources"

Unity 에서는 빌드를 컴파일 할때, 각 씬의 게임오브젝트 마다 연결된 에셋(_Assets 안의 데이터를 칭한다._)들을 찾아서 컴파일 한다. 그리고 런타임에서 Hierarchy 안에 게임 오브젝트가 만들어졌을 때, 게임 오브젝트에 연결된 에셋들을 로드한다.(중복되면 한번만 로드한다.)

그런데 로드하는 과정에서 약간의 문제가 생길 소지가 있다. 크기가 아주 큰 에셋을 로드하면 많은 메모리 공간을 차지해 문제가 생길 우려가 있다. 특히 메모리가 부족한 모바일 플랫폼에서는 신경써서 관리를 해주어야 한다.

그래서 원본 에셋을 동적으로 로드하고, 해제하는 기능을 제공한다. 그 기능을 사용하게 해주는 디렉토리가 Resources 다. 이 디렉토리 안의 데이터들은 런타임에서 경로를 입력해 로드, 언로드가 가능하다.

#### "Editor"

Unity 에서는 Unity 에디터 안에서 일정한 범위의 에디터 커스터마이징을 제공한다. 그런 에디터에서 쓰이는 코드들 또한 Assets 안에 존재한다. 그리고 그 코드들을 모아놓는 디렉토리가 Editor 디렉토리다. 또한 이 폴더안에 모두 분류하는 직접적인 이유가 있다.

Unity 는 런타임 내에서는 에디터 코드와 전혀 상관없이 돌아가게 모듈화를 강제한다. 빌드를 컴파일하게 되면, Editor 디렉토리는 모두 제외하고 컴파일을 한다. 또한 Unity 에서 에디터 바이너리를 제공하는 UnityEditor.dll 또한 빌드에 포함되지 않는다. 런타임 코드에서 UnityEngine 네임스페이스를 참조하고 컴파일하면 직접 에러를 볼 수 있을것이다.

[Unity Compile Order](https://docs.unity3d.com/Manual/ScriptCompileOrderFolders.html) 에서도 Editor 컴파일은 가장 나중에 한다. 에디터 컴파일과 빌드 컴파일을 쉽게 분리하기 위해 이렇게 순서를 정한듯 하다.

#### "Plugins"

Unity 는 Android, PC, macOS 등 왠만한 플랫폼을 지원하는 크로스 플랫폼 게임 엔진이다. 하지만 엔진에서는 자체적으로 지원하는 기능들만 크로스 플랫폼을 지원하고, Third-party 라이브러리가 필요한 경우가 있다. 그래서 Unity 에서는 플랫폼 별로 Plugins 디렉토리에 넣을 수 있는 기능을 제공한다. 또한 정적, 동적 라이브러리 들을 포함하는 용도로도 쓰인다. 자세한 사항은 [Unity Plugins](https://docs.unity3d.com/Manual/PluginInspector.html) 를 참조하라.

## 참조

- [Unity Special Folder](https://docs.unity3d.com/kr/current/Manual/SpecialFolders.html)
- [Unity Compile Order](https://docs.unity3d.com/Manual/ScriptCompileOrderFolders.html)
- [용일이의 개발,번역 블로그](http://yongil.net/unity/2015/04/23/Unity-Special-Folder-Names-in-your-Assets-Folder.html)
- [Unity Plugins](https://docs.unity3d.com/Manual/PluginInspector.html)
