- [Primitive Type](#primitive-type)
  - [정수 타입](#정수-타입)
    - [Byte](#byte)
      - [값의 범위](#값의-범위)
      - [기본 값](#기본-값)
    - [기타](#기타)
    - [Char](#char)
    - [Short](#short)
      - [값의 범위](#값의-범위-1)
      - [기본 값](#기본-값-1)
    - [Int](#int)
      - [값의 범위](#값의-범위-2)
      - [기본 값](#기본-값-2)
    - [Long](#long)
      - [값의 범위](#값의-범위-3)
      - [기본 값](#기본-값-3)
  - [실수 타입](#실수-타입)
    - [Float](#float)
    - [Double](#double)
  - [논리 타입](#논리-타입)
    - [Boolean](#boolean)

# Primitive Type

## 정수 타입

### Byte

#### 값의 범위

- 8bit 중 첫번째 비트는 부호를 표현, 나머지 7bit로 수를 표현
- 첫번째 비트가 0이면 양의 정수, 1이면 음의 정수
- 첫번째 비트가 0일 때 표현할 수 있는 2 ^ 7 가지 수 중 다 0일 때는 0을 의미하므로 양의 정수는 2 ^ 7 - 1 개 표현할 수 있다
- 첫번째 비트가 1일 때 표현할 수 있는 음의 정수는 2 ^ 7 개이다
- 따라서 값의 범위는 -2^7 ~ 2^7-1

#### 기본 값

0

### 기타

Byte 타입에 범위를 넘는 것을 할당하면 -> 컴파일 에러
Byte 타입이 런타임에 범위를 넘으면 -> 오버플로우 처리됨

### Char

### Short

#### 값의 범위
- 2 byte로 표현되는 정수
- Byte와 동일한 이유로 값의 범위는 -2^15 ~ 2^15-1

#### 기본 값
0

### Int

#### 값의 범위
- 4 byte로 표현되는 정수
- -2^31 ~ 2^31-1

#### 기본 값
0

### Long

#### 값의 범위 
- 8 byte로 표현되는 정수
- -2^63 ~ 2^63-1

#### 기본 값
0

## 실수 타입
- 값을 부호, 지수, 가수로 나누어 저장

### Float

### Double

## 논리 타입

### Boolean
