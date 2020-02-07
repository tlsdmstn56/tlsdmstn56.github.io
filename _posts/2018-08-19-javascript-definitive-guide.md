---
layout: single
title: "[요약] Javascript Definitive Guide"
tags: [javascript, 프로그래밍, 코딩]
author_profile: true
---

JavaScript - The Definitive Guide(David Flanagan)에서 몰랐거나 중요하다고 생각하는 부분을 정리하였습니다.

## 숫자형 타입(number)

* 정수, 실수 구분 없이 64비트 부동소수점 타입으로 저장한다.
* 단, 배열 인덱싱이나 비트 연산시에는 32비트 정수를 사용한다.

* 16진법의 숫자 표기(e.g. 0xaf)는 표준으로 지원한다.
* 8진법의 숫자 표기(e.g. 012)는 일부 구현만 지원하므로 사용하지 않는 것이 좋다.

오버플로우, 언더플로우, 0으로 나누기 등의 상황에서 에러를 발생시키지 않고 특별한 값을 반환한다.
* 오버플로우: `Infinity`, `NUMBER.POSITIVE_INFINITY`
* 언더플로우: `-Infinity`, `NUMBER.NEGATIVE_INFINITY`
* 0을 0으로 나누기: `NaN`
* `0`과 `-0`

```javascript
var zero = 0;     // Regular zero
var negz = -0;    // Negative zero 
zero === negz     // => true: zero and negative zero are equal
1/zero === 1/negz // => false: infinity and -infinity are not equal
```

실수 비교는 위험하다. 다음 예시를 보자.

```javascript
var x = .3 - .2;  // thirty cents minus 20 cents
var y = .2 - .1;  // twenty cents minus 10 cents
x == y            // => false: the two values are not the same!
x == .1           // => false: .3-.2 is not equal to .1
y == .1           // => true: .2-.1 is equal to .1
```

`Date()` 객체

```javascript
var then = new Date(2010, 0, 1);
var later = new Date(2010, 0, 1, 17, 10, 30);
var now = new Date();
var elapsed = now - then;
later.getFullYear()
later.getMonth()
later.getDate()
later.getDay()
later.getHours()
later.getUTCHours()
later.toString()
later.toUTCString()
later.toLocaleDateString()
later.toLocaleTimeString()
later.toISOString()
```

## 문자열

문자열은 UTF-16을 사용하여 한 문자당 16비트씩 차지한다. 단, 반드시 모든 유니코드 문자가 16비트 안에 들어오는 것은 아니기 때문에 아래와 같은 상황이 발생할 수는 있다.

```javascript
var p = "π"; // π is 1 character with 16-bit codepoint 0x03c0
var e = "e"; // e is 1 character with 17-bit codepoint 0x1d452
p.length     // => 1: p consists of 1 16-bit element
e.length     // => 2: UTF-16 encoding of e is 2 16-bit values: "\ud835\udc52"
```

관련 메소드들

```javascript
var s = "hello, world"// Start with some text.
s.charAt(0)           // => "h": the first character.
s.charAt(s.length-1)  // => "d": the last character.
s.substring(1,4)      // => "ell": the 2nd, 3rd and 4th characters.
s.slice(1,4)          // => "ell": same thing
s.slice(-3)           // => "rld": last 3 characters
s.indexOf("l")        // => 2: position of first letter l.
s.lastIndexOf("l")    // => 10: position of last letter l.
s.indexOf("l", 3)     // => 3: position of first "l" at or after 3

/* ECMA5 */
s[0] // => 'h'
```

### 패턴매칭

`RegExp()`

```javascript
var text = "testing: 1, 2, 3"; // Sample text
var pattern = /\d+/g           // Matches all instances of one or more digits
pattern.test(text)             // => true: a match exists
text.search(pattern)           // => 9: position of first match
text.match(pattern)            // => ["1", "2", "3"]: array of all matches
text.replace(pattern, "#");    //=> "testing: #, #, #"
text.split(/\D+/);             // => ["","1","2","3"]: split on non-digits
```