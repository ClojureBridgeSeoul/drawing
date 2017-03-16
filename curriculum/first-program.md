Quil을 이용해 첫 번째 프로그램 만들기
===================================

지금까지 클로저 코드 작성과 관련된 기본적인 내용을 익혔으니, 이제 독립적으로 실행되는
애플리케이션을 만드는 방법을 알아 보겠습니다.

그러러면 *프로젝트(project)*를 생성해야 합니다. 먼저 *이름공간(namespace)*을 이용해
프로젝트 구성하는 법을 배우게 됩니다. 다음으로 프로젝트의 *의존
라이브러리(dependencies)*들을 지정하는 방법을 배웁니다. 마지막으로 프로젝트를 빌드해서
독립적으로 실행되는 애플리케이션 만드는 방법을 배웁니다.


## 프로젝트 생성하기

지금까지 여러분은 REPL에서 코드를 실행했습니다. 그런데 REPL에서 행한 모든 작업은, REPL을
닫을 때 모두 사라지게 됩니다. 프로젝트를 이용하면, 여러분이 작성한 코드를 영구적으로
저장할 수 있습니다. 프로젝트를 생성하고 관리하기 위해 "Leiningen"이라는 도구가
사용됩니다. 새로운 프로젝트를 생성하려면, 다음의 명령을 실행합니다.


```clojure
lein new quil drawing
```
다음과 같은 디렉토리 구조가 생성될 것입니다.

```
drawing
├── LICENSE
├── README.md
├── project.clj
└── src
    └── drawing
        └── core.clj
```

생성된 프로젝트의 디렉토리 구조에 특별한 것은 없습니다. 단순히 Leiningen에서 사용하는
관례(convention)를 따르고 있습니다. Leiningen을 이용해 클로저 앱을 빌드하고 실행하게
되는데, 이때 Leiningen은 기본적으로 위와 같은 방식으로 앱의 구조를 구성합니다.

- `project.clj`는 Leiningen 설정 파일입니다. 예를 들면, 프로젝트에서 이용하는 의존
  라이브러리들이나 , 클로저 프로그램이 실행될 때 최초로 호출되는 함수를 이 프로젝트
  파일에서 지정할 수 있습니다.

- `src/drawing/core.clj`는 클로저 코드입니다.


이 프로젝트에서는 [Quil](https://github.com/quil/quil)이라는 클로저 라이브러리를
사용하는데, 그림(또는 스케치)을 그리는데 사용합니다.

이제 Quil로 그린 그림을 실행해 봅시다. Nightcode를 실행한 후, Import 메뉴를 이용해 drawing
폴더를 찾아 클릭합니다. 그리고 나서 `src/drawing/core.clj` 파일을 엽니다.

우측 하단의 창에서 다음을 실행합니다.

1.  Run with REPL 메뉴를 클릭한다.
2.  Reload File 메뉴를 클릭한다.

Run with REPL 메뉴를 실행할 때 시간이 좀 걸릴 수 있습니다. 하단의 창에 `user=>` 프롬프트가
보이면 Reload 메뉴를 클릭합니다.

창이 하나 나타나고, 원이 벽에 부딪혀 튀어 다니는 모습을 볼 수 있을 겁니다.

창을 닫으려먼, 창의 상단 좌측의 닫기(X) 버튼을 누룹니다.  


## 프로젝트 수정하기

Quil을 이용해 다른 그림을 그려 봅시다. Nightcode의 좌측 디렉토리 트리 창에서 drawing
폴더를 선택한 후, 우측 창 상단에 있는 New File 메뉴를 클릭합니다.

![새로운 파일 생성하기](images/create-new-file.png)

파일의 이름을 `lines.clj`로 저장합니다.


## 프로젝트의 구성(organization)

프로그램이 복잡해지면, 코드를 별도의 디렉토리와 파일로 관리해줄 필요가 있습니다. 관련되는
함수와 데이터를 별도의 파일들에 묶어 코드를 관리하게 됩니다. 클로저에서는 각각의 파일이
*이름공간(namespace)*에 일대일로 대응됩니다. 그래서 각 파일의 맨 위에 이름공간을 *선언*해
주어야 합니다.

지금까지는 이름공간에 대해 신경쓸 필요가 없었지만, 함수명이나 데이터명 앞에 이름공간을
붙여주면, 서로 다른 이름공간에서 동일한 함수명이나 데이터명을 사용할 수 있습니다. 예를
들면, `my-special-namespace`라는 여러분의 이름공간에 `println` 함수를 만들어 사용하면,
클로저의 `println` 함수와 충돌하지 않게 됩니다. 즉, 클로저의 `println` 함수와 구별하기
위해, 함수 앞에 이름공간을 붙여 `my-special-namespace/println`처럼 사용할 수 있습니다.

`src/drawing/lines.clj` 파일에 이름공간을 지정해 봅시다. 파일을 열고 다음과 같이 입력해 줍니다.

```clojure
(ns drawing.lines)
```

위와 같이 선언하면, 여러분이 이 파일 안에서 정의한 모든 것들은 `drawing.lines`라는
이름공간에 저장됩니다.

다음으로 진행하기 전에, 상단의 메뉴에서 Save를 클릭합니다.


## 의존 라이브러리(Dependencies)

마지막으로, 프로젝트 파일에서 *의존 라이브러리들(dependencies)*을 지정해 봅시다. 의존
라이브러리는 여러분의 프로젝트에서 사용하고자 하는, 다른 사람들이 작성한 라이브러리를
의미합니다.

의존 라이브러리를 추가하려면, `project.clj` 파일을 열고 `:dependencies` 부분에 다음과 같이
해 줍니다.

```clj
:dependencies [[org.clojure/clojure "1.8.0"]
               [quil "2.4.0"]]
```

이 부분에 의존 라이브러리들이 나열됩니다. 이 프로젝트에서 필요한 모든 라이브러리들은 이미
포함되어 있습니다.

이 라이브러리들을 사용하려면, 해당 소스코드 파일에서 _require_해 주어야 합니다. 앞에서
만들었던 `src/drawing/lines.clj` 파일의 `ns` 부분을 다음과 같이 수정해 줍니다.

```clojure
(ns drawing.lines
   (:require [quil.core :as q]))
```

이후에, 원하는 라이브러리를 해당 소스 코드에서 호출할 수 있게 됩니다.

위의 코드에서 `ns`의 `:require`는 해당 이름공간을 메모리에 로드하는 일을
합니다. `:require`의 `:as` 부분은 로드한 이름공간에 *별칭(alias)*을 부여하는데, 이렇게
되면 이름공간 전체를 타이핑할 필요가 없게 됩니다. 예를 들면, `quil.core/fill` 대신에
`q/fill`처럼 사용할 수 있게 됩니다.

다음으로 넘어가기 전에 수정한 파일을 저장하는 것 잊지 마세요. 코드를 변경했을 때는 상단의
Save 메뉴를 클릭하세요.


## 첫 번째 프로그램

### Quil로 그리기

Quil은 그림과 애니메이션을 그리는 도구인 [Processing](https://processing.org/)을 클로저로
랩핑(warpping)한 라이브러리입니다. 앞으로 그림을 그릴 때, 이 Quil의 함수를 이용하게 됩니다.

함수는 다음과 같이 정의합니다.

```clojure
(defn draw []
   ; Do some things
   )
```

위의 함수 안에서 Quil이 제공하는 함수를 다음과 같이 사용합니다.

```clojure
   ; Call the quil background function
   (q/background 240)
```

위의 두 코드를 합치면 다음과 같습니다.

```clojure
(defn draw []
   ; Call the quil background function
   (q/background 240)
   )
```

Quil로 그림(Quil의 용어로는 sketch)을 그리려면, `setup`과 `draw`, `defsketch`를 정의해야
합니다. `setup` 함수에서는 그림을 그리기 전에 필요한 사전 설정 작업을 해줍니다.  `draw`
함수에서 실제 그리는 작업을 정의하는데, 이 함수는 Processing이 주기적으로 반복해서
호출하게 됩니다. `defsketch`는 그림 자체를 정의합니다. 그러면 이 함수들을 각각 정의하고,
무슨 일을 하는지 알아 봅시다.

lines.clj 파일의 ns 문 뒤에 다음의 코드를 추가합니다. 

```clojure
(defn setup []

  (q/frame-rate 30)

  (q/color-mode :rgb)

  (q/stroke 255 0 0))
```

위의 `setup` 함수에서는 그림을 그리기 전에 필요한 사전 설정 작업을 수행합니다. 먼저 Quil의
`frame-rate` 함수를 호출해, 화면에 초당 30번씩 그림을 그려줄 것을 요청하고
있습니다. 함수명 앞에 `q/`를 붙여 `quil.core`의 `frame-rate` 함수임을 표시합니다. 앞에서
`:as q`로 선언해 주었기 때문에, `quil.core/` 대신에 `q/`로 짧게 표현할 수 있게
됩니다. 라이브러리의 함수를 호출할 때는 `library-name/function-name` 방식으로 합니다.


다음으로 컬러 모드를 RGB 방식으로 지정합니다.

세 번째로, `stroke` 함수에서 그림을 그릴 때 라인의 색깔을 지정합니다. 코드 `255 0 0`은
빨간 색입니다. [RGB 색상 코드](http://xona.com/colorlist/)에서 색상 관련 정보를 참고할 수
있습니다.
 
lines.clj 파일의 `setup` 함수 뒤에 다음의 코드를 입력합니다.

```clojure
(defn draw []

  (q/line 0 0 (q/mouse-x) (q/mouse-y))

  (q/line 200 0 (q/mouse-x) (q/mouse-y))

  (q/line 0 200 (q/mouse-x) (q/mouse-y))

  (q/line 200 200 (q/mouse-x) (q/mouse-y)))
```

위에서 `line` 함수를 4번 호출하고 있습니다. 그리고 `line` 함수의 인수로 두 개의 함수
`mouse-x`와 `mouse-y`를 반복해서 호출하고 있습니다. 이 두 함수는 마우스의 현재의 2차원 x,
y 좌표를 얻어 오는 일을 합니다. `line` 함수는 4개의 인수를 받고 있는데, 실은 두 쌍의 x, y
좌표를 받고 있습니다. 첫 번째 x, y 좌표는 라인의 시작 좌표입니다. 두 번째 x, y 좌표는
라인의 끝 좌표입니다. 그래서 고정된 시작점 좌표 4개와, 마우스가 현재 위치한 한 개의 끝점
사이를 연결하는 4개의 선을 반복(1초에 30번씩)해서 그리게 됩니다.

```clojure
(q/defsketch hello-lines

  :title "You can see lines"

  :size [500 500]

  :setup setup

  :draw draw

  :features [:keep-on-top])
```

`defsketch`에서는 그림(또는 스케치) 자체를 정의합니다. 여기에서 그림의 제목이나 크기같은,
그림의 속성들을 정의할 수 있습니다. 그리고 setup 함수와 draw 함수의 이름을 지정해 줄 수
있는 항목이 있습니다. 여기에 위에서 정의한 setup 함수와 draw 함수의 이름을 정확히 지정해
주어야 합니다. 마지막 줄에서는 그림이 그려지는 창이 다른 창들보다 항상 위에 나타나도록
지정하고 있습니다.

이제 Run with REPL - Reload File 메뉴를 클릭하면, 파일을 실행해 그림을 보여주게 됩니다.

그림이 보이지 않으면, Save file - Stop - Run with REPL - Reload File 메뉴를 차례로 눌러
재실행해 보세요.


### 연습 문제: 무지개색 라인

여러분의 그림을 다음과 같이 수정해 보세요.

* 라인들의 색깔들을 다른 색상으로 그린다.
* 그림의 제목도 다르게 바꾼다.
* 라인의 시작 좌표를 다르게 해 본다.

보너스: 라인 색깔 4개를 모두 각각 다른 색깔로 바꾸어 본다.

보너스 #2: 마우스의 위치에 따라 라인들의 색깔을 변화시킨다.

힌트: 함수에 대한 설명은 [Quil API](http://quil.info/api)를 참고한다.

힌트: 클로저 브릿지 커리큘럼에서 사용하고 있는 함수들에 대한 설명은 the [Quil
Cheatsheet](https://github.com/ClojureBridge/curriculum/blob/gh-pages/outline/cheatsheet-quil.md)를
참고한다.
