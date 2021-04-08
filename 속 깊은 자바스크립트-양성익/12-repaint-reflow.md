# Repaint와 Reflow


자바스크립트의 중요한 특징 가운데 하나는 DOM과 상호작용한다는 것입니다. DOM은 Document Object Model의 약자로 플랫폼이나 언어에 독립적으로 문서를 구성하고 디자인을 적용시킬 수 있는 인터페이스 입니다. HTML과 함께 XML, SVG등이 DOM을 기반으로 구성됩니다.

자바스크립트는 특히 HTML과 조합되어 사용자에게 적절한 데이터를 보여줍니다. 최근에는 비동기통신으로 자바스크립트에서 받은 데이터를 HTML의 DOM조작 통해서 동적으로 데이터를 출력하거나 DOM디자인을 조작하는작업이 많아 졌습니다.

DOM을 동적으로 조작하는것은 생각보다 간단한 작업이 아닙니다. 이유는 **TREE를 순회하는 조작이 같이 발생해 컴퓨팅 자원 소모가 심하다는 것**입니다(DOM은 TREE구조를 가집니다). 본 글에서는 이렇게 자바스크립트를 이용하여 DOM을 동적으로 조작할 때 알아야하는 **DOM repaint**와 **DOM reflow**에 대해 알아보겠습니다.


## DOM repaint
먼저 DOM repaint는 특정 엘리먼트의 visibility를 수정하거나 글자색, 배경색 처럼 HTML의 엘리먼트들의 위치는 변경되지 않고, 색상등의 스타일만 변경될 때 발생합니다. DOM repaint의 경우 **해당 DOM의 하위 트리 구조 전체를 탐색**하므로 CPU자원이 소모됩니다.


## DOM reflow
DOM reflow는 repaint와 다르게 DOM의 구조나 크기, 클래스가 변경(클래스에 대한 새로운 CSS가 적용되므로)될때 발생합니다. 이는 DOM의 구조가 변경되 각 엘리먼트의 위치를 다시 계산되는 것을 의미합니다. repaint보다 reflow는 작업특성상 자원소모가 더 큽니다.





## DOM reflow 최소화 하기
reflow를 최소화하는 것은 그만큼 CPU자원소모를 최소화하는 것입니다. reflow는 변경되는 엘리먼트의 하위 엘리먼트들의 위치를 계산하므로 CPU를 사용하는 것인데, 자주 변경되는 엘리먼트들의 position을 absolute나 fixed로 설정하면 좋습니다. 이는 웹 페이지 천제에는 영향을 미치기 않기 때문입니다. 아주 간단한 방법이지만 일반적 DOM 엘리먼트를 변경하는 것과 비교하면 월등하게 성능상 유리합니다. 

position을 absolute로 지정하고 left와 top을 지정하여 reflow에 영향을 미치지 않게 할 수도 있지만 position : relative를 이용하면 위치계산없이 페이지의 중간에 absolute를 사용할 수 있다.


```html
<html>
<head>
  <style>
    #bannerWrapper {
      overflow: hidden;
      height: 400px;
      width: 265px;
      position: absolute;
    }
    
    #bannerImg {
      position: absolute;
    }

    #bannerImg.mouseover {
      top: -400px;
    }
  </style>
</head>
<body>
  <div id="bannerWrapper">
    <img id="bannerImg" src="http://i.imgur.com/3DpJ0ou.png"/>
  </div>
  <script>
    (function(){
      var imgBannerImg = document.getElementById('bannerImg');
      imgBannerImg.addEventListener('mouseover', function(){
        this.classList.add('mouseover');
      });

      imgBannerImg.addEventListener('mouseout', function(){
        this.classList.remove('mouseover');
      });
    }());
  </script>
</body>
</html>
```
위 예제는 배너이미지에 마우스오버 이벤트를 등록한 예제입니다. bannerImg가 absolute로 적용되어 mouseover가 적용되어도 bannerImg또는 그 하위(위 소스에는 하위가 없음) 엘리먼트만 reflow가 발생하도록 적용되었습니다. 위 같은 상황은 메뉴의 드랍아웃등의 예제로 확장 될 수 있습니다.

