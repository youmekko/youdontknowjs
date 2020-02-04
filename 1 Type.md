# 1 Type

# JS 내장타입

자바스크립트의 내장타입은 null, undefined, boolean, number, string, object, symbol(ES6에서 추가) 이렇게 7개다.

하지만 typeof의 리턴값은 항상 저 7개와 매치되진 않는다.

typeof 가 리턴하는 문자열은 function도 있다.

    typeof function a(){ /* ... */ } === "function"

이것만 보면 function이 마치 최상위 레벨의 내장 타입처럼 보이지만 실제론 object의 하위 타입이다.

구체적으로 설명하면 함수는 '호출 가능한 객체' 라고 명시되어 있다.

참고로 배열은 그냥 객체다.

    typeof [1,2,3] === "object"

하나 이상한 것은 아래다. 주의하자.

    typeof null === "object"

# 값은 타입을 가진다.

값에는 타입이 있지만 변수엔 따로 타입이 없다. 변수는 언제라도 어떤 형태의 값이라도 가질 수 있다.

typeof에 연산자를 대어보는건 "이 변수의 타입은 무엇이니?" 라는 질문과 같지만 실은 타입이란 개념은 변수엔 없으므로 정확히는 "이 변수에 들어있는 값의 타입은 무엇이니" 라고 묻는 것이다.

그렇담 값이 선언되지 않은 변수는?

값이 없는 변수의 값은 undefined이며 typeof의 결과는 "undefined" 이다.

    let a ;
    typeof a === "undefined"
    
    let b = 1;
    let c ;
    b = c;
    
    typeof b === "undefined"
    typeof c === "undefined"

### undefined vs undeclared

    var a;
    
    a; //undefined
    b; //Uncaught ReferenceError: b is not defined
    
    typeof a === "undefined"
    typeof b === "undefined"

주의하자. 에러 메세지도 헷갈리고 선언되지 않은 변수의 type도 undefined이다.

위 처럼 헷갈리게 만들었지만, 브라우저에서 자바스크립트 코드를 처리할 때, 특히 여러 스크립트 파일의 변수들이 전역 네임스페이스를 공유할 때 typeof의 안전 가드는 의외로 쓸모가 있다.

(예) 프로그램의 디버그 모드를 DEBUG라는 전역변수로 만들었다 치자. 최상위 전역 스코프에 아래처럼 선언하고 개발/단계에서 이 파일을 브라우저가 로딩하기만 하면 될 것 이다.

    let DEBUG = true;

그러나 나머지 어플리케이션 코드에서 ReferenceError(undeclared된 상태)가 나지 않게 하려면 전역변수를 체크해야 한다.

    //이렇게 하면 에러가 난다.
    //DEBUG가 undeclared라면 Reference Error가 난다.
    //(메모리에 올라가지 않은 값을 참조할 때)
    if(DEBUG){
    	console.log('디버그 시작')
    }
    
    //이렇게 해야 안전하게 존재 여부를 체크할 수 있다.
    if(typeof DEBUG !== "undefined"){
    	console.log('디버그 시작'
    }