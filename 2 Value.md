# 2 Value

# 배열

배열 값이 delete 연산자를 적용하면 슬롯을 제거할 수 있지만 마지막 원소까지 제거해도 length 프로퍼티 값이 바뀌진 않는다.

    let a = [1, 2, 3]
    a.length //3
    
    delete a[0] 
    a; [empty, 2, 3]
    a.length //3
    
    delete a[1]
    a; [empty x 2, 3]
    a.length //3

구멍난 배열을 다룰 때는 주의하자

    let a = [];
    a[0] = 1;
    a[2] = [3]
    
    a[1] //undefiend
    
    a.length //3
    
    a; //[1, empty, Array(1)]
    
    a[1] = undefined;
    a; //[1, undefined, Array(1)]

a[1] 슬롯값은 응당 undefined가 될 것 같지만 명시적으로 a[1] = undefined 세팅한 것과 똑같지는 않다.

배열의 인덱스는 숫자인데 배열도 하나의 객체여서 키/프로퍼티 문자열을 추가할 순 있다. 하지만 length가 증가하지는 않는다.

    let a = []
    
    a[0] = 1;
    a['foo'] = 2;
    
    a.length; //1
    a['foo']; //2
    a.foo //2

그런데 키로 넣은 문자열이 표준 10진수 숫자로 타입이 바뀌면 마치 문자열 키가 아닌 숫자 키를 사용한 것과 같은 결과를 초래한다.

    let a = [];
    a["10"] = 1;
    a.length // 11
    a; //[empty x 10, 1]

일반적으로 배열에 문자열 타입의 키/프로퍼티를 두는건 추천하지 않으며 그렇게 해야한다면 객체를 대용하고 배열 원소의 인덱스는 확실히 숫자만 쓰자.

### 유사배열

일단 유사배열이 뭐지?

    let arr = [1,2,3] 
    arr // [1,2,3]
    
    //이런것들이 유사배열이다.
    let nodes = document.querySelector('div'); //NodeList [div, div, div, ...]
    let els = document.body.children; //HTMCollection [noscript, link, div, script, ...]
    
    Array.isArray(arr); //true
    Array.isArray(nodes); //false
    Array.isArray(els); //false
    
    arr isnstanceof Array; //true
    nodes isnstanceof Array; //false
    els isnstanceof Array; //false

직접 배열 리터럴로 선언한 arr만 배열이다.

    let yoosa = {
    	0: 'a',
    	1: 'b',
    	2: 'c',
      length: 3
    }

이런것도 유사배열이다. 위에서 살펴본 배열도 객체라는 성질을 이용한 트릭이다.

배열과 유사배열을 구분해야하는 이유는 유사배열의 경우 배열의 메서드를 쓸 수 없기 때문이다.

    array.forEach(function(el) { console.log(el); }); // 1, 2, 3
    els.forEach(function(el) { console.log(el); }); // Uncaught TypeError: els.forEach is not a function

위의 els에 forEach같은 배열 메서드를 사용하면 에러가 발생한다. (nodes는 프로토타입에 forEach가 있어서 된다.) 배열이 아니므로 에러가 발생하는 것이다. 이럴 때 메서드를 빌려쓰는 방법이 있는데, 배열 프로토 타입에서 forEach메서드를 빌려온는 것이다. **call** 이나 **apply**를 이용하면 된다.

    Array.prototype.forEach.call(nodes, function(el){
    	console.log(el)
    })
    
    [].forEach.call(els, function(el){
    	console.log(el)
    })

이렇게 하면 유사배열에서도 배열 메서드를 사용할 수 있다. 

ES6부터는 Array.from 으로 더 간단하게 할 수 있다.

    Array.from(nodes).forEach(function(el) {
    	console.log(el)
    })

ES6에서는 더 이상 안보이지만 유사배열이 한개 더 있다. function의 arguments다. 

    function arrayLike() {
    	console.log(arguments);
    }
    
    arrayLike(4, 5, 6)

    function arrayList(){
    	console.log(arguments)
    	[].forEach.call(arguments, function(el) {
    		console.log(el)
    	})
    }
    
    arrayList(4, 5, 6)

[ ] 로 감싸져 있다고 다 같은 배열이 아니라는 것,

Array.isArray 또는 arr instanceof Array로 판별하는 것,

배열 프로토 타입에서 메서드를 빌려쓴느 방법에 대해서 알아두면 된다.

참고 h[ttps://www.zerocho.com/category/JavaScript/post/5af6f9e707d77a001bb579d2](https://www.zerocho.com/category/JavaScript/post/5af6f9e707d77a001bb579d2)

# 문자열

문자열은 단지문자의 배열이라고 생각한다. 자바스크립트 문자열은 실제로 생김새만 비슷할 뿐 문자 배열과 같지 않다. 

물론 겉모습은 많이 닮았다. length 프로퍼티를 가지고, concat() 메서드를 가진다.

**하지만 문자열은 불변 (immutable) 값이고 배열은 가변(mutable) 값 이다.**

한가지 더, 문자열은 불변 값이므로 문자열 메서드는 그 내용을 바로 변경하지 않고 항상 새로운 문자열을 생성한 후 반환한다. 반면에 대부분의 배열 메서드는 그 자리에서 바로 원소를 수정한다.

    let a = 'foo';
    let b = ['f', 'o', 'o'];
    
    let c;
    c = a.toUpperCase();
    
    a === c; //false
    
    b.push('!')
    b; //[['f', 'o', 'o', '!']

그리고 문자열을 다룰 때 유용한 대부분의 배열 메서드는 사실상 문자열에 쓸 수 없지만, 배열의 불변 메서드를 빌려 쓸 수는 있다.

    a.join //undefined
    a.map //undefined
    
    let c = Array.prototype.join.call(a, "-")
    let d = Array.prototype.map.call(a, function(v){
    	return v.toUpperCase() + '.';
    }).join("");
    
    c; //"f-o-o"
    d; //"F.O.O"

다음은 문자열의 순서를 거꾸로 뒤집는 코드다. 배열에는 reverse()  라는 가변 메서드가 준비되어 있지만 문자열은 그렇지 않다.

    a.reverse; //undefined
    
    b.reverse(); //['o', 'o', 'f']

문자열은 불변 값이라 바로 저장되지 않으므로 배열의 가변 메서드는 통하지 않고 그래서 빌려쓰는것 또한 안된다.

    Array.prototyype.reverse.call(a) 
    /*
    여전히 String 객체 래퍼를 반환한다. 
    라고 책에 나와있지만 콘솔에서 확인 결과 undefined이 나온다.
    VM915:1 Uncaught TypeError: Cannot assign to read only property '0' of object '[object String]'
    */

이런 경우 문자열을 배열로 바꾸고 원하는 작업을 수행한  후 다시 문자열로 되돌리는 것이 꼼수이다. (이런걸 Hack이라고 한다) 영 내키진 않지만 단순 문자열에 대해 좀 지저분 하더라도 빠르게 써먹을 방법이 필요하다면 이런 방법도 나쁘지는 않다.

    let c = a.split("").reverse().join("") //"oof"

문자열 자체에 어떤 작업을 빈번하게 수행하는 경우라면 관점을 달리해 문자열을 문자 단위로 저장하는 배열로 취급하는 것이 더 나을 수도 있다. 문자열 ↔ 배열 변환을 매번 번거롭게 신경쓰지 않아도 되니 시간과 노력을 아낄 수 있다. 문자열로 나타내야 할 때는 언제나 배열에 join(" ") 메서드를 호출 하면 된다.

# 숫자

자바스크립트의 숫자 타입은 number가 유일하고 정수(Integer)와 부동 소수점 수자(Factional Decimal Number)를 모두 아우른다.

아주 크거나 작은 숫자는 지수형(Exponent Form)으로 표시하며, toExponential()의 메서드의 결과 값과 같다.

    var a = 5E10;
    a; //50000000000
    a.toExponentila() //"5e+10"
    
    var b = a * a;
    b; //2.5e+21
    
    var c = c / a;
    c; //2e-11

숫자 값은 Number 객체 Wrapper로 박싱 할 수 있기 때문에 Number.prototype 메서들 접근 할 수도 있다. 

(예) toFixd : 지정한 소수점 이하 자리 수까지 나타낸다. 리턴은 문자열이다.

    var a = 42.59;
    a.toFixed(0); //"43"
    a.toFixed(1); //"42.6"
    a.toFixed(2); //"42.59"
    a.toFixed(3); //"42.590"
    a.toFixed(4); //"42.59000"

(예) toPrecision() : toFixed와 기능은 비슷하지만 유효 숫자 개수를 지정할 수 있다.

    var a = 42.59;
    a.toPrecision(1); //"4e+1"
    a.toPrecision(2); //"43"
    a.toPrecision(3); //"42.6"
    a.toPrecision(4); //"42.59"
    a.toPrecision(5); //"42.590"
    a.toPrecision(6); //"42.5900"

위 두 메서드의 경우 숫자 리터럴에서 바로 접근할 수 있으므로 굳이 변수를 만들어서 할당하지 않아도 된다. 하지만 . 이 소수점 일 경우엔 프로퍼티 접근자가 아닌 숫자 리터럴의 일부로 해석되므로 . 연산자를 사용할 때는 조심해야 한다.

    //잘못된 구문
    //아래서의 . 이 42. 의 일부가 되어버려 .toFixed 메서드에 접근 할 수 없다.
    42.toFixed(3); //Syntax Error
    
    //올바른 구문
    (42).toFixed(3); //"42.000"
    0.42.toFixed(3): //"0.420"
    42..toFixed(3); //"42.000"
    42 .toFixed(3); //"42.000"

이렇게 원시값이 메서드를 직접 호출 할 일은 거의 없지만 알아두자.

큰 숫자는 보통 지수형으로 표시한다.

    var onethousnad = 1E3; //1 * 103^3
    var onemillionehundrendthousand = 1.1E6; //1.1 * 10^6

숫자 리터럴은 2진, 8진, 16진 등 다른 진법으로도 나타낼 수 있다.

    0xf3; //243의 16진수
    0Xf3; //위와 같다
    
    //ES6 + 엄격모드에서는 0363처럼 0을 앞에 붙여 8진수를 표시하지 못한다.
    0363; //243의 8진수

ES6부터는 다음과 같이 쓸수도 있다.

    0o363; ///243의 8진수
    0O363; //위와 같음
    
    0b11110011; //243의 이진수
    0B11110011: //위와 같은

### 작은 소수 값

    0.1 + 0.2 === 0.3; //false

수식만 보면 분명 ture인데 결과는 false이다.

간단히 말하면 이진 부동 소수점으로 나타낸 0.1과 0.2는 원래의 숫자와 일치하지 않는다. 그래서 둘은 결과 역시 정확히 0.3이 아니다.

실제로는 0.30000000000000004에 가깝지만 가깝다고 해도 같은 것은 아니다.

    0.1 + 0.2 === 0.30000000000000004 //true가 되기는 한다.

그렇다면 0.1 + 0.2와 0.3 를 어떻게 비교해야 할까?

가장 일반적으로는 미세한 '반올림 오차'를 '허용 공차(Tolerance)' 로 처리하는 방법이 있다. 이런 미세한 오차는 '머신 입실론(Machine Epsilon)' 이라고 하는데 자바스크립트 숫자의 머신 입실론은 2-52(2.220446049250313e-16)다.

ES6부터는 이 값이 Number.EPSILON으로 미리 정의되어 있으므로 필요시 사용하면 되고 ES6 이전 부라우저는 다음과 같이 폴리필을 대신 사용한다.

    if(!Number.EPSILON){
    	Number.EPSILON = Math.pow(2, -52)
    }

Number.EPSILON으로 두 숫자의 (반올림 허용 오차이내의) 동등함을 비교할 수 있다.

    function numbersCloseEnoughToEqual(n1, n2) {
    	return Math.abs(n1 - n2) < Number.EPSILON
    }
    
    var a = 0.1 + 0.2
    var b = 0.3
    
    numbersCloseEnoughToEqual(a, b) //true
    numbersCloseEnoughToEqual(0.0000001, 0.0000002) //true

부동 소수점 숫자의 최대값은 대략 1.798e+308 이고(엄청 큰 숫자다.) Number.MAX_VALUE로 정의되며, 최솟값은 5e-324로 음수는 아니지만 거의 0에 가까운 숫자고 Number.MIN_VALUE로 정의한다.

### 안전한 정수 범위

숫자를 표현하는 방식이 이렇다 보니 정수는 Number.MAX_VALUE보다 훨씬 작은 수준에서 안전 값의 범위가 정해져 있다.

안전하게 표현할 수 있는(즉, 표현한 값과 실제 값이 정확하게 일치한다고 장담할 수 있는) 정수는 최대 2^53 - 1 (9007199254740991)이다. 세 자리 콤마를 찍어보면 얼추 9천조가 넘는 여유로운 숫자다.

이 값은 ES6에서는 Number.MAX_SAFE_INTEGER로 정의한다. 최소값은 Number.MIN_SAFE_INTEGER로 정의하며 -9007199254740991이다.

자바스크립트에서 이처럼 아주 큰 숫자에 맞딱드리는 경우는 데이터 베이스등의 64비트 ID를 처리할 때가 대부분이다. 64비트 숫자는 숫자 타입으로 정확하게 표시할 수 없으므로 (보내고 받을 떄) 자바스크립트 string 타입으로 저장해야 한다.

다행이 그리 큰 ID 값을 숫자로 연산할 일은 흔치 않지만 아주 큰 수를 다룰 수 밖에 없는 상황이라면, 지금으로선 큰 수 유틸리티 사용을 권한다.

### 정수인지 확인

ES6 부터는 Number.inInteger() 로 어떤 값의 정수 여부를 결정한다.

    Number.isInteger(42); //true
    Number.isInteger(42.000); //true
    Number.isInteger(42.3); //false

ES6 이전 버전을 위한 폴리필은 아래와 같다.

    if(!Number.isInteger){
    	Number.isInteger = function(num){
    		return typeof num == "number" && num % 1 === 0;
    	}
    }

안전한 정수 여부는 ES6부터 Number.isSafeInteger()로 체크한다.

    Number.isSafeInteger(Number.MAX_SAFE_INTEGER); //true
    Number.isSafeInteger(Max.pow(2, 53); //false
    Number.isSafeInteger(Max.pow(2, 53) - 1); //true

폴리필은 아래와 같다.

    if(!Number.isSafeInteger){
    	Number.isSafeInteger = function(num){
    		return Number.isInteger( num ) &&
    			Math.abs( num ) <= Number.MAX_SAFE_INTEGER;
    	};
    }

### 32비트 (부호 있는 정수)

정수의 안전 범위가 대략 9천조(53비트)에 이르지만 (비트 연산처럼) 32비스 숫자에만 가능한 연산이 있으므로 실제 범위는 훨신 줄어든다.

따라서 정수의 안전한 번위는 Math.pow(-2, 31) (-2147483648, 약 -21억) 에서 Math.pow(2, 31)-1 (2147483648, 약 +21억) 까지다.

a | 0 과 같이 쓰면 '숫자 값 → 32비트 부호가 있는 정수' 로 강제 변환을 한다. | 비트 연산자는 32비트 정수값에만 쓸 수 있기 때문에 (즉, 32비트까지만 관심을 가지기 때문에 그 상위 비트는 소실됨) 가능한 방법이다. 0과의 OR 연산은 보진적으로 NOOP 비트 연산과 같다.

# 특수 값

## undefined

느슨한 모드에서는 전역 스코프에서 undefined 식별자에 값을 할당할 수 있다. 절대 추천하진 않지만.

    function foo() {
    	undefined = 2;
    }
    foo();
    
    function foo(){
    	'use strict'
    	undefined = 2; //타입에러발생
    }
    
    foo();

그런데 모드와 상관없이 undefined 라는 이름을 가진 지역 변수는 생성할 수 있다. 가능하기는 하지만 생각만 해도 끔찍하다.

    function foo(){
       'use strict'
        var undefined = 2;
        console.log(undefined)
    }

### void 연산자

void 연산자는 내장 식별자로, 값은 undefined이지만 이 값은 void 연산자로도 얻을 수 있따.

표현식 void ~~는 어떤 값이든 무효로 만들어 항상 결과 값을 undefined로 만든다. 기존 값은 건드리지 않고 연산 후 값은 복구 할 수 없다.

    let a = 42;
    console.log(void a, a); //undefined 42

(명확하게 void true라고 하던가, 동일한 기능을 void 표현이 있는데도, 대게 C 언어 프로그래밍에서 유래된) 관례에 따라 void 만으로 undefined 값을 나타내려면 void 0 이라고 쓴다. void 0, void1, undefined 모두 같다.

void 연산자는 어떤 표현식의 결과 값이 없다는 걸 확실히 밝혀야 할 때 긴요하다.

    function doSomething() {
    	if(!ready){
    		return void setTimeout(doSomething, 100);
     	}
    	let result;
      //별도 처리 수행
    	return result;
    }
    
    //제대로 처리 됐으면!
    if(doSomething) {
    	//다음 작업 바로 실행
    }

setTimeout() 함수는 숫자 값 (타이머를 취소할 때 사용할 타이머의 고유 식별자)를 반환하지만 예제에서는 이 숫자 값을 무효로 만들어 doSomething() 함수의 결과 값이 if 문에서 긍정 오류가 일어나지 않게 했다.

이 때 다음 코드처럼 두 줄로 분리해서 쓰는걸 선호하는 개발자들이 많고 void 연산자는 잘 쓰지 않는다.

    if(!App.ready)
    	setTimeout(doSomething, 100)
    	return
    }

정리하자면 void 연산자는 (어떤 표현식으로부터) 값이 존재하는 곳에서 그 값이 undefined가 되어야 좋을 때만 사용하자. 아마도 그렇게 해야 하는 경우도 거의 없고 극히 제한적으로 쓰이겠지만 제법 쓸모는 있다.

### 특수 문자

숫자 타입에는 몇 가지 특수한 값이 있다.The not number, number.

수학 연산시 두 피연산자가 전부 숫자(또는 평범한 숫자로 해석 가능한 10진수 또는16진수)가 아닐 경우 유효한 숫자가 나올 수 없으므로 그 결과는 NaN 이다.

NaN은 글자 그대로 Not a Number(숫자 아님) 이다. 그런데 이 명칭과 설명이 아주 형편없고 오해의 소지가 다분하다. NaN은 숫자 아님 보다 유효하지 않은 숫자, 실패한 숫자, 또는 몹쓸 숫자라 표현하는게 차라리 정확하다.

    let a = 2 / 'foo'; //NaN
    typeof a === "number"; //true

즉, NaN (숫자 아님)의 타입은 숫자다! 란 뜻이다... 

NaN은 경계 값(Sentinel Value)의 일종으로 (또는 특별한 의미를 부여한 평범한 숫자값으로) 숫자 집합 내에서 특별한 종류의 에러 상황을 나타낸다. ("난 당신이 내준 수학 연산을 해봤지만 실패했어 그러니 여기 실패한 숫자를 도로 가져가")

어떤 변수값이 특수한 실패 숫자, 즉 NaN인지 여부를 확인할 때 null이나 undefined 처럼 NaN도 직접 비교하고 싶은 충동이 생기겠지만.. 틀렸다.

    var a = 2 / 'foo';
    a == 'NaN'; //false
    a === 'NaN'; //false

NaN은 어떤 NaN과도 동일하지 않다. 즉 자기 자신과도 같지 않다. 사실상 반사성이 없는 (x === x 로 식별되지 않는) 유일무이한 값이다. 따라서 NaN !== NaN이다.

비교 불능이라면 NaN 여부는 어떻게 확인 할 수 있을까? 내장 전역 유틸리티인 isNaN() 함수가 NaN 여부를 발해준다.

    var a = 2 / 'foo';
    isNaN(a); //true

하지만 isNaN() 에도 치명적인 결함이 있다. 이 함수는 NaN의 의미를 너무 글자 그대로만 해석해서 실제로 인자 값이  숫자인지 여부를 평가하는 기능이 전부다. 

    var a = 2 / 'foo'
    var b = 'foo'
    
    a; //NaN
    b; //"foo"
    
    window.isNaN(a); //true
    window.isNaN(b); //ture

ES6부터는 Number.isNaN()이 등장한다. ES6 이전의 브라우저에서는 다음 폴리필을 쓰면 안전하게 NaN 여부를 체크할 수 있다.

    if(!Number.isNaN) {
    	Number.isNaN = function(n) {
    		return (
    			typeof n === "number" && window.inNaN(n)
    		);
    	};
    };
    
    var a = 2 / 'foo';
    var b = 'foo';
    
    Number.isNaN(a); //true
    Number.isNaN(b); //false

NaN이 자기 자신과도 동등하지 않는 독특함을 이용해 폴리필을 더 간단히 구현할 수도 있다. 

    if(!Number.isNaN) {
    	Number.isNaN = function(n) {
       return n !== n
    	};
    }

되게 이상해보이지만 잘 동작한다.

실제로 많은 자바스크립트 코드에 NaN은 고의로/실수로 박혀있다. 의미를 오해하지 않고 바르게 쓰려면 Number.isNaN() 같은 내장 유틸리티를 사용하자.

### 0

자바스크립트에 보통의 0과 음의 0 (-0) 이 존재한다.

    var a = 0 / -3; //-0
    var b = 0 * -3; //-0

그러나 명세에 의하면 -0을 문자열 화 하면 항상 0 이다.

    var a = 0 / -3;
    a; //-0
    
    a.toString(); //"0"
    a + ""; //"0"
    String(a); //"0"
    JSON.stringify(a); //"0"

신기하게도 반대로 하면 (문자열에서 숫자로 바꾸면) 있는 그대로 보여준다.

    +"0"; //-0
    Number("-0"); //-0
    JSON.parse("-0"); //-0

-0 을 문자열화 할 때 진짜 값을 감춰버리는 것 말고도 비교 연산 결과 역시 (고의로) 거짓말을 한다.

    var a = 0;
    var b = 0 / -3;
    
    a == b; //true
    -0 == 0; //true
    
    a === b; //true
    -0 === 0; //true
    
    0 > -0; //false
    a > b; //false

확실하게 -0과 0을 비교하고 싶다면 콘솔 창 결과에만 의존할 게아니라 조금 더 영리해야 한다.

    function isNegZero(n) {
    	n = Number(n);
    	return (n === 0) && (1 / n === -Infinity);
    }
    
    isNegZero(-0); //true
    isNegZero(0/-3); //true
    isNegZero(0); //false

그런데 -0은 왜 만들걸까?

값의 크기로 어떤 정보(예: 애니메이션 프레임당 넘김 속도)와 그 값의 부호로 또 다른 정보(예: 넘김 방향)를 동시에 나타내야 하는 어플리케이션이 있기 때문이다.

+0, -0 개념이 없다면 어떤 변수값이 0에 도달하여 부호가 바뀌는 순간, 그 직전까지 이 변수의 이동 방향은 무엇인지 알 수 없으므로 부호가 다른 두 0은 유용하다. 즉, 잠재적인 저오 소실을 방지하기 위해 0의 부호를 보전한 셈이다.

### 특이한 동등 비교

앞서 설명했뜬이 NaN과 -0의 동등 비교는 독특하다.

하지만 ES6부터는 잡다한 예외를 걱정하지 않아도 두 값이 절대적으로 동등한지를 확인하는 새로운 유틸리티를 제공한다. 바로 Object.is() 다.

    var a = 2 / 'foo';
    var b = -3 * 0;
    
    Object.is(a, NaN); //true
    Object.is(b, -0); //true
    Object.is(b, 0); //false

ES6 이전 환경에서도 Object.is() 폴리필을 만들어 간단히 쓸 수 있다.

    if(!Object.is) {
    	Object.is = function(v1, v2) {
    	//-0 테스트
    	if(v1 === 0 && v2 === 0) {
    		return 1 / v1 === 1 / v2;
    	}
    
    	//NaN테스트
    	if(v1 !== v1) {
    		return v2 !== v2;
    	}
    
    	//기타
    	return v1 === v2
    	};
    }	

== 나 ===가 안전하다면 굳이 Object.is()를 사용하지 않는 편이 좋다. (4장 강제변환 참조) 아무래도 기본 연산자가 조금 더 효율이 좋고 일반적이기 때문이다. Object.is()는 특이한 동등 비교에 쓴다.

# 값 VS 레퍼런스

자바스크립트는 포인터라는 개념 자체가 없고 참조하는 방법도 조금 다르다. 우선 어떤 변수가 다른 변수를 참조할 수 없다. 그냥 안된다.

자바스크립트에서는 레퍼런스는 (공유된) 값을 가리키므로 서로 다른 레퍼런스가 있따면 이들은 저마다 항상 공유된 단일 값 (서로에 대한 레퍼런스/ 포인터 따위는 없다.) 을 개별적으로 참조한다.

더구나 자바스크립트에는 값 또는 레퍼런스의 할당 및 전달을 제어하는 구문 암시(Syntax hint)가 전혀 없다.

예를 들면

    var a = 2;
    var b = a;
    b++;
    a; //2
    b; //3
    
    var c = [1, 2, 3];
    var d = c; //d는 공유된 [1, 2, 3] 값의 레퍼런스다.
    d.push(4);
    c; //[1, 2, 3, 4]
    d; //[1, 2, 3, 4]

null, undefined, string, number, boolean 그리고 ES6의 symbol 같은 단순 값 (스칼라 원시 값 Scalar Primitives)은 언제난 값 -복사 방식으로 할당, 전달 된다.

객체 (배열과 박싱된 객체 래퍼 전체 - 3장 네이티브 참고)나 함수 등 합성 값(compound values)은 할당, 전달시 반드시 레퍼런스 사본을 생성한다.

레퍼런스는 변수가 아닌 값 자체를 가리키므로 A 레퍼런스로 B 레퍼런스가 가리키는 대상을 변경 할 수는 없다.

    var a = [1, 2, 3]
    var b = a;
    a; // [1, 2, 3]
    b; // [1, 2, 3]
    
    b = [4, 5, 6]
    a; //[1, 2, 3]
    b; //[4, 5, 6]

함수 인자 역시 가장 자주 헷갈리는 부분이다.

    function foo(x) {
    	x.push(4);
    	x; //[1, 2, 3, 4]
      
      x = [4, 5, 6];
    	x.push(7);
    	x; //[4, 5, 6, 7]
    }
    
    var a = [1, 2, 3]
    
    foo(a);
    
    a; //[1, 2, 3, 4]

a를 인자로 넘기면 a의 레퍼런스 사본이 x로 할당된다. x와 a는 모두 동일한 [1, 2, 3]을 가리키는 별도의 레퍼런스다. 이제 함수 내부에서 이 레퍼런스를 이용해 값 자체를 변경한다. (x.push(4)) 하지만 그 후 x = [4, 5, 6] 으로 새 값을 할당해도 초기 레퍼런스가 a 가 참고하고 있던 값에는 아무 영향이 없다. 즉 a 레퍼런스는 여전히 [1, 2, 3, 4] 값을 바라보고 있다.

레퍼런스로 x로 a가 가리키고 있는 값을 바꿀 도리는 없다. 다만 a와 x 둘다 가리키는 공유 값의 내용만 바꿀 수 있다.

배열을 새로 생성하여 할당하는 식으로는 a의 내용을 [4, 5, 6,7]로 바꿀 수 없다. 기존에 존재하는 배열 값만 변경해야 한다.

 

    function foo(x) {
    	x.push(4)
    	x; //[1, 2, 3, 4]
    
      x.length = 0;
    	x.push(4, 5, 6, 7)
    	x; //[4, 5, 6, 7]
    }
    
    var a = [1, 2, 3]
    
    foo(a);
    
    a; //[4, 5, 6, 7]

값 복사냐 레퍼런스 복사냐는 내 마음대로 결정할 수 없음을 기억하자. 전적으로 값의 타입을 보고 엔진의 재량으로 결정된다.

(배열 같은) 합성 값을 값-복사에 의해 효과적으로 전달하려면 손수 값의 사본을 만들어 전달한 레퍼런스가 원본을 가리키지 않게 하면 된다.

    foo(a.slice());

인자 없이 slice() 를 호출하면 전혀 새로운 배열의 (얕은 복사 shallow copy) 사본을 만든다. 이렇게 복사한 사본만 가리키는 레퍼런스를 전달하니 foo()는 a의 내용을 건드릴 수 없다.

반대로 스칼라 원시 값을 레퍼런스처럼 바뀐 값이 바로바로 반영되도록 넘기려면 원시 값을 다른 합성 값 (객체, 배열 등)으로 감싸야 한다.

    function foo(wrapper){
    	wrapper.a = 42;
    }
    
    var obj = {
    	a: 2
    }
    
    foo(obj);
    obj.a = 42;

같은 원리로 2와 같은 스칼라 원시 값을 레퍼런스 형태로 넘기려면 Number 객체 래퍼로 원시 값을 박싱하면 된다.

Number 객체의 레퍼런스 사본이 함수에 전달되는 것은 맞지만 아쉽게도 공유된 객체를 가리키는 레퍼런스가 있다고 해도 자동으로 공유된 원시 값을 변경할 권한이 주어지는 것은 아니다.

    function foo(x) {
    	x = x + 1;
    	x; //3
    }
    
    var a = 2;
    var b = new Number(a); //Object(a) 와도 같은 표현이다.
    
    foo(b)
    
    b; //2

문제는 스칼라 원시 값이 불변이라는 점이다. (문자열, boolean도 마찬가지다.) 스칼라 원시값 2를 가진 Number 객체가 있다면, 이와 동일한 객체가 다른 원시 값을 가지도록 변경 할 수 없다. 단지 다른 값을 넣은 완전히 별개의 Number 객체를 생성할 수는 있다.

표현식 x + 1에서 x가 사용될 때 내부에 간직된 스칼라 원시 값 2는 Number 객체에서 자동 언박싱 (추출) 되므로 x = x + 1의 x 공유된 레퍼런스에서 Number 객체로 아주 교모하게 뒤바뀌고 2 + 1 덧셈 결과인 스칼라 원시값인 3을 가지게 된다. 따라 바깥의 b는 원시값 2를 씌운, 변경되지 않은, 불변의 원본 Number 객체를 잠조한다.

Number 객체에 프로퍼티를 추가하고 간접적이나마 추가된 프로퍼티를 통해 정보를교환 할 수는 있다. 그러나 별로 일반적이지도 않을 뿐더러 많은 개발자가 좋은 습관이라고 생각하지 않는다.

레퍼런스는 꽤 강력하지만 이따금 걸림돌이 되기도 하고 심지어 존재하지도 않는 래퍼를 찾아 정처 없이 해메기도 한다. 값 복사냐 레퍼런스 복사냐를 결정하는 유일한 단서는 타입 뿐이므로 사용할 값 타입을 잘 정해서 간접적으로 할당, 전달 로직에 반영해야 한다.

# 정리

- 자바스크립트 배열은 모든 타입의 값들을 숫자로 인덱싱한 집합이다. 문자열은 일종의 '유사배열' 이지만  나름 특성이 있기 때문에 배열로 다루고자 할 때는 조심한느 것이 좋다.
- 자바스크립트의 숫자는 정수와 부동 소수점 숫자 모두 포함한다.
- 원시 타입에는 몇몇 특수 값이 있다. null 타입은 nul 이란 값 하나 뿐이고 undefined 타입도 값은 undefined 뿐이다. undefined 는 할당된 값이 없다면 모든 변수/프로퍼티의 디폴트 값이다.
- void 연산자는 어떤 값이라도 undefined로 만들어 버린다.
- 숫자에는 NaN, +Infinity, -Infinity, -0 같은 특수 값이 있다.
- 단순 스칼라 원시 값 (문자열, 숫자 등)은 값- 복사에 의해, 합성 값(객체 등)은 레퍼런스 - 복사에 의해 값이 할당, 전달 된다. 자바스크립트에서 레퍼런스는 다른 언어의 레퍼런스/포인터와 전혀 다른 개념으로 또 다른 변수,레퍼런스가 아닌 오직 자신의 값 만을 가리킨다.