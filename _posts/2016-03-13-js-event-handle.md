---
layout: post
title: "자바스크립트 이벤트에 대한 이해"
date: 2016-03-13 19:46:00
categories: javascript
---
자바스크립트 이벤트 전파에서 버블링과 캡쳐링 개념이 매우 헷갈렸는데, 제가 이해한 개념을 공유하고자 합니다.

아래와 같은 html 문서가 있다고 할 때, 웹브라우저에서 hi를 클릭하게 되면 `MouseEvent`가 생성됩니다.


```
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
	<title></title>
</head>
<body>
<ul>
	<li>
    	<p>hi</p>
    </li>
</ul>
</body>
</html>
```
`MouseEvent`의 이벤트타겟은 `<p>`가 될 것입니다. 

이벤트 전파를 비유해서 표현하자면, `MouseEvent`는 버스, `window 객체`는 출발 지점, `<p>`는 유턴하는 분기점입니다.
`MouseEvent` 버스가 출발 지점과 분기점 사이에는 다음과 같은 정류장들이 있을 것입니다.

- 1. document
- 2. body
- 3. ul
- 4. li

이를 그림으로 표현한다면, 
![image](ssucom502.github.io/img/event.png)



이렇게 될 것입니다. 
여기서 다음과 같은 자바스크립트는


```
document.addEventListener("click", function() {
	console.log("document in");
}, true);
```


내려가는 길의 `document`정류장에서 `MouseEvent` 버스를 기다리겠다는 것이고,
다음과 같은 소스는


```
document.addEventListener("click", function() {
	console.log("document in");
}, false);
```


> 내려가는 것을 캡쳐링, 올라가는 것을 버블링 이라고 합니다.
> 구버전 ie는 버블링만 지원하기 때문에 eventListener의 Default 값으로 false로 세팅 되어 있습니다.


올라오는 길의 `document` 정류장에서 `MouseEvent` 버스를 기다리겠다는 것입니다. 
물론 한 소스내에서 두가지의 이벤트리스너는 둘다 등록 가능합니다. 엄밀히 둘은 다른 정류장이니까요..



그리고 이벤트의 전파를 막는 `stopPropagation` 은 자기 정류장에서 버스 기사를 ~~죽여(?)~~ 더이상 버스를 움직이지 않게 한다고 이해할 수 있겠습니다.

이건 제 개인적으로 이해한 부분이니, 부족한 부분이나, 틀린 점이 있으면 댓글로 달아주세요~
