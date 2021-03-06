# 4장 문자와 문자열

# 1. 아스키코드

문자 인코딩은 문자 집합을 메모리에 저장하거나 통신하는데 사용하기 위해 부호하하는 방식을 뜻한다.
문자 집합은 문자를 모아둔 것이다.

0과 1만 아는 컴퓨터에 문자를 인식시키려면 문자를 2진수로 나타내어야 한다.
문자 하나에 정수 하나를 매핑해 두면 이 정수는 특정 문자를 표현하게 된다. 이렇게 매핑된 정수를 코드 포인트라고 하고, 이 코드 포인트들을 모아 놓은 집합을 부화화된 문자 집합(Coded Character Set, CSS)라 한다.

아스키는 대표적인 문자 인코딩 방식이다. 비트는 일곱 개로 문자를 표현하므로 문자를 총 128개까지 표현할 수 있고 코드 포인트 수도 128개다.

대문자 'A'는 10진수로는 65이고 16진수로는 0x41이다. 소문자 'a'는 10진수로는 97이고 16진수로는 0x61이다. 숫자 문자인 '0'은 10진수로는 48이고 16진수로는 0x30이다.

```
>>> ch = 'A'
>>> bch = ch.encode()
>>> bch
b'A'
>>> bch[0]
65
```

마지막 출력 값을 보면 대문자 'A'가 정수 65와 매핑된 것을 확인할 수 있다.


```
>>> ch_0 = '0'
>>> bch_0 = ch_0.encode()
>>> bch_0
b'0'
>>> bch_0[0]
48
```

숫자 문자 '0'은 정수 48과 매핑되었음을 알 수 있다.

# 2. 유니코드

아스키코드의 경우 알파벳은 있지만 한글이 없다.
세계 각국의 사람이 커뮤니케이션을 하기 위해 언어가 더 많이 필요해졌다. 이때 나온 해결 방법이 7비트로 표현한 문자를 16비트로 확장하는 것이었다. 7비트일때는 128개 문자를 표현할 수 있지만 16비트로 확장하면 65,536개 표현이 가능하다. 여기에 더해 수 하나에 다시 문자 하나를 일대일로 대응한 새로운 표를 만들었는데, 이 테이블이 바로 유니코드이다.

기존 아스키코드인 0x0000부터 0x007F까지는 유지하고 그 이후부터 숫자 하나에 문자 하나씩을 대응하였다. 문자에는 한글, 중국어, 일본어 같은 언어 대부분이 포함되었다. 

```
>>> '\uAC00'
'가'
>>> '\uAC01'
'각'
```

# 3. 유니코드 인코딩 방식

- 코드 유닛(code unit)은 코드 포인트를 특정한 방법으로 인코딩했을때 변환되어 얻어지는 비트의 나열을 말한다. 
- 문자 인코딩 방식은 코드 유닛을 옥텟으로 나열하여 변환하는 방법이다. 옥텟은 데이터의 단위로 8비트를 의미한다. 

- 유니코드는 2바이트로 숫자 하나에 문자 하나를 대응하여 표현한다. 기본 다국어 평면을 포함해 평면이 열 입곱개 있으므로 모든 문자를 표현하려면 3바이트가 필요하다.
- 1바이트 정수만 저장할 수 있는 시스템이라면 3바이트짜리 정수는 저장할 수 없으므로 이 시스템에서는 한글을 표현할 수 없다. 그러므로 다양한 코드 유닛을 갖는 인코딩 방식을 두어 유연하게 대처해야 한다.
- 유니코드 인코딩 방식에도 다양한 종류가 있다.

```
>>> ch = '가'
>>> ch.encode()
b'\xea\xb0\x80'
>>> ch.encode('UTF-8')
b'\xea\xb0\x80'
>>> ch.encode('UTF-16')
b'\xff\xfe\x00\xac'
>>> ch.encode('UTF-32')
b'\xff\xfe\x00\x00\x00\xac\x00\x00'
```

## 3.1 UTF-8

UTF-8은 유니코드 인코딩 방식 중 하나로 유니코드 문자 하나를 1바이트에서 4바이트 사이에서 표현한다. 
유니코드 코드 포인트가 U+0000 ~ U+007F는 1바이트, U+0080~U+07FF는 2바이트, U+0800~U+FFFF는 3바이트, 나머지는 4바이트로 표현한다.
문자에 따라 바이트 수가 달라지므로 가변 길이 인코딩 방식이라고 불린다.

```C
char * str1 = u8"가";
```

- 문자열 "가" 앞에 붙은 u8은 UTF-8을 의미한다. str1은 UTF-8로 인코딩된 문자열 "가"가 저장된 메모리 공간을 가리킨다.
- 문자 '가'의 코드포인트 U+AC00은 U+0800~U+FFFF 사이의 수이므로 3바이트로 표현될 것이다. (메모리 주소는 별 의미가 없다.)

문자 '가' UTF-8
메모리 주소0x00126b30
표현 ea b0 80

UTF-8에서 코드 포인트가 U+0800에서 U+FFFF 사이인 문자는 다음과 같은 포멧으로 변환된다. 

<u>111</u>0XXXX 10XXXXXX 10XXXXXX

- 맨 앞에 있는 111은 바이트 수를 나타낸다.
- 1바이트로 표현되면 1, 2바이트로 표현되면 11, 3바이트로 표현되면 111이다.

문자 '가'의 코드 포인트 U+AC00을 2진수로 표현하면 다음과 같다.
1010 1100 0000 0000

포맷의 X를 보면 첫번째 등장하는 X는 4자리, 그 다음은 6자리, 마지막은 6자리이다. 이 자릿수에 맞춰 변환해 둔 2진수를 표현하자
1010 110000 000000

이제 포맷의 X를 이 수로 채우면
11101010 10110000 1000000

이를 16진수로 변환하면 다음과 같다
0xEAB080

## 3.2 UTF-16

UTF-16도 유니코드 인코딩 방식 중 하나로 2바이트 단위로 문자를 표현한다. 해당 문자가 기본 다국어 평면에 있으면 2바이트로 인코딩되고, 그렇지 않으면 4바이트로 인코딩된다. 코드 유닛의 크기는 16비트이다.

```
char16_t * str2 = u"가";
```

문자열 "가"앞에 붙은 소문자 u는 UTF-16으로 인코딩하겠다는 의미이다. char16\_t는 C++언어에서 UTF-16방식으로 변환된 데이터를 담기 위해 만들어진 자료형으로 2바이트이다. 

메모리 주소 0x00FA6B34
표현 00 ac

문자 '가'의 코드포인트인 AC00을 바이트 단위로 순서만 바꾼 모양이다.

## 3.3 UTF-32

UTF-32도 유니코드 인코딩 방식의 하나로 모든 문자를 4바이트로 표현한다. 코드 유닛의 크기는 32비트이다.
```
char32_t * str3 = U"가";
```

# 4. 파이썬 문자열의 특징

C/C++에서는 문자열을 변수로 만들면 요소인 문자를 변경할 수 있고, 문자열을 상수로 만들면 요소를 변경할 수 없다. 즉, 프로그래머가 변경 가능성을 선택할 수 있다.

하지만 파이썬의 경우 문자열은 요소를 변경할 수 없다.
```
>>> string = 'abcde'
>>> string[2] = 'a'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  TypeError: 'str' object does not support item assignment
```


