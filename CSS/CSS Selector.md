# CSS 선택자
- CSS 선택자는 CSs 규칙의 첫 부분이다.  
- 규칙 내의 CSS 속성 값을 적용하기 위해 어떤 HTML 요소를 선택해야 하는지 브라우저에 알려주는 요소 및 기타용어 패턴이다.
- 선택자에 의해 선택된 요소들은 선택자의 주제(Subject)로 지칭된다.
> 대부분의 선택자는 [level3 선택자 사양](https://www.w3.org/TR/selectors-3/) 
  
## 선택자 목록
동일한 CSs 사용하는 항목이 두 개 이상인 경우 규칙이 모든 개별 선택자에 적용되도록 개별 선택자를 ___선택자 목록___ 으로 결합할 수 있다.  
예를 들어서, `<h1>`에 대해 동일한 CSS 와 `Special` class 를 사용하면 이것을 두 개의 별도 규칙으로 작성할 수 있습니다.  
```css
h1 {
    color: blue;
}

.special{
    color: blue;
}
```  
또한 이들 사이에 쉼표를 추가하여 선택자 목록으로 결합할 수도 있다.
```css
h1, .special{
    color: blue;
}
```
잘못된 class 선택자 규칙이 무시되고 `<h1>`은 여전히 스타일이 지정된다.
```css
h1 {
    color: blue;
}
/*  잘못된 클래스 선택자 */
..special {
    color: blue;
}
```
그러나 결합시에 전체 규칙이 유효하지 않은 것으로 간주되어 `<h1>` 또는 class가 스타일 지정되지 않는다.
```css
h1, ..special{
    color: blue;
}
```

## 선택자자의 유형
### Type, class 및 ID 선택자

#### HTML 요소를 대상으로 하는 선택자
```css
h1{}
```
또한 `class`를 대상으로 하는 선택자가 포함된다.
```css
.box{}
```
또는 ID:
```css
#unique{}
```
#### 속성 선택자
요소에 특정 속성이 있는지에 따라 요소를 선택하는 다른 방법.
```css
a[title] {}
```
또는 특정 값을 가진 속성의 존재 여부를 기반으로 선택할 수도 있다.
```css
a[href="https://example.com"] {  }
```
#### Pseudo-classes 및 pseudo_elements
이 선택자 그룹에는 요소의 특정 상태를 스타일링하는 pseudo-classes가 포함된다.  
예를 들어 `:hover` pseudo-class는 마우스 포인터에 의해 요소를 가리킬 때만 요소를 선택한다.
```css
a:hover { }
```  
또한 요소 자체가 아닌 요소의 특정 부분을 선택하는 pseudo-elements도 포함된다.  
예를 들어, `::first-line`은 항상 `<span>`이 첫 번째 형식의 라인을 감싸는 것처럼 작동하여, 요소 내부의 첫 번째 텍스트라인 (아래의 경우 `<p>`)을 선택한다.  
```css
p::first-line{}
```

### 결합자(Combinators)
최종 선택자 그룹은 문서 내의 요소를 대상으로 하기 위해 다른 선택자를 결합합니다. 예를 들어 다음은 자식 결합자 (`>`)를 사용하여 `<article>`요소의 자식인 단락을 선택한다.  
```css
article > p{  }
```
| 선택자 | 예제 |CSS 자습서 배우기 |
|------|-----|----------|
|TYPE 선택자        |   h1 {   }      | Type selectors         |   
|범용 선택자          |   * {   }       | the universal selector |
|Class 선택자       |   .box {   }    | Class selectors        |
|id 선택자          |   #unique {  }  | ID selectors           |
|속성 선택자          |  a[title] {   } |Attribute selectors     |
|Pseudo-class선택자 |   p:first-child{ }|Pseudo-classes        |
|Pseudo-element선택자|  p::first-line{ }|Pseudo-elements       |
|하위 결합자          |  article p     |  Descendant combinator  |
|자식 결합자          |  article >p    |  Child combinator       |
|인접 형제 결합자       | h1 + p         | Adjacent sibling        |
|일반 형제 결합자       | h1 ~ p         | General silbing         |