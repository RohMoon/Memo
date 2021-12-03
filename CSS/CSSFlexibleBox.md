# CSS -Flexible Box

대부분 사이트는 전체 레이아웃이 수직 구성이며 `위- 아래`로 스크롤하여 사용하고 있다.  
레이아웃을 구성할 때 가장 많이 사용하는 요소(Elements)들이 기본적으로 블록 (Block) 개념으로 표시 (Display)되며 이는 뷰(View)에서 수직(위에서 아래로) 쌓이기 때문에 수직 구성은 상대적으로 쉽게 만들 수 있습니다.   
하지만 수평(왼쪽에서 오른쪽으로 )구성의 경우는 상황이 조금 다릅니다.  

문제는 수평 구조를 만드는 속성이 명확하지 않았기 때문인데, 그래서 많은 경우 `<table>`이나 `float` 혹은 `inline-block`등의 도움을 받았습니다.

하지만 이러한 방법들은 다양한 문제 (`Clear`,`White`,`space`등 해결은 가능하지만)를 가지고 있기 때문에 결국 수평 레이아웃 구성의 처산책일 뿐이며, 이제 우리는 Flex(Flexible Box)라는 명확한 개념(속성들)으로 레이아웃을 쉽게 구성할 수 있습니다.  

```html
<head>
    <style>
        .box{ float:left;}
        .clear-element {clear:both;/* or left*/}
    </style>
</head>

<body>

<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="clear-element"></div>

</body>
```

자세한 설명은 생략하고, 위 방법은 보기엔 단순하지만 `box`를 제외한 `clear-element`라는 이름(class)의 다음(next)요소도 있어야 하기 때문에 실제 사용엔 매우 불편하며 명확하지 않은 방법으로써 많은 경우 아래 방식을 사용합니다.
```html
<head>
    <style>
        .clearfix::after{
            content:"";
            clear:both;
            display:block;
        }
        .box{
            float:left;
        }
    </style>
</head>
<body>
    <div class="clearfix">
        <div class="box"></div>
        <div class="box"></div>
        <div class="box"></div>
    </div>
</body>
```
에제를 보면 수평이 될 요소들에 직접! `float`를 적용하고 드 요소들의 Container(부모요소 )에 미리 설정한 `clearfix`를 적용한다.