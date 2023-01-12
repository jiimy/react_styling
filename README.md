# react_styling
리액트의 스타일링에 대한 연구를 하는 md만 있는 레포입니다. 

* * *
* * *

# 큰제목


# 프롤로그

---

[ 글을 쓰게된 이유, 필자에 대한 소개 ]

- 여러 회사를 다니면서 여러 방법들을 시도, 경험해보았다.
- 설계 방법에 대한 가이드를 잡아주고 싶었다.
- 여러 스타일링 방법들을 사용하며 느낀점을 알려주고 싶었다.
- 전역 상태가 아닌 스타일관리만으로도 스타일을 조정하는 것에 대해 의견을 제공하고 싶었다..

# scss

- 기본적인 scss의 문법에 대해 자세히 알려주지 않고, 더 다양할 활용에 대해서 전달한다.
- 앞서 말한듯 효율을 떠난 scss에서의 가능성을 보여주고싶다.

## mixin, extend

mixin과 extend둘다 공통된 기능을 묶는다는 기능을 가지고 있지만, mixin은 파라미터를 보낼 수 있다는 점이 가장 큰 차이점이고, extend는 아래와 같은 이유로 최대한 지양해야 합니다. 

- 한 번 지정된 속성이 기본값이기 때문에 기존속성을 지우거나 변동시킬 수없어 최소한으로만 공통된 요소 추가.
- 위의 이유로 변동을 할 수 없어 재사용성 저하

컴포넌트 화 하는것이 가장 중요하지만 -컴포넌트 관리하지 않으면 관리가 힘들어집니다- 그런 상황이 아닐 경우 mixin으로 해야할지,  

반복되는 부분을 mixin으로 사용할지, class로 관리하는것이 좋을지에 대한 여부는 그 layout의 변동 가능성으로 판별할 수 있습니다. 

```scss
@mixin guide {
  width: calc(100% - 754px);
}
```

위 코드는 단순히 width값만 리턴하는 경우이다.  754px은 규격화 되어있지 않은 사이즈이기 때문에 size관리를 해주고, 더 사용성이 확장되게 class로 관리하는것이 좋습니다. 

```scss
@mixin same($same) {
  width: $same;
  height: $same;
  @content;
}
```

위와 같은 코드는 단순히 width와 height가 같다는것뿐인데 함수를 사용하게 만든다. 이런 경우 컴포넌트화 해서 관리를 해줘야 한다. 

### 배열 list, map 의 응용

#### 1. list

list는 javascript의 array처럼 키값이 없는 index값만 있는 value의 모음입니다. 

이 list 를 이용해서 자식, 형제 또는 자기 자신이 아닌 자신을 기준으로 상위의 클래스, 부모의 클래스가 특정 클래스를 가지고 있다는 것을 판별하여 별도의 스타일을 지정할 수 있습니다. 

```html
<div id="tutoringWrap">
    <div id="tutoringHead" class="tutoringHead">헤더</div>
    <div class="test-page">랜딩</div>
    <div class="early-bird-allpage">랜딩2</div>
  </div>
```

```scss
$url-list: '.test', '.page', '.test-page', '.early-bird-allpage'; // 선언된 리스트
$boolean: 'false'; // 있는지 없는지 구분할 변수

@mixin render($header) {
  #{$header} {
    @content;
  }
}

@mixin class-check($classname, $header) {
  $child : $classname;
  @each $url in $url-list {
    @if ($url == #{$classname} ) { // .test-page가 url-list 에 있다면 
      $boolean: 'true'!global; // $boolean 값을 true로 체크하고 
      @include render($header)  { //그 클래스에게 받은 스타일 값을 준다
        @content;
      };
    }
    @else {
      $boolean: 'false'!global; // .test-page가 url-list에 없다면 작동하지 않는다. 
    }
  }
}

div {
  @include class-check('.test-page', '.tutoringHead') { // 첫번째 변수가 $url-list 에 있다면 두번째 변수에게 아래의 스타일을 주기
      color :red;
  }
}
```

이 코드의 결과값은 헤더 텍스트가 color : red 속성을 가지게 됩니다. 

이 방법은 평상시에는 헤더가 fixed된 상태이지만, 랜딩페이지와 같이 특정 페이지에서 헤더의 스타일을 변경 할 때 사용될 수 있습니다. 

#### 2. map

map은 javascript 의 json처럼 key와 value값을 가지고 있습니다. 

기본적으로 depth가 1인 map을 많이 사용하나 depth가 2인 map일때의 경우도 있습니다. 

하지만 depth가 2일 경우 사용되고 있는 node-sass, sass의 버전에 따라 적용이 안될 수도 있습니다. 

[https://codepen.io/rudwnok/pen/RwVZvwQ](https://codepen.io/rudwnok/pen/RwVZvwQ)

```html
<div class="cluster-office-marker c1">
 c1 
</div> 
<div class="cluster-office-marker c2">
 c2 
</div> <div class="cluster-office-marker c3">
 c3
</div> <div class="cluster-office-marker c4">
 c4 
</div> 

<div class="cluster-order-marker c1">
 c1 
</div> 
<div class="cluster-order-marker c2">
 c2 
</div> <div class="cluster-order-marker c3">
 c3
</div> <div class="cluster-order-marker c4">
 c4 
</div>
```

```scss
$bg-color1: #9195c1;
$bg-color2: #767fd7;
$bd-color1: #878bb2;
$bd-color2: #7780d9;
$bg-color3: #5c6cfd;
$bd-color3: #5362f2;
$map-cluster-office : (
  1 : ('bg-color': $bg-color1, 'bd-color': $bd-color1, 'size' : 30px),
  2 : ('bg-color': $bg-color2, 'bd-color': $bd-color2, 'size' : 40px),
  3 : ('bg-color': $bg-color2, 'bd-color': $bd-color2, 'size' : 55px),
  4 : ('bg-color': $bg-color2, 'bd-color': $bd-color2, 'size' : 70px),
  5 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 80px),
  6 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 90px),
  7 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 100px),
  8 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 110px),
  9 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 120px),
);
$map-cluster-order : (
  1 : ('bg-color': $bg-color1, 'bd-color': $bd-color1, 'size' : 30px),
  2 : ('bg-color': $bg-color2, 'bd-color': $bd-color2, 'size' : 40px),
  3 : ('bg-color': $bg-color2, 'bd-color': $bd-color2, 'size' : 55px),
  4 : ('bg-color': $bg-color2, 'bd-color': $bd-color2, 'size' : 60px),
  5 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 65px),
  6 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 70px),
  7 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 75px),
  8 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 80px),
  9 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 85px),
  10 : ('bg-color': $bg-color3, 'bd-color': $bd-color3, 'size' : 90px),
);

// type : 'office' or 'order'
@mixin cluster-office ($type : 'office') {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  font-weight: 600;
  color: #f2f2f2;
  border: 1px solid #fff;

  $map-name: '';
  @if( $type == 'office') {
    $map-name: $map-cluster-office;
  }
  @if( $type == 'order') {
    $map-name: $map-cluster-order;
  }
  @for $i from 1 through length($map-name) {
    &.c#{$i} {
      z-index: (10 + $i);
      opacity: 0.93;
      width: map-get($map-name, $i, 'size');
      height: map-get($map-name, $i, 'size');
      background-color: map-get($map-name, $i, 'bg-color');
      border-color: map-get($map-name, $i, 'bd-color');
    }
  }
}

.cluster-office-marker {
    @include cluster-office('office');
}
.cluster-order-marker {
    @include cluster-office('order');
}
```

### 반복문 for, each 응용

#### 1. for

javscript의 for 와 each 처럼 scss의 for와 each도 비슷한 기능을 가지고 있습니다.  

for문의 최대 반복횟수를 length 로 받는 경우의 예제입니다. 

```html
<p>다람쥐 헌 쳇바퀴에 돌고파 다람쥐 헌 쳇바퀴에 돌고파다람쥐 헌 쳇바퀴에 돌고파다람쥐 헌 쳇바퀴에 돌고파다람쥐 헌 쳇바퀴에 돌고파다람쥐 헌 쳇바퀴에 돌고파다람쥐 헌 쳇바퀴에 돌고파</p>
```

```scss
$breakpoints-tablet : 1366;
$breakpoints-mobile : 768;

@function pxtovw-mo($target) {
  $vw-content: ($breakpoints-mobile * 0.01) * 1px;
  $result: ();
  $length: length($target);

  @for $i from 1 through $length {
    $result: append($result, nth($target, $i) / $vw-content +vw);
  }

  @return $result;
}

@function pxtovw-ta1366($target) {
  $vw-content: ($breakpoints-tablet * 0.01) * 1px;
  $result: ();
  $length: length($target);

  @for $i from 1 through $length {
			@if(type-of(nth($target, $i)) == string) { 
				$result: append($result, nth($target, $i));
			}
			@else {
		    $result: append($result, nth($target, $i) / $vw-content +vw);
			}
  }

  @return $result;
}

@mixin pxtovw-1366($value, $size, $mode:'ta', $flag:'false') {

  // mo
  @if ($mode=='mo') {
    @media (max-width: $breakpoints-mobile + 'px') {
      @if($flag==true) {
        #{$value}: pxtovw-mo($size) !important;
      }

      #{$value} : pxtovw-mo($size);
    }
  }

  //tablet
  @if ($mode=='ta') {
    @media (min-width: $breakpoints-mobile + 1 + 'px') and (max-width: ($breakpoints-tablet +'px')) {
      @if($flag==true) {
        #{$value}: pxtovw-ta1366($size) !important;
      }
      #{$value} : pxtovw-ta1366($size);
    }

    //pc
    @media (min-width: ($breakpoints-tablet+'px')) {
      @if($flag==true) {
        #{$value}: $size !important;
      }
      #{$value} : $size;
    }
  }
}

p {
  @include pxtovw-1366(font-size, 30px);
  @include pxtovw-1366(margin, 20px 10px auto);
}

.box {
	@include pxtovw-1366(width, 800px);
	@include pxtovw-1366(height, 800px);
	background: #ccc;
}
```

#### 2. each

each를 이중으로 사용할 경우

```scss
$character-status-img : (
	1: study,
  2: nice,
  3: good,
  4: wink,
  5: smile,
  6: normal,
  7: bad,
  8: sad
);
$character-name-img : (
	1: kkaeda,
  2: chaelli,
  3: ddami
);

@mixin character-status() {
  @each $list, $status in $character-status-img {
    @each $list, $name in $character-name-img {
      &.is-#{$status}.#{$name} {
        background:url('components/imgs/#{$name}-#{$status}.png') no-repeat center;
        background-size: contain;
      }
    }
  }
}
```
위 코드는 1~8까지의 상태를 가진 1~3개의 캐릭터에 대한 8*3의 경우의 수를 계산하는 식입니다. 

#### 조건문 if 응용

이 if문은 하나의 컴포넌트에 여러가지의 타입을 구분할 경우에 자주 사용됩니다. 

```scss
@mixin type($type: 'default') {
	color: red;
	@if($type == 'type1') {
		color: blue;
	}
	@if($type == 'type2') {
		color: orange;
	}
}

p {
	@include type();
	&.is-type1 {
		@include type('type1');
	}
  &.is-type2 {
		@include type('type2');
	}
}
```

이와 같은 방법으로 작성하게 되면 각각의 상태, 타입에 대한정의를 쉽게 파악할수있고, 수정도 용이합니다. 

하지만 기존 속성을 유지한체 type에 의해 덮어 씌워지기 때문에 내부에 속성정의가 많다면 개발자모드에서 속성이 길게 나오게 됩니다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b3680aea-778c-4598-86b3-6839e9c329f3/Untitled.png)

이 문제에 대한 해결 방안은 바로 뒤쪽에 추가적으로 진행됩니다. 

### 그 외 기능들

- spread operator
- unquote, quote
- unique-id()

각각의 기능들을 간단히 알아보겠습니다. 

**spread operator**

```html
<div class="selector">그라디언트</div>
```

```scss
@mixin linear-gradient($direction, $gradients...) {
  background-color: nth($gradients, 1);
  background-image: linear-gradient($direction, $gradients...);
}

.selector {
  @include linear-gradient(to right, magenta, red, orange, yellow, green, blue, purple);
}
```

… 로 사용되며,  매개변수의 가장 마지막에만 선언 가능합니다. 

**unquote, quote**

```html
<div class="transform">트랜스폼</div>
```

```scss
@mixin transform($type, $size, $mode:'ta', $scale:null) {
		@media (min-width: ($viewport-tablet+'px')) {
				transform: #{$type}#{unquote("(#{$size})")} $scale;
  }
}

.transform {
	@include transform(translate, '20px');
}
```

unquote는 따옴표( ‘ ) 를 벗겨내고, quote 는 따옴표 ( ‘ ) 를 추가합니다. 

대부분 string 으로 받은 변수를 string 으로 사용하지 않거나, 받은 변수를 string 으로 변환하기 위하여 사용합니다. 

**unique-id()**

```html
<div class="unique-id">unique-id</div>
```

```scss
$button-selector: unique-id();

.unique-id {
	color: $button-selector;
	&:after {
		content: quote($button-selector);
	}
}
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/29f838b3-d66d-4c9d-bbb3-17ff3b8cf771/Untitled.png)

기본적으로 해쉬변수를 만들어내는 내장 함수입니다. 

[ unique-id 를 이용한 중복 스타일 제거하기 코드 추가 ]

[ unique-id 를 이용한 클래스에 고유값 주기 코드 추가 ]

### 최신 버전

- using slot

using slot은 @content를 여러개 사용할 수 있습니다. 

using slog은 버전이 맞지 않아 대부분의 개발환경에서는 실행이 어렵습니다. 

```html
<!-- input을 label로 감싼 경우 -->
<label for="label1" class="custom-label">
  <input type="checkbox" name="" id="label1">
  <span></span>
  <span class="text">라벨 텍스트</span>
</label>
```

```html
<!-- input을 label로 감싸지 않은 경우 -->
<input type="checkbox" name="" id="label1" class="custom-label-checkbox">
<label for="label1" class="custom-label">
  <span></span>
  <span class="text">라벨 텍스트</span>
</label>
```

```scss
@mixin custom-label {
  display: inline-block;
  width: 20px;
  height :20px;
  cursor:pointer;
  input[type="checkbox"], input[type="radio"] {
    display: none;
    + span {
      display: inline-block;
      vertical-align: middle;
      @content (bg);
    }
    &:checked + span {
      @content (checked-bg);
    }
  }
  .text {
    display: inline-block;
    vertical-align: middle;
  }
}

.custom-label {
  @include custom-label using ($slot) {
    @if $slot == bg {
      // 이부분에 input 스타일
      background: #ccc;
    } @else if $slot == checked-bg {
      // 이부분에 선택된 input 스타일
      background: red;
    }
  }
}
```

```scss
@mixin custom-label {
  cursor:pointer;
  input[type="checkbox"], input[type="radio"] {
    display: none;
    + span {
      display: inline-block;
      display: inline-block;
      width: 20px;
      height :20px;
      vertical-align: middle;
      @content (bg);
    }
    &:checked + span {
      @content (checked-bg);
    }
  }
  .text {
    display: inline-block;
    vertical-align: middle;
  }
}

.custom-label {
  @include custom-label using ($slot) {
    @if $slot == bg {
      // 이부분에 input 스타일
      background: #ccc;
    } @else if $slot == checked-bg {
      // 이부분에 선택된 input 스타일
      background: red;
    }
  }
}
```

앞에서 봤던 @if와 비교해보면 

```scss
// using slot

@mixin example {
  .alpha {
    @content (alpha);
  }
  .beta {
    @content (beta);
  }
  .koma {
    @content (koma);
  }
}

@include example using ($slot) {
  @if $slot == alpha {
    color: red;
  } @else if $slot == beta {
    color: blue;
  } @else if $slot == koma {
    color: orange;
  }
}
```

```scss
// @if

@mixin example($type: 'default') {
	@if($type == 'alpha') {
		color: red;
	}
	@if($type == 'beta') {
		color: blue;
	}
	@if($type == 'koma') {
		color: orange;
	}
}

div {
	&.alpha {
		@include example('alpha');
	}
	&.beta {
		@include example('beta');
	}
	&.koma {
		@include example('koma');
	}
}
```

example using slot 은 include 할 때 스타일을 선언하지 않고, mixin 단계에서 선언하기 때문에 코드정리가 덜 되어 보이는 부분이 있습니다. 

mixin 단계에서 스타일을 선언하는 경우는 이 컴포넌트가 어떤 스타일을 가지고 있는지 한눈에 파악하기 용이합니다. 

그래서 예제로 들었던 checkbox와 같이 유형의 갯수가 적을시에는 상태를 더 알아보기 쉽습니다. 

if 를 이용한 example은 정의부에 스타일이 있고, 선언부에서만 사용이 되기 때문에 유형이 많아져도 어떤 타입이 있는지 알아보기 쉽습니다. 

그래서 유형이 많은 컴포넌트일때 사용하는 것이 적당합니다. 

---

지금까지의 내용처럼 scss는 사용방법에 따라 css의 기능을 벗어나 javascript의 기능과 개념들을 많이 포함하고 있어 여러 기능들이 나올 수 있습니다.

문자수신 300초, jquery의 next()와 prev() 처럼 지정된 클래스의 형제, 부모 선택 등 scss로 구현이 될거같은 기능들을 연구 중에 있습니다. 

## react

react 안에서 스타일링을 하는 방법은 대표적으로 scss, css in js, module 이 있습니다. 

주로 사용되는 scss와 css in js를 비교해보겠습니다. 

### css in js vs scss

css in js 는 js안에서 css를 쓰는 방법입니다. styled-components 를 대부분 사용하며, emotion 을 쓰기도 합니다. 

**scss와 styled components의 코드 비교**

이 부분의 styled component는 typescript 와 emotion환경입니다. 

**이미지 루프**

```scss
// 1. 순회 바로 하기 
// ⓐ 이미지 변수명의 map 객체 생성
$class-img : (
 receipe,
 hospital
);

// ⓑ 실제 적용 
li {
	@for $i from 1 through length($class-img) {
  &.nth-of-type($i) {
		.img {
				display: inline-block;
				vertical-align: middle;
				width: 23px;
				height: 23px;
				background: map-get($class-img, $i);
				background-size: auto 23px;
				}
	   }
	}
}

```

```scss
// 2. 함수로 사용
// ⓐ 이미지 변수명의 map 객체 생성
$class-img : (
 receipe,
 hospital
);

// ⓑ 맵 순회 함수 
@mixin array ($map-name) {
  @for $i from 1 through length($map-name) {
    &:nth-of-type(#{$i}) {
      .img {
				display: inline-block;
				vertical-align: middle;
				width: 23px;
				height: 23px;
				background: map-get($class-img, $i);
				background-size: auto 23px;
				}
     }
  } 
}

// ⓒ 실제 적용
li {
	@include array($class-img);
}
```

```tsx
// ⓐ 이미지 파일을 import 
import {
receipe,
hospital
} from './icon';

// ⓑ 불러온 파일을 배열 처리
const menuimgurl: string[] = [receipe, hospital];

// ⓒ 렌더해주는 부분
function loopRender(i: number) {
	return `
		&:nth-of-type(${i + 1}) {
			.img {
				display: inline-block;
				vertical-align: middle;
				width: 23px;
				height: 23px;
				background: url( ${menuimgurl[i]} ) no-repeat center;
				background-size: auto 23px;
			}
		}
	`;
}

// ⓓ 배열을 순회하는 함수
function arrayloop() {
	let str = "";
	for (let index = 0; index < menuimgurl.length; index += 1) {
		str += loopRender(index);
	}
	return str;
}

// ⓔ 스타일드 컴포넌트에 적용
export const Menulist = styled.li`
	${arrayloop()}
	.img {
		position: relative;
	}
`;
```

```jsx
// 번외
```

**정해져있는 상태 받기 ( true, false 의 경우 )**

이 부분에서는 탭 부분에서 탭컨텐츠가 다르게 보일경우, 또는 테마형식으로 레이아웃등이 비슷하나 일부가 다른 경우 사용하는 부분입니다.  **이미 테마의 갯수나 상태가 정해져있는 경우**

```scss
@mixin List($type) {
	@if( $type == 'true') {
		color: red;
	}
	@if ($type == 'false') {
		color: blue;
	}
}

li {
	&.is-true {
		@include List('true');
}
	&.is-false {
		@include List('false');
	}
}

```

### 정해져있지 않은 상태 받기

기존에 상태가 없었고 **비슷하나 이전에 없던 다른 테마나 상태가 추가될 경우**

css에선 타입 추가시 유동적으로 대응 가능. 

```scss
.button {
	background: black;
// or 
	&.is-bg-red {
	}
}

// 클래스를 추가해서 작업
.btn-bg-red {
	background: red;
}

<button class="button"></button> => <button class="button btn-bg-red"></button> 

```

테마(바뀔 부분)가 **'확정'** 적으로 정해져있다면 스타일드컴포넌트를 만들시에 타입을 지정하지만..

```tsx
// ⓐ 넘겨줄 props 설정
export type ListProps = {
	eventstate? : string;
}

const List = styled.li<ListProps>`
  ${props => props.eventState == 'true'
	? 
    `color: red;
		`
	: 
		`color :blue;
		`
  }
`

<List eventstate='true'></List>
<List eventstate='false'></List>
```

타입이 아에 없는 상태에서 추가를 하게 된다면 기존 스타일드컴포넌트 파일에 타입을 넣는 부분을

수정해야 한다. 

```tsx
// 선언
const btn = styled.button`
	background: black;
`
// 사용
<btn></btn>

// 재선언 및 타입을 추가
type btn = {
	theme: string;	
}
const btn = styled.button<btn>`
	background: black;
  color: ${props => props.theme == 'red' ? 'red' : '';
`

// 타입 연결
<btn theme=""></btn>
```

### 키프레임

```scss
@keyframes delayShow {
	0% {
		opacity: 0;
		bottom: -10px;
	}
	100% {
		opacity: 1;
		bottom: 0;
	}
}

span {
	animation: delayShow 0.5s ease backwards
}
```

### 믹스인

```tsx
@mixin button($width: '20px') {
 // 각종 속성 및 함수들
	width: $width;
}

div {
	@include button();
}
```

```jsx
import { css, keyframes } from "@emotion/core";

const delayShow = keyframes`
	0% {
		opacity: 0;
		bottom: -10px;
	}
	100% {
		opacity: 1;
		bottom: 0;
	}
	`;

<span css={css`
	animation: ${delayShow} 0.5s ease backwards
`}>
</span>
```

```tsx
// css in js에서는 mixin 개념이 없고 위쪽의 props를 받아서 쓰는 방식으로 사용합니다. 
```

### extend

한파일내에서 반복되는 부분이 있다면 코드의 라인수를 줄이기 위해 사용될수도 있지만, 테마가 다른 탭같은 경우는 mixin안에 두고 타입을 받아 변동하는것으로 extend를 대체할 수 있습니다. 

```scss
@extend listbutton{
  display: block;
}

.listbutton {
	%listbutton;
}

```

스타일드 컴포넌트에서는 자주 쓰이는 편입니다. 

```tsx
const ListbuttonStyle = css`
	display: block;
`
const Listbutton = styled.div`
	${ListbuttonStyle}
`

<Listbutton></Listbutton>
```

### 사용 경험

scss는 css 기반이라 젠코딩이나 자동완성의 효율이 좋습니다. 
확장프로그램에서 csscomb를 이용하면 속성들의 순서도 정렬 가능합니다.

스타일 파일이 분리되 있을때 scss에서는 미리 정의한 파일을 import 해서 사용할수 있지만, 

css in js 에서는 import 할때 일일이 스타일정의된 파일에서 export 를 해줘야 사용할수 있었습니다. 

확장프로그램을 별도로 설치하지 않으면 젠코딩 및 자동완성이 힘들고, 그마저도 제대로 인식이 되지 않았습니다. 

css 작성하듯이 하면 자동완성때문에 오타가 빈번히 발생했고, 스타일 수정이 필요할 때에는 추적이 힘들어 직접 파일을 찾아가며 수정해야 했습니다. 

ex: letter-spacing : -1px → 자동완성으로 인해 letter-spacing : —1px;  

하지만 상태가 3개이상 많은 경우 css에서는 상태가 먼저 정의되어있어야 하지만 스타일드 컴포넌트에서는 받은 즉시 상태가 되어서 상태 변경에는 좋았습니다. 

#####styled-component의 단점들
1. 해쉬처리로 인한 추적의 어려움 
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c06269e5-b03b-4f3f-a156-b161238e5175/Untitled.png)
    
2. 선언된 스타일이 많을 경우, 파일을 별도 분리하여 사용하게 되는데 그럴때에도 모든 선언된 styled를 export 로 지정해야 사용 가능합니다.
3. styled로 선언된 스타일을 수정할 시 스타일부분만 변경되지 않고 페이지 전체가 re-rendering 됩니다. 
4. is-active와 같이 특정 상태에 대한 ui를 확인하기 위해서는 개발자모드에서 dom에 class추가하는걸로 확인할 수 없고, styled-component로 선언된 스타일에 상태를 직접 추가해야 확인 가능합니다.

2, 4, 5 번의 경우 ui를 작업해야는 개발자 입장에서는 너무나 큰 타격입니다. 

### css in js 를 사용하지 않고 css의 단점을 보완하는 방법

### 1. classname 중복 ( 해쉬 )

1.1 module 

node-sass 패키지 설치 후, 

스타일 파일 이름을 'buttons.module.scss' 처럼 '이름.module.scss' 로 만들고 

import 로 가져올시 import styles from '파일경로' 로 작성합니다.

```jsx
// TodoItem
import styles from './TodoItem.module.scss';

return (
	<div className={`${styles.TodoItem__text} ${styles.remove}`}>asdfasdf</div>
)
```

```scss
// TodoItem.module.scss 
.text {
  background: black;
}
```

1.2 cx

cx를 사용해도 위와 같이 나타낼 수 있지만, 이벤트가 아닌 모든 곳의 classname을 cx로 감싸줘야 합니다. 

```jsx
import style from './cta.scss';

const cx = classNames.bind(style)

return (
	<div className={cx("challenge-cta", "is-simple")}>내용</div>
)

```

1.3 scope

[https://min9nim.github.io/2020/04/react-scoped-css/](https://min9nim.github.io/2020/04/react-scoped-css/)

실제 적용코드

[https://github.com/gaoxiaoliangz/react-scoped-css/tree/master/examples/simple](https://github.com/gaoxiaoliangz/react-scoped-css/tree/master/examples/simple)

하지만 이 방법들도 단점이 있습니다. 

module을 사용한다면, 

앞에 styles를 항상 붙여줘야하고, 정의가 되어있지 않은 classname이라면 페이지 렌더 자체에서 에러가 납니다. ( 에러부분 추적엔 좋지만, 잦은 에러 발생으로 방해가 되는 부분도 있습니다. )

cx를 사용한다면,

module과 **cx, css in js** ( 스타일드 컴포넌트, 이모션 ) 과 같은 방식 사용시 아래의 단점이 있습니다. 

- [ **cxx in js, cx** ] **클래스 난수화로 인해 파일추적의 어려움** ( 어디까지 내가 지정한 클래스이고, 어디서부터 난수인건지 구분이 어려움. bem 까지 쓰게된다면 더더욱!  )
- [ **cx** ] is- 와 같은 상태를 넣을때, 개발자보기에서 넣으면 안되고 **js파일에 직접 넣었다 뺏다 해야되는 번거로움** ( 클래스가 빠지고 들어가는 순간에 애니메이션같은게 있다면 파악하며 하기가 어려움 )
- [ **css in js** ] 같은 경우는 bem과의 조합이 좋지 않아서 오히려 사용하면 모듈화 + 모듈화여서  클래스가 더 알아보기 복잡해지는 문제..

|  |  |
| --- | --- |
|  |  |
|  |  |

### 2. classname 짓기

classname은 styled component 에서도 마찬가지로 이름을 있는건 똑같이 때문에 같은 문제를 가지고 있다. 

### 3. 모듈화

js in css 는 사용자체로 모듈화가 되고 있고, scss와 같은 css 프리프로세서에서는 Nesting과 class 이름을 짓는 방법론을 사용해서 모듈화를 만들수있다. 

header 라는 부분 자체를 모듈화 한다면

```scss
.header {
	&__nav {
		@content;
	}
}
```

위와 같이 사용할 수 있습니다. 

## 스타일 설계와 디자인 시스템

### 컴포넌트 분류

1. 아토믹
- 장 : 파일 분류가 세세하게 되어있어서 사용시 그 부분을 조합하여 사용.
- 단 : 세세한 분류로 되어있어서 전체를 파악하려면 스토리북이나 시스템가이드를 이용해야 하고, 유동적인 변동에 대처가 힘듬.

 2. mixin을 이용한 분류 ( 루트 함수.scss가 있는 경우 )

2-1.  모듈별로 파일이 정해져 있는 경우

┗ 한폴더에 js파일과 같이 묶는 형식 (component> button(button.js, button.scss), label(label.js, label.scss) ) 

┗ 아토믹과는 다르지만 자주 쓰이는 것으로 구분되어진것. label, button,input 등 규모나 형태에 상관없이 그때그때 만드는 편

┗ 각 파일에서 루트 함수를 import, 타입별 정의

┗ 이 부분에서의 js컴포넌트 파일은 기본적으로 디폴트 props를 가지고 있어서 다른 곳에서 독립적으로 사용했을시 오류가 나지 않게 한다. 

- 장: 필요시에 정의된 컴포넌트를 import 하거나, 새로운 타입을 추가할 수 있음.
- 단: 스토리북과의 조합이 좋은 편은 아니라 전체적인 파악이 불편할 수 있음..
    - 단점을 보완하기 위해 스토리북보다는 ( js 와 묶여 있기 때문에 ) ui 를 모아놓은 페이지같은게 있으면 파악에 용이

2-2. 모듈별로 파일이 정해져있지않고, 한 폴더내에서 관리할 경우 ( style or scss 폴더 안에 .scss 파일 일괄 포함 )

┗ js파일과 독립적으로 사용 가능

┗ scss 파일의 mixin 과 모듈을 보고 마크업구조를 맞추고 클래스를 넣는 형식

- 장: js와 독립적이라 유동적인 대처가 가능하다? ( 중간에 태그가 들어갈 경우 모듈.scss에서 수정이 아닌 그 js컴포넌트의 이름의 scss파일 별도 생성 후 거기에 추가 )
- 단: 모듈.scss에 정해져있는 구조대로 마크업과 클래스 명을 넣어야 정해진 모듈대로 맞출수있고, 이 방법 또한 전체적인 구조파악이 어려움.
    - 이 구조 파악은 스토리북이나 ui보다는 메뉴얼( 코드상으로 ) 처럼 있는것이 더 파악에 용이

3. 스타일드 컴포넌트를 이용할 경우( emotion )

  3-1.  아토믹 시스템을 이용할 경우

      ┗ atom, molecules 등 

- 장: 스타일드 컴포넌트와 아토믹의 조합은 상당히 좋아서 스타일드 컴포넌트를 사용하셨다면 그렇게 러닝커브가 높진 않습니다.
- 단: 아토믹의 if, for 문 같은 제어, 반복문은 서로같에 composit(혼합사용)이 불편했던 걸로 기억하고, 이런 기능들이 필요할 정도라면 ui정의가 잘못된거일 가능성이 높습니다.

  3-2. 아토믹 시스템을 이용하지 않을 경우

      ┗ 폴더 안에 컴포넌트와 매치 하는 경우

- 장: 특정 컴포넌트에 사용될 스타일과 로직의 분리. 컴포넌트를 만들때 동시에 작업 가능.
- 단: 정의된 스타일이 많을 수록 import, export 해야될 것이 많아져서 신경써야 하는 부분이 급격히 많아짐 - 이 구조에서의 컴포넌트 설계는 최대한 작은 단위로 쪼개기

      ┗ root 폴더 안에 기능별, 종류별로 분류 되어 있는 경우

- 장: 용도와 기능에 맞게 필요시에만 import 하여 사용.
- 단: 정의된 기능별, 종류별에 대한 내용이 공유가 되지 않거나, 관리가 되지 않는다면 비슷한 기능이 계속 추가될 수도 있는등. 필요없는 작업을 하게 될 수 도 있음.

4. 스타일드 컴포넌트와 scss를 같이 쓸 경우 - 이후의 내용에서 자세히 설명

데이터를 넘기기 위해서 props가 필요할 때가 있지만 관리 및 사용이 불편하고, ㅊ

스타일 작성할때는 css가 편하기 때문에 두 가지의 장점을 혼합해서 하는 방식인데 이론뿐이라 맞춰가면서 사용해야 할듯 싶다.

아토믹은 하나하나 다 규정을 만들어서 규정이 잘 만들어지고, 거대한 틀 안에서 움직인다면 생산성이 제일 좋고, 

좀더 유동적으로 대처를 해야된다면, 

모듈별로 하는것이 나아 보이는데 그중에서도 가볍게 한다면 한 파일내에서 모듈을 관리, 무겁게 한다면 모듈별로 파일을 묶어서 하는 방법이 나아보인다. 

### react에서의 스타일링 정답은?

위의 분석 내용처럼 프로젝트의 예상개발범위를 예측하여 성격, 규모에 따 설계방법이 달라질 수 있습니다.  

**리액트 스타일 패턴 - compound style pattern**

제가 선택한 방법은 혼합사용하는 패턴입니다. 

**사용하지 않을 때의 문제점**

기존 scss로 작업시 단순히 ui만 변경되는 부분일 때 이전에 보았던 type mixin 을 통하여 받는 변수에 따라 분기를 달리하여 ui를 변경하였는데, 

이 방식을 사용할 경우. 만들어진 컴포넌트에 어떤 ui 테마가 있는지 사용하는 개발자는 파악이 어려웠습니다. 

그래서 단순히 테마만 있는 ui는 styled component에. 개발자가 신경을 쓰지 않고, 사용하지 않는 ui는 scss에 정리하여 스타일을 분리했습니다. 

이 패턴은 아직 초기 단계라 아직 단점이 몇몇 존재하는데, 

이전 styled component의 단점인 사용된 선택자가 많을 수록 import 하는 부분이 많아 질 수 있습니다. 

예시 코드를 보겠습니다. 

```scss
// ⓐ 
&.shop {
	position: fixed;
	width: calc(100% - 200px);
	min-height: 52px;
	padding: 9px 16px;
	z-index: 12;
}

// ⓑ
&.is-active {
	background: #f6f7ff;
	color: #4d5eff;
}
```

위 코드에서 ⓐ와 ⓑ는 어떤 차이점이 있을까요? 

ⓐ는 어떠한 ‘클래스’가 shop 클래스를 가지고 있을 때에 대한 ‘테마’ 이고, ⓑ는 어떠한 ‘컴포넌트’가 is-active 라는 클래스를 가지고 있을 때에 대한 ‘상태’ 로 구분할 수 있습니다. 

여기에서 저는 css인데도 불구하고 ‘테마’와 ‘상태’ 라는 말을 사용하였는데, 좀 더 깊게 설명하자면 

‘테마’ : 하나의 레이아웃에 여러 타입이 있는 경우( 헤더 - 상단에 고정된 헤더, 고정되지 않은 헤더, 모바일 헤더 등… )

‘상태’ : 하나의 컴포넌트로 구분될수 있는 모듈에 대한 그 컴포넌트가 가진 상태 

테마는 한번 만들어놓으면 거의 수정되거나 변동될 일이 없으므로 정의된 컴포넌트를 불러올때 (ex : <Header/> ) 이 컴포넌트의 props를 보내 정해진 테마를 사용하고 그 테마는 컴포넌트 내부에 interface로 정의가 되어있으면 개발자가 <Header /> 컴포넌트를 불러올때 어떠한 테마가 지금 정의되 있고, 이 테마를 사용만 하면된다라고만 인식이 되면 ui 개발자는 더이상 관여를 할 필요가 없습니다. 

상태는 input, button 처럼 최소한의 기능을 가진 작은 유닛에 대한 각자의 고유한 상태를 말하고, 이런 각자의 상태들은 이 작은 유닛들이 적용되는 부분마다 미세하게 다르거나, 

button의 여러 타입들과 같이 사용되어 더 많은 타입들이 나올 수도 있습니다. ( small-btn, medium-btn, large-btn * active, disable, default 등 )

### next에서의 스타일 관리

next에서는 scss를 일부분만 사용가능합니다. 

이전 처럼 각 폴더 아래에 js와 1:1매치가 불가능하여 1:1매치를 하려면 module.scss를 사용해야 하거나, scss파일을 root에 넣고 index 에서 전부 import 한 후 사용하는 방법밖에 없습니다. 

### 프론트 개발자와의 협업

- vac 패턴
- 컴포넌트 만들고 주석 달아주기

## 에필로그
