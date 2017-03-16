Quil로 멋진 애니메이션 만들기
===========================================

    이 글은 최근에 클로저 브릿지 워크샵에 참석한 클라라의 이야기입니다.
    클라라는 워크샵에서 클로저와 클로저 코드 작성법을 배웠는데, 클로저가 
    아주 훌륭한 함수형 언어라는 인상을 받았습니다.
    아울러 클로저로 작성된 Quil이라는 아주 흥미로운 라이브러리가 있다는 것을 듣게 되었고,
    그 사용법을 익히면서 아주 멋진 애니메이션을 만들고 싶다는 생각을 갖게 되었습니다.

    클라라가 자신의 Quil 애플리케이션을 개발한 과정을 이 글에서 설명합니다.  
       
## 단계 1. 푸른 배경색에 눈송이 보이기

클라라는 자신의 최초의 애니메이션 앱을 구상하면서 어디에서부터 시작해야할지 생각
중입니다. 그때 그녀의 마음 속에 작은 아이디어가 하나 떠오릅니다. "푸른 색 바탕에 하얀
눈송이를 보여주면 좋겠는 걸." 그런데 배경색을 푸른 색으로 하고, 그 위에 눈송이 하나를 보여
주려면 어떻게 해야 할지 궁금해졌습니다. 하지만, 클라라는 그 방법을 어떻게 찾아야 할지는
이미 알고 있었습니다. 그것은 바로
 
1. Quil의 API 문서 웹사이트로 간다.
2. 그곳에서 관련 내용을 찾아 본다.

이 방법을 따라 클라라는 Quil API 웹사이트 [http://quil.info/api](http://quil.info/api)에
가서, [Loading and Displaying](http://quil.info/api/image/loading-and-displaying) 부분을
찾았습니다. [background-image](http://quil.info/api/color/setting#background-image) 함수도
발견합니다.

추가로 구글 검색을 한 후에 StackOverflow에서 [Load/display image in clojure with
quil](http://stackoverflow.com/questions/18714941/load-display-image-in-clojure-with-quil)라는 질문을 발견했는데, 도움이 많이 되는 글이었습니다. 또한 클로저 브릿지의 [Quil and
Processing
Resources](https://github.com/ClojureBridgeSeoul/drawing#quil-and-processing-resources) 부분도
참고했습니다. 그곳에 나열되어 있는 웹사이트들을 훑어 보다가, 재미있는 크리스마스 트리 예제
[xmas-tree.clj](https://github.com/quephird/fun-with-quil/blob/master/src/fun_with_quil/animations/xmas-tree.clj)를
하나 발견했는데, 파일에서 읽어 온 크리스마스 트리를 창에 보여주는 예제였습니다. 이
유용한 검색 결과에 "이것이 바로 내가 원하던 거야"라며 클라라는 기쁨을 감추지 못합니다.
  
이제 1 단계를 이루는데 필요한 정보는 충분히 수집한 셈입니다.

그런데 그녀는 클로저 브릿지 워크샵에서 Quil 앱 튜토리얼인 [Quil을 이용해 첫 번째 프로그램
만들기](https://github.com/ClojureBridgeSeoul/drawing/blob/master/curriculum/first-program.md)
프로젝트를 본 적이 있었습니다. 그래서 이 `drawing` 프로젝트를 그대로 이용해 자신의
애니메이션 앱을 만들겠다고 마음을 정합니다.

### 단계 1-1: 새로운 소스 파일 생성하기

클라라는 `src/drawing` 디렉토리 아래에 `practice.clj`라는 이름의 파일 하나를 새로
추가합니다. 이때의 디렉토리 구조는 다음과 같습니다.

```
drawing
├── LICENSE
├── README.md
├── project.clj
└── src
    └── drawing
        ├── core.clj
        ├── lines.clj
        └── practice.clj      <-- this file is added in step 1-1
```

### 단계 1-2: 이름공간 추가하기

클로저 소스 코드에서는 먼저 이름공간을 선언해 주어야 합니다. 그래서 클라라는 `lines.clj`
파일의 맨 앞부분에 있는 `ns`문을 복사해 붙여 놓습니다. 하지만, 새로운 파일의 이름이
`practice`이기 때문에, 그 이름공간을 `drawing.lines`에서 `drawing.practice`로 변경합니다.

이때 `practice.clj`는 다음과 같습니다.

```clojure
(ns drawing.practice
  (:require [quil.core :as q]))
```

### 1-3 단계: 기본적인 Quil 코드 추가하기

기본적인 Quil 코드는 `setup`과 `draw` 함수, 그리고 앱을 정의하는 `defsketch` 매크로를 갖게
됩니다. Quil의 이 규칙을 따라 클라라는 `practice.clj` 파일에 다음의 코드를 추가합니다.

이때의 `practice.clj`는 다음과 같습니다.

```clojure
(ns drawing.practice
  (:require [quil.core :as q]))

;; setup and draw functions and q/defsketch are added in step 1-3
(defn setup [])

(defn draw [])

(q/defsketch practice
  :title "Clara's Quil practice"
  :size [500 500]
  :setup setup
  :draw draw
  :features [:keep-on-top])
```

### 1-4 단계: 눈송이와 배경 이미지 다운로드하기

Quil API와 StackOverflow의 질문을 보면서, 클라라는 이미지 파일의 위치가 중요하다는 것을
알게 되었습니다. 그래서 `drawing` 디렉토리 아래에 `images`라는 새로운 디렉토리를 만들고,
[두 개의 이미지 파일](https://github.com/ClojureBridgeSeoul/drawing/tree/master/images)을
그곳에 둡니다.

이제 프로젝트의 디렉토리 구조는 다음과 같습니다.

```
drawing
├── LICENSE
├── README.md
├── images
│   ├── blue_background.png      <-- added in step 1-4
│   └── white_flake.png          <-- added in step 1-4
├── project.clj
└── src
    └── drawing
        ├── core.clj
        ├── lines.clj
        └── practice.clj
```

### 1-5 단계: 프레임웍 추가하기

이제 이미지들이 준비되었으니, 다음 단계는 Quil API를 이용해 코딩하는 것입니다.

클라라는 크리스마스 트리 예제를 살펴 본 후, `:require` 부분에 `[quil.middleware :as m]`를
추가해야 한다는 사실을 알게 되었는데, 이것이 그녀에게는 새로운 경험이었습니다. 그녀는
`quil.middleware`가 무엇이고 어떻게 사용해야 하는지 알아보기 위해, [Functional mode (fun
mode)](https://github.com/quil/quil/wiki/Functional-mode-(fun-mode)) 부분을 살펴
보았습니다.

문서를 다 읽고 나서, 그녀는 다음과 같이 중얼거립니다. "아, fun 모드, 이름 한번 잘 지었네!
그렇다면 지금 내가 해야 할 일은 `practice.clj` 파일에 다음을 추가하는 일이네."

1. `ns` 문에 `[quil.middleware :as m]`을 추가한다.
2. `q/defsketch` 문에 `:middleware [m/fun-mode]`를 추가한다.
3. `draw` 함수에 `state` 인수를 추가한다.

이때 `practice.clj`는 다음과 같습니다.

```clojure
(ns drawing.practice
  (:require [quil.core :as q]
            [quil.middleware :as m]))  ;; this line is added in step 1-5

(defn setup [])

(defn draw [state])                    ;; argument state is added in step 1-5

(q/defsketch practice
  :title "Clara's Quil practice"
  :size [500 500]
  :setup setup
  :draw draw
  :features [:keep-on-top]
  :middleware [m/fun-mode])             ;; this line is added in step 1-5
```

### 1-6 단계: 눈송이와 배경 이미지를 로드하기

클라라는 이미지를 로드하려면 다음과 같이 해야 한다는 것을 이미 알고 있습니다.

1. `setup` 함수에서, 이미지들을 로드하고 그것을 **상태값**으로 반환한다.
2. `draw` 함수에서, `state` 인수로부터 그 내용을 찾아 화면에 그려준다.

그래서 그녀는 `practice.clj` 파일의 `setup`과 `draw` 함수에 코드를 몇 줄
추가합니다. 이미지 파일의 이름을 지정할 때, 실제 디렉토리 구조도 포함해야 한다는 것을 알고
있기에, 이미지 파일명을 입력할 때 주의를 기울입니다.
  
### 1 단계의 `practice.clj`

이제 `practice.clj`는 다음과 같습니다.
 
```clojure
(ns drawing.practice
  (:require [quil.core :as q]
            [quil.middleware :as m]))

(defn setup []
  ;; these two lines, a map (data structure) is added in step 1-6
  {:flake (q/load-image "images/white_flake.png")
   :background (q/load-image "images/blue_background.png")}
  )

(defn draw [state]
  ;; q/background-image and q/image functions are added in step 1-6
  (q/background-image (:background state))
  (q/image (:flake state) 200 10)
  )

(q/defsketch practice
  :title "Clara's Quil practice"
  :size [500 500]
  :setup setup
  :draw draw
  :features [:keep-on-top]
  :middleware [m/fun-mode])
```

클라라가 이 코드를 실행하자, 다음의 이미지가 보입니다.

![step 1 screenshot](images/step-1.png)

와우! 그녀가 해냈습니다!


#### [보너스] 구조분해(destructuring)

클로저에는
[구조분해](http://clojurebridge.github.io/community-docs/docs/clojure/destructuring/)라는
멋진 기능이 있습니다. 함수의 인수에 이 구조분해를 적용하면, `draw` 함수를 다음과 같이
작성할 수 있습니다.
 
```clojure
(defn draw [{flake :flake background :background}]
  (q/background-image background)
  (q/image flake 200 10))
```

클로저 프로그래머들은 이 편리한 기능을 자주 사용합니다.


## 2 단계. 눈송이를 떨어지게 만들기

클라라는 파란 배경에 하얀 눈송이 이미지를 그릴 수 있는 것에 만족했지만, 한편 따분한 느낌을
주는 것도 사실이었습니다. 그래서 다음 단계로 눈송이가 떨어지는 것처럼 움직이게 만들고
싶었습니다. 그러자면 더 많은 Quil API에 대한 지식과 구글 검색이 필요했습니다.

검색을 해 본 결과, 특정 이미지를 움직이게 만드는 것은 **애니메이션**이라
불린다는 것을 알게 되었습니다. 기본적인 아이디어는 다음과 같습니다.

- 특정 위치에 이미지를 그린다.
- 이미지의 위치를 변경한다.
- 변경된 위치에 이미지를 다시 그린다.

소위 애니메이션이라는 것은 위의 단계들을 계속해서 반복하는 것입니다.


### 2-1 단계: `y` 좌표값 변경하기

이때 클라라에게 든 생각은, 눈송이가 떨어지는 것처럼 눈송이가 움직이러면 어떻게
프로그래밍해야 하는가입니다.

눈송이를 그리는 일은 Quil의
[API](http://quil.info/api/image/loading-and-displaying#image)에 설명된대로 `image` 함수를
사용했습니다.
 
눈송이를 그리기 위해 클라라가 사용했던 `x`와 `y` 좌표값은 좌측 상단으로부터 200과 10의
위치였습니다. 하지만, 이미지가 떨어지는 것처럼 보이려면, 시간이 경과함에 따라 y 좌표값을
증가시켜야 합니다.

![x and y](images/x-y-grid.png)

프로그래밍의 관점에서는, 눈송이가 떨어지는 것처럼 움직인다는 것은 다음을 의미합니다.

1. 초기값을 설정한다. 예를 들면, `(x, y) = (200, 10)` 
2. 배경을 먼저 그리고, 뒤이어 눈송이를 그린다.
3. 좌표값 `y`를 증가시켜 상태값을 변경한다. 예를 들면, `(x, y) = (200, 11)`
4. 다시 배경을 먼저 그리고, 뒤이어 눈송이를 그린다.
5. `y` 좌표값을 증가시키며, 2와 3의 과정을 반복한다.

현 단계에서 변경해야 하는 상태값은 `y` 좌표값입니다. 그런데 어떻게 해야 `y`의 값을 1만큼
증가시킬 수 있을까요?

클로저에는 `inc`라는 함수가 있는데, 이 함수를 사용하면 됩니다.

`y`의 값을 변경하려면 다음과 같이 합니다.
- `setup` 함수가 반환하는 맵에 `y-param`을 추가한다. 이 함수가 반환하는 값이 **상태값**이 된다.  
    ```clojure
    {:flake (q/load-image "images/white_flake.png")
     :background (q/load-image "images/blue_background.png")
     :y-param 10}
    ```

-  `y`의 값을 1씩 증가시키는 `update` 함수를 추가한다.

    ```clojure
    (defn update [state]
      ;; updating y paraemter by one
      (update-in state [:y-param] inc))
    ```

-  `q/defsketch` 매크로 내에 `update` 함수를 지정한다.

    ```clojure
    (q/defsketch practice
      :title "Clara's Quil practice"
      :size [500 500]
      :setup setup
      :update update
      :draw draw
      :features [:keep-on-top]
      :middleware [m/fun-mode])
    ```

### 2-2 단계: 변경된 위치에 이미지 그리기

지금까지 `y` 좌표값을 변경하는 기능을 추가했습니다. 하지만, 그것만으로는 눈송이가 떨어지는
효과를 내는데 충분하지 않습니다. 추가적으로 변경된 위치에 눈송이를 그리는 작업을 수행해
주어야 합니다.

클라라는 `draw` 함수를 변경해서, `q/image`가 변경된 `y`의 좌표값을 반영하도록 합니다.
 
```clojure
(defn draw [state]
  ;; drawing blue background and a snowflake on it
  (q/background-image (:background state))
  (q/image (:flake state) 200 (:y-param state)))
```

그녀는 `setup` 함수에 `(q/smooth)` 코드를 하나 더 추가합니다. 이 함수는 애니메이션을 더
부드럽게 그려줍니다.


### 2 단계 2의 `practice.clj`

이제 `practice.clj`는 다음과 같습니다.

```clojure
(ns drawing.practice
  (:require [quil.core :as q]
            [quil.middleware :as m]))

(defn setup []
  (q/smooth)                                      ;; added in step 2-2
  {:flake (q/load-image "images/white_flake.png")
   :background (q/load-image "images/blue_background.png")
   :y-param 10}                                   ;; added in step 2-1
  )

;; update function is added in step 2-1
(defn update [state]
  (update-in state [:y-param] inc))

(defn draw [state]
  (q/background-image (:background state))
  (q/image (:flake state) 200 (:y-param state))    ;; changed in step 2-2
  )

(q/defsketch practice
  :title "Clara's Quil practice"
  :size [500 500]
  :setup setup
  :update update                                   ;; added in step 2-1
  :draw draw
  :features [:keep-on-top]
  :middleware [m/fun-mode])
```
클라라가 이 코드를 실행하자, 눈송이가 떨어지는 모습이 보입니다.


## 3 단계. 눈송이가 계속해서 떨어지게 만들기

클라라는 좋은 Quil 앱을 만들었지만, 눈송이가 바닥 아래로 사라지면 그것으로
끝입니다. 창에는 파란색 배경만이 남아있을 뿐입니다. 그래서 계속해서 눈송이가 내리게 하고
싶어집니다.

이를 달리 표현하자면, 눈송이가 바닥에서 사라지게 되면, 눈송이를 다시 위에 위치시킨 후
떨어지게 만들고 싶습니다.

이것을 프로그래밍하려면 어떻게 해야 할까요?

만약 `y`의 값이 창의 높이보다 크면 `y`의 값을 `0`의 상태로 되돌립니다. 그렇지 않은
경우에는 `y`의 값을 1씩 증가시킵니다. 그래서 `y`의 값을 증가시키는 경우의 수가 두 가지가
됩니다.

클라라는 클로저 워크샵의 [제어
구조](http://clojurebridge.github.io/curriculum/outline/flow_control.html)에서 `if`의
사용법을 기억했습니다.  그 `if`가 이 두 가지 경우를 다음과 잘 처리해 주었습니다.

```clojure
(defn update [state]
  (if (>= (:y-param state) (q/height)) ;; y-param is greater than or equal to image height?
    (assoc state :y-param 0)           ;; true - get it back to the 0 (top)
    (update-in state [:y-param] inc)   ;; false - update y paraemter by one
    ))
```

그래서 그녀는 `update` 함수에서 `if`를 사용해 눈송이가 창의 위쪽에 다시 위치할 수 있도록
해 줍니다.


### 3 단계의`practice.clj`

이제 `practice.clj`는 다음과 같습니다.

```clojure
(ns drawing.practice
  (:require [quil.core :as q]
            [quil.middleware :as m]))

(defn setup []
  (q/smooth)
  {:flake (q/load-image "images/white_flake.png")
   :background (q/load-image "images/blue_background.png")
   :y-param 10})

(defn update [state]
  ;; these three lines were added in step 3
  (if (>= (:y-param state) (q/height))
    (assoc state :y-param 0)
    (update-in state [:y-param] inc)))

(defn draw [state]
  (q/background-image (:background state))
  (q/image (:flake state) 200 (:y-param state)))

(q/defsketch practice
  :title "Clara's Quil practice"
  :size [500 500]
  :setup setup
  :update update
  :draw draw
  :features [:keep-on-top]
  :middleware [m/fun-mode])
```

이제 클라라는 눈송이가 바닥에서 사라진 후에, 창의 위쪽에 다시 나타나는 모습을 보게 됩니다.


## 4 단계. 여러 개의 눈송이가 떨어지게 만들기

이제 클라라는 눈송이가 여러 개 떨어졌으면 좋겠다는 생각을 하게 됩니다. 지금은 한 개의
눈송이만 떨어지고 있는데, 더 추가해 보면 어떨까요? 여러 개의 눈송이들이 좌측과 우측에서 하나
이상씩 떨어지는 모습을 보고 싶습니다.

다시 한번 그녀는 자신의 생각을 프로그래밍 코드로 표현해야 합니다. 그녀는 이미 작성한
코드를 되돌아 본 후, 여러 개의 이미지를 서로 다른 `x` 좌표값에 그리면 되겠구나 하고
생각합니다. 가장 손쉬운 방법은 `(q/image (:flake state) 200 (:y-param state)` 코드를
복사해 붙여 놓은 후, 각기 다른 `x` 좌표값을 주는 것입니다. 예를 들면 다음과 같습니다.
 
```clojure
(q/image (:flake state) 10 (:y-param state))
(q/image (:flake state) 200 (:y-param state))
(q/image (:flake state) 390 (:y-param state))
```

하지만 클라라에게는 이 방법이 그리 좋아 보이지 않습니다. 그녀는 클로저에 관해 이미 많이
배웠기에 알고 있는 지식을 활용해보고 싶습니다.

먼저 그녀는 여러 개의 `x` 좌표값을 보관할 방법을 생각합니다. [자료
구조](http://clojurebridge.github.io/curriculum/outline/data_structures.html)의
벡터 편을 기억하고는, 이 경우에 적합하다고 생각합니다.

다음은 그녀가 더 많은 눈송이들을 추가한 방법입니다.

1. `def`를 이용해, 여러 개의 `x` 좌표값을 담은 벡터에 `x-params`라는 이름을 부여한다.

    ```clojure
    (def x-params [10 200 390]) ;; x parameters for three snowflakes
    ```

2. `doseq`을 이용해, `x-params`에 담긴 요소의 개수만큼 눈송이들을 그린다.

    ```clojure
    (doseq [x x-params]
      (q/image (:flake state) x (:y-param state)))
    ```

* [doseq](http://clojurebridge.github.io/curriculum/outline/sequences.html#/3)을 참고하세요.

### 4 단계의 `practice.clj`

이때 `practice.clj`는 다음과 같습니다.

```clojure
(ns drawing.practice
  (:require [quil.core :as q]
            [quil.middleware :as m]))

(def x-params [10 200 390])                      ;; added in step 4

(defn setup []
  (q/smooth)
  {:flake (q/load-image "images/white_flake.png")
   :background (q/load-image "images/blue_background.png")
   :y-param 10})

(defn update [state]
  (if (>= (:y-param state) (q/height))
    (assoc state :y-param 0)
    (update-in state [:y-param] inc)))

(defn draw [state]
  (q/background-image (:background state))
  (doseq [x x-params]                              ;; two lines were changed
    (q/image (:flake state) x (:y-param state))))  ;; in step 4

(q/defsketch practice
  :title "Clara's Quil practice"
  :size [500 500]
  :setup setup
  :update update
  :draw draw
  :features [:keep-on-top]
  :middleware [m/fun-mode])
```

클라라는 세 개의 눈송이가 떨어지는 것을 보고 "그래, 이거야!"라고 외칩니다. 


## 단계 5. 눈송이들을 다른 속도로 떨어지게 하기

자신이 만든 앱이 좋아 보였지만, 클라라는 아직 꽤 만족스럽지는 못하다는 느낌을 받습니다. 창
안의 새 개의 눈송이들이, 마치 로봇들이 걷듯이 같은 속도로 떨어지고 있기 때문입니다.
자연스럽게 보이지는 않습니다. 그래서 눈송이들이 서로 다른 속도로 떨어지게 민들고 싶습니다.
   
프로그래밍의 관점에서 볼 때, 여기서의 문제는 모든 눈송이들이 같은 `y` 좌표값을 갖고
있다는데 있습니다. 이것을 감안하면, 각기 다른 `y` 좌표값을 사용하면 문제가 해결될 것
같은데 어떻게 해야 할까요?

### 5-1 단계: 눈송이의 `y` 좌표값과 속도를 맵 데이터형으로 표현하기

`x`의 좌표값들을 벡터로 표현했기 때문에, 서로 다른 `y` 좌표값들의 경우에도 벡터를 사용하는
것이 좋을 것 같아 보입니다. 하지만, 이 경우에는 각각의 눈송이들이 높이와 속도라는 두 개의
값을 사용해야 하기 때문에, 단순히 벡터를 이용하는 것은 바람직하지 않습니다. 각각의 눈송이들이 
떨어지는 속도도 표현할 수 있어야 하기 때문입니다.

그래서 클라라는 클로저 브릿지 커리큘럼으로 되돌아가, [자료
구조](http://clojurebridge.github.io/curriculum/outline/data_structures.html) 부분을 다시
살펴 봅니다. 그곳에서 멥이라는 데이터 구조가 여러 개의 파라미터를 저장하기에 좋다는 것을
확인합니다. 그리고 맵과 관련된 여러 예제들을 본 후, 한 개의 값을 가진 `y-param`을, 세 개의
맵을 담은 한 개의 벡터로 변경합니다. 아울러 `:y-param` 키워드를 `:y-params`로
바꿉니다. 이제 초기 **상태값**은 다음과 같이 바뀌었습니다.

```clojure
{:flake (q/load-image "images/white_flake.png")
 :background (q/load-image "images/blue_background.png")
 :y-params [{:y 10 :speed 1} {:y 150 :speed 4} {:y 50 :speed 2}]}
```

위의 데이터 구조는, 하나의 벡터 안에 세 개의 맵들이 있고, 그 벡터를 더 큰 외부의 맵 안에
담고 있습니다. 그런데 한 가지 문제점은, 이제 `update` 함수가 복잡해질 수 밖에 없다는 데
있습니다. 그녀는 각 눈송이의 `y` 좌표값들을 변경할 때, 한 개의 벡터 안에 위치한, 세 개의
맵 안의 모든 `y` 값들을 변경해야만 합니다.


### 5-2 단계: 벡터 안의 맵들의 값을 변경하기

맵과 벡터를 동시에 생각하는 것은 그녀에게 너무 혼란스러운 일이라, 먼저 맵에 대해서만
생각하기로 합니다. 각각의 맵은, 다음과 같이 초기 **상태값**으로 `y`와 `speed` 항목을 갖고
있습니다.

```clojure
{:y 150 :speed 4}
```

위의 값이 변경될 때, `speed`의 값이 `y`의 값에 더해져야 합니다. 그 결과로 맵은 다음과 같이
바뀌어야 합니다.

```clojure
{:y 154 :speed 4}
```

이런 식으로 맵을 변경하기 위해서, 클라라는 다음과 같이 `update-y` 함수를 추가했습니다. 

```clojure
(defn update-y
  [m]
  (let [y (:y m)
        speed (:speed m)]
    (if (>= y (q/height))           ;; y is greater than or equal to image height?
      (assoc m :y 0)                ;; true - get it back to the 0 (top)
      (update-in m [:y] + speed)))) ;; false - add y value and speed
```

#### [보너스] 구조분해

클로저의 구조분해 기능을 이용하면, `update-y` 함수를 다음과 같이 작성할 수도 있습니다.
 
```clojure
(defn update-y
  [{y :y speed :speed :as m}]
  (if (>= y (q/height))
    (assoc m :y 0)
    (update-in m [:y] + speed)))
```

이렇게 되면, 위의 코드에서처럼 `let` 바인딩을 사용하지 않아도 됩니다. 


### 5-3 단계: 벡터 내의 맵들 변경하기

다음 단계는 `update-y` 함수를 이용해 벡터 내의 맵들을 변경하는 것입니다. 이 함수를
작성하기 전에, 클라라는 벡터의 내용을 어떻게 변경할 것인가에 집중하고자, 문제를 단순화해
접근하기로 합니다. 그녀는 함수와 벡터를 인수로 주고, 벡터 내의 요소들을 그 함수에 일일이
적용시키는 [`map`](http://clojurebridge.github.io/curriculum/outline/functions.html#/9) 함수를
기억합니다.

예를 들면,

```clojure
(map inc [1 2 3]) ;=> (2 3 4)
```

> 클로저는 `map` 함수와 `map` 데이터형을 갖고 있다.
> 이것은 혼란을 초래할 수 있으므로, 주의해서 둘 사이의 차이를 구별할 줄 알아야 한다.
> 파이썬에서는 함수는 `map`이고, 데이터형은 딕셔너리(dictionary)이다.
> 루비에서는 함수는 `map`이고, 데이처형은 해시(hash)이다. 

그녀는 이 함수를 REPL에서 테스트해 봅니다.

```clojure
(defn update-test
  [m]
  (let [y (:y m)
        speed (:speed m)]
    (if (>= y 500)
      (assoc m :y 0)
      (update-in m [:y] + speed))))

(def y-params [{:y 10 :speed 1} {:y 150 :speed 4} {:y 50 :speed 2}])

(map update-test y-params)
;=> ({:y 11, :speed 1} {:y 154, :speed 4} {:y 52, :speed 2})
```

잘 동작하는 것으로 보입니다. 그래서 그녀는 `practice.clj` 파일로 되돌아가, `update` 함수를
수정하기로 합니다. 이 함수는 **상태값**을 맵으로 반환하는데, 이 맵의 `:y-params` 키는
벡터를 값으로 갖습니다. 다음은 그 결과로 만들어진 `update` 함수입니다.

```clojure
(defn update [state]
  (let [y-params (:y-params state)]
    (assoc state :y-params (map update-y y-params))))
```


### 5-4 단계: `draw` 함수 수정하기

또다른 문제는 벡터 내의 맵들에 있는 값을 반영할 수 있도록 `draw` 함수를 수정하는
일입니다. 클라라는 클로저에서 반복적인 일을 수행하는데 사용할 수 있는 함수들을 몇 개 이미
알고 있습니다. 이것들 중에 이미지를 반복적으로 그리기 위해 `dotimes`와 `nth`를 사용하기로
합니다. `nth` 함수는 커리큘럼 중에 [자료
구조](http://clojurebridge.github.io/curriculum/outline/data_structures.html#/6)에서
다뤘습니다.

이 경우에는 그녀는 이미 정확히 3개의 눈송이들이 있다는 것을 알고 있으므로, 다음과 같이
코드를 변경해 3개의 눈송이를 그립니다.

```clojure
(let [y-params (:y-params state)]
    (dotimes [n 3]
      (q/image (:flake state) (nth x-params n) (:y (nth y-params n)))))
```

### 5 단계의 `practice.clj` 

이제 `practice.clj` 전체 코드는 다음과 같습니다.

```clojure
(ns drawing.practice
  (:require [quil.core :as q]
            [quil.middleware :as m]))

(def x-params [10 200 390])

(defn setup []
  (q/smooth)
  {:flake (q/load-image "images/white_flake.png")
   :background (q/load-image "images/blue_background.png")
   :y-params [{:y 10 :speed 1} {:y 150 :speed 4} {:y 50 :speed 2}]})  ;; changed in step 5-1

;; update-y function was added in step 5-2
(defn update-y
  [m]
  (let [y (:y m)
        speed (:speed m)]
    (if (>= y (q/height))
      (assoc m :y 0)
      (update-in m [:y] + speed))))

(defn update [state]
  (let [y-params (:y-params state)]                   ;; update function
    (assoc state :y-params (map update-y y-params)))) ;; was changed in step 5-3

(defn draw [state]
  (q/background-image (:background state))
  (let [y-params (:y-params state)]   ;; three lines below were changed in step 5-4
    (dotimes [n 3]
      (q/image (:flake state) (nth x-params n) (:y (nth y-params n))))))

(q/defsketch practice
  :title "Clara's Quil practice"
  :size [500 500]
  :setup setup
  :update update
  :draw draw
  :features [:keep-on-top]
  :middleware [m/fun-mode])
```

그녀가 이 코드를 실행했을 때, 3개의 눈송이가 서로 다른 속도로 끊임없이 떨어집니다. 더
자연스러워 보입니다.
 

## 단계 6. 리팩토링(refactoring: 코드 다듬기) 하기

클라라는 `practice.clj`를 보면서, 코드가 꽤 많이 늘어났다고 생각합니다.

코드를 처음부터 끝까지 면밀히 검토하면서, `x-params`는 **상태값*의 일부로 들어갈 수
있겠다고 생각합니다. 예를 들면 다음과 같이 말입니다.

```clojure
[{:x 10 :y 10 :speed 1} {:x 200 :y 150 :speed 4} {:x 390 :y 50 :speed 2}]
```

그녀는 이 새로운 데이터 구조가 눈송이들 각각의 상태 관리를 더 쉽게 해준다고 생각합니다.

이 새로운 데이터 구조를 사용하기 위해, 그녀는 초기 상태값에 `x` 좌표값을 포함해 반환하도록
`setup` 함수를 변경합니다. 아울러 `:y-params` 키워드를 `:params`로 변경합니다.
 
```clojure
{:flake (q/load-image "images/white_flake.png")
   :background (q/load-image "images/blue_background.png")
   :params [{:x 10  :y 10  :speed 1}
            {:x 200 :y 150 :speed 4}
            {:x 390 :y 50  :speed 2}]}
```

클라라는 `update-y` 함수를 살펴보고, 아무 것도 수정하지 않고 있는 그대로 남겨 두기로
결정합니다. `:x` 키와 그에 대응하는 값의 존재가 `y` 값을 변경하는 데에 아무런 영향도
미치고 있지 않기 때문입니다. 이 `update-y` 함수는 변경된 `y`값을 포함한 3 개의 키-값 쌍을
담은 맵을 반환합니다.
 
`update` 함수의 경우에는 어떤가요? 이 함수는 약간 수정할 필요가 있는데, 키명이
`:y-params`에서 `:params`로 바뀌었기 때문입니다.

```clojure
(defn update [state]
  (let [params (:params state)]
    (assoc state :params (map update-y params))))
```

`draw` 함수의 경우에는, `x` 값을 뽑아내는 방법이 달라져야 하기 때문에, 더 많이 수정해야
합니다. 처음에 클라라는 `dotimes` 함수를 다음과 같이 수정했습니다.

```clojure
(let [params (:params state)]
  (dotimes [n 3]
    (q/image (:flake state) (:x (nth params n)) (:y (nth params n)))))
```

하지만, `(nth params n)`라는 코드가 두 번이나 등장하고 있습니다.  반복을 피할 수 있는
방법이 있지 않을까 하고 그녀는 생각합니다. 답은 의외로 쉬었습니다. 바로 `dotimes` 함수
안에서 `let` 바인딩을 사용하는 것입니다. `let`을 사용해 코드를 다음과 같이 수정했습니다.
 

```clojure
(let [params (:params state)]
  (dotimes [n 3]
    (let [param (nth params n)]
      (q/image (:flake state) (:x param) (:y param)))))
```

마지막 라인이 더 깔끔해졌습니다!

### 단계 6의 `practice.clj`

이제 `practice.clj`는 다음과 같습니다.

```clojure
(ns drawing.practice
  (:require [quil.core :as q]
            [quil.middleware :as m]))

(defn setup []
  (q/smooth)
  {:flake (q/load-image "images/white_flake.png")
   :background (q/load-image "images/blue_background.png")
   :params [{:x 10  :y 10  :speed 1}                       ;; changed in step 6
            {:x 200 :y 150 :speed 4}                       ;;
            {:x 390 :y 50  :speed 2}]})                    ;;

(defn update-y
  [m]
  (let [y (:y m)
        speed (:speed m)]
    (if (>= y (q/height))
      (assoc m :y 0)
      (update-in m [:y] + speed))))

(defn update [state]
  (let [params (:params state)]                    ;; changed to params in step 6
    (assoc state :params (map update-y params))))  ;;

(defn draw [state]
  (q/background-image (:background state))
  (let [params (:params state)]                            ;; changed in step 6
    (dotimes [n 3]                                         ;;
      (let [param (nth params n)]                          ;;
        (q/image (:flake state) (:x param) (:y param)))))) ;;

(q/defsketch practice
  :title "Clara's Quil practice"
  :size [500 500]
  :setup setup
  :update update
  :draw draw
  :features [:keep-on-top]
  :middleware [m/fun-mode])
```

단계 5에서와 같은 결과를 얻지만, 코드는 더 깔끔해졌습니다. 이런 종류의 작업을
"리팩토링"이라고 합니다.


## 단계 7. 눈송이들을 좌우로 휘날리며 떨어지게 하기

클라라는 클로저 코딩에 훨씬 더 익숙해졌습니다. 아울러 그녀의 Quil 앱도 훨씬 더
멋있게 변했습니다.

눈송이들이 다른 속도로 떨어지게 만든 것은 멋진 일이었습니다. 하지만 그녀의 마음에 들지
않았던 것은, 눈송이들이 수직으로 떨어지고 있다는 점입니다. 이것은 행진하는 로봇들의
모습보다는 훨씬 낫지만, 눈송이들을 좌우로 흔들리며 떨어지게 한다면 더 멋있게 보일 것
같았습니다.

프로그래밍의 관점에서, 이것은 `x` 좌표값을 변경할 때, 그 값을 늘려주거나 또는 감소시키
주는 것입니다. 이것은 눈송이가 다음의 그림에서와 같은 궤적을 그리도록, `y` 좌표값
뿐만아니라 `x` 좌표값도 변경해 주어야 한다는 것을 의미합니다.

![하강 곡선](images/curve.png)

이것은 그녀에게는 큰 도전 과제입니다.


### 7-1 단계: 초기 **상태값**에 swing 파라미터 추가하기

클라라는 `y` 좌표값에 대한 경험으로부터, 이 세 `x` 좌표값을 포함하고 있는 각각의 맵에
`swing` 파라미터를 추가하면, 좌우로 흔들리는 모습을 구현할 수 있겠다는 생각을 갖게
되었습니다. 그래서 초기 **상태값**을 다음과 같이 변경합니다.
   
```clojure
[{:x 10  :swing 1 :y 10  :speed 1}
 {:x 200 :swing 3 :y 100 :speed 4}
 {:x 390 :swing 2 :y 50  :speed 2}]
```

`swing` 파라미터는 하강 곡선의 좌우 흔들림 값을 지정합니다.

![곡선의 좌우 흔들림](images/curve-range.png)


수정된 `setup` 함수는 다음과 같습니다.

```clojure
(defn setup []
  (q/smooth)
  {:flake (q/load-image "images/white_flake.png")
   :background (q/load-image "images/blue_background.png")
   :params [{:x 10  :swing 1 :y 10  :speed 1}
            {:x 200 :swing 3 :y 100 :speed 4}
            {:x 390 :swing 2 :y 50  :speed 2}]})
```

### 7-2 단계: 눈송이들이 흔들리게 보이도록, `x`의 좌표값 계산하기

`x` 값을 변경하는 것은 `y` 값을 변경하는 것과 유사합니다. 그래서 클라라는 `update-y`
함수를 추가한 방식대로 `update-x` 함수를 추가해 볼까 생각했습니다. 하지만 그녀는 그 시도를
멈추고, 마음 속에 곡선을 그려가면서, `x` 값을 어떻게 계산해 낼 수 있을까 생각합니다.
 
그녀는 Quil api 문서 사이트로 가서, 이 문제를 해결하는데 도움이 될만한 함수가 있는지 살펴
보았습니다. [`sin`](http://quil.info/api/math/trigonometry#sin) 함수를 발견하고는, sine
곡선의 모습을 떠올려 봅니다.

그녀가 원하는 곡선의 모양은 대략 다음과 같이 구할 수 있습니다.

```
x = sin(y)
```

창의 크기를 고려해 볼 때, 흔들리는 모양을 좋게 만들려면 몇 개의 파라미터가 다음과 같이
추가로 필요했습니다.

```
x = x + a * sin(y / b)
```

파라미터 `a`는, 앞에서 **상태값**에 추가했던 바로 그 `swing` 값에 해당합니다. 이 값이
1일때는 곡선은 좌즉의 이미치처럼 궤적을 그리고, 이 값이 3일 때에는 우측의 이미지처럼
궤적을 그리게 됩니다.

![swing is 1](images/1-sin-x.png)  ![swing is 3](images/3-sin-x.png)

파라미터 `b`는 최고점과 최고점 사이의 거리를 결정합니다. 이 값이 작으면 눈송이는 좌우로
바쁘게 움직입니다. 그와는 반대로 이 값이 크면, 눈송이들은 좌우로 여유롭게 움직입니다. 창의
크기를 고려해볼 때 `b`의 값으로는 50이 적당합니다. 이것을 감안한 계산 식은 다음과
같습니다.

 ``` x = x + swing * sin(y / 50) ```

`update` 함수에서는 `x`의 값을 변경해야 할 뿐만 아니라, `x`가 0보다 작은 경우(너무 왼쪽)와
창의 너비보다 큰 경우(너무 오른쪽)를 모두 처리해야 주어야 합니다. `x`가 0보다 작은
경우에는 `x`의 값을 창의 너비값으로 설정해 주어, 창의 오른쪽에서 눈송이가 나타나게 해야
합니다. 마찬가지로 `x`의 값이 창의 너비보다 클 경우에는 `0`으로 설정해 주어, 창의 왼쪽에서
눈송이가 나타나게 해 주어야 합니다. 이런 조건들을 고려해셔, `update-x` 함수를 다음과 같이
작성했습니다.

```clojure
(defn update-x
  [m]
  (let [x (:x m)
        swing (:swing m)
        y (:y m)]
    (cond
     (< x 0) (assoc m :x (q/width))                                  ;; too left
     (< x (q/width)) (update-in m [:x] + (* swing (q/sin (/ y 50)))) ;; within frame
     :else (assoc m :x 0))))                                         ;; too right
```

이 함수에서는 `if`를 사용하고 있지 않는데, 그 이유는 `if`는 단지 두 가지 경우의 수만을
처리할 수 있기 때문입니다. 그래서 `if` 대신에 `cond`를 사용해 여러 경우의 수를 처리하고
있습니다.


### 7-3 단계: 벡터 안의 맵들에 있는 x와 y 좌표값 변경하기

클라라는 `update-y`와 `update-x` 함수를 작성했습니다. 다음 단계는 이 함수들들 이용해 맵
안의 `x`와 `y`의 좌표값을 변경하는 것입니다. 이런 일은 그녀에게는 더 이상 어려운 일이
아닙니다. 한 가지 어려운 점은 맵 안의 `y`의 값을 변경한 후에, 뒤이어 같은 맵 안의 `x`의
값을 변경해야 한다는 것입이다. 다행히도 클로저의 `let` 바인딩에서는 이런 종류의 변경을
쉽게 처리할 수 있숩니다. `let` 안에서 각각의 바인딩은 처음주터 마지막까지 차례대로 하나씩
실행됩니다. 그래서 `update` 함수는 최종적으로 다음과 같습니다.

```clojure
(defn update [state]
  (let [params (:params state)
        params (map update-y params)
        params (map update-x params)]
    (assoc state :params params)))
```

첫 번째 바인딩에서는 `state`의 `:params` 키에 대응하는 값(3 개의 맵으로 구성된 한 개의
벡터)이 지역 심볼 `params`에 배당됩니다. 그 다음에는, `update-y` 함수를 통해 각 맵 안의
`y` 좌표값이 변경된 후, 그 값이 두 번째 `params`에 다시 배당됩니다. 그 후에, `update-x`
함수를 통해 각 맵 안의 `x` 좌표값이 또 한번 변경된 후, 그 값이 세 번째 `params`에 다시
배당됩니다. 그래서 `let` 본체 내의 `assoc` 함수에 이르렀을 때에는, 각 맵 안의 `x`와 `y` 값
모두가 이미 변경되어 있는 상태가 됩니다.

이제 눈송이를 좌우로 흔들리게 하기 위한 모든 과정이 끝났습니다. `draw` 함수는 변경 없이
그대로 사용합니다.

### 단계 7의 `practice.clj`

이제 `practice.clj` 전체 코드는 다음과 같습니다.

```clojure
(ns drawing.practice
  (:require [quil.core :as q]
            [quil.middleware :as m]))

(defn setup []
  (q/smooth)
  {:flake (q/load-image "images/white_flake.png")
   :background (q/load-image "images/blue_background.png")
   :params [{:x 10  :swing 1 :y 10  :speed 1}       ;; swing was added in step 7-1
            {:x 200 :swing 3 :y 100 :speed 4}       ;;
            {:x 390 :swing 2 :y 50  :speed 2}]})    ;;

;; this update-x function was added in step 7-2
(defn update-x
  [m]
  (let [x (:x m)
        swing (:swing m)
        y (:y m)]
    (cond
     (< x 0) (assoc m :x (q/width))
     (< x (q/width)) (update-in m [:x] + (* swing (q/sin (/ y 50))))
     :else (assoc m :x 0))))

(defn update-y
  [m]
  (let [y (:y m)
        speed (:speed m)]
    (if (>= y (q/height))
      (assoc m :y 0)
      (update-in m [:y] + speed))))

(defn update [state]
  (let [params  (:params state)
        params (map update-y params)
        params (map update-x params)]               ;; added in step 7-3
    (assoc state :params params)))

(defn draw [state]
  (q/background-image (:background state))
  (let [params (:params state)]
    (dotimes [n 3]
      (let [param (nth params n)]
        (q/image (:flake state) (:x param) (:y param))))))

(q/defsketch practice
  :title "Clara's Quil practice"
  :size [500 500]
  :setup setup
  :update update
  :draw draw
  :features [:keep-on-top]
  :middleware [m/fun-mode])
```

클라라가 이 코드를 실행시켰을 때, 눈송이가 좌우로 사인 곡선을 그리며 좌추로 흔들면서
떨어지는 모습을 보았습니다. 그녀는 "멋져!"라고 소리치며 기뻐합니다.

리책토링을 포함해서 몇 가지 개선할 점들이 남아 있기는 하지만, 클라라는 이 앱에 만족해
합니다. 더 나아가 그녀는 클로저로 된 더 멋진 앱에 대해 구상하기 시작했습니다!

이상입니다.

--------------
Snowflake is designed by Freepik, http://www.flaticon.com/packs/snowflakes
