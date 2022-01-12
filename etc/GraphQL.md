- [GraphQL](#graphql)
  - [Operation Type](#operation-type)
  - [Query, Mutation](#query-mutation)
    - [Fields](#fields)
    - [Arguments](#arguments)
    - [Aliases](#aliases)
    - [Fragments](#fragments)
      - [Using variables inside fragments](#using-variables-inside-fragments)
    - [Operation name](#operation-name)
    - [Variables](#variables)
      - [Variable definitaions](#variable-definitaions)
      - [Default variables](#default-variables)
    - [Directives](#directives)
    - [Mutations](#mutations)
      - [Multiple fields in mutations](#multiple-fields-in-mutations)
    - [Inline Fragments](#inline-fragments)
      - [Meta fields](#meta-fields)
  - [Schema, Type](#schema-type)
    - [Type system](#type-system)
    - [Type language](#type-language)
    - [Object types and fields](#object-types-and-fields)
    - [Arguments](#arguments-1)
    - [The Query and Mutation types](#the-query-and-mutation-types)
  - [resolver](#resolver)
  - [introspection](#introspection)

<br/>

# GraphQL

- 클라언트가 서버로 부터 효율적으로 데이터를 가져오는 것이 목적
- 쿼리를 주로 클라이언트 시스템에서 작성하고 호출
- 특정 데이터베이스나 플랫폼에 종속적이지 않음
- 단 하나의 Endpoint가 존재
- 불러오는 데이터의 종류를 쿼리 조합을 통해서 결정
- 데이터를 가져오는 구체적인 과정(resolver)을 직접 구현해야 한다

## Operation Type
`query`, `mutation`, `subscription`

## Query, Mutation
query : 데이터를 읽는(R) 개념, mutation : 데이터를 수정(CUD)하는 개념

- `query` 키워드와 쿼리 이름을 모두 생략하는 축약형 구문 가능

### Fields
- 필드, 객체의 필드에 대한 질의 가능
- 관련 객체 및 필드를 순회할 수 있으므로 하나의 요청으로 관련된 많은 데이터를 가져올 수 있다
  - REST 아키텍처에서는 여러 번 요청을 해야된다

### Arguments
- 필드에 인수를 전달할 수 있다

### Aliases

### Fragments
- 재사용 가능한 단위

#### Using variables inside fragments
- fragment에서 query 또는 mutation에 정의된 변수에 접근할 수 있다

### Operation name
```graphql
query HeroNameAndFriends {
    ...
}
```
`HeroNameAndFriends`이 operation name

- 일반 쿼리와 오퍼레이션 네임 쿼리
  - 오퍼레이션 네임 쿼리
    - 쿼리용 함수
    - 데이터베이스에서 procedure 개념과 유사
    - 이 개념 덕분에 한번의 네트워크 왕복으로 원하는 모든 데이터를 가져올 수 있다
    - 클라이언트 프로그래머가 작성하고 관리

### Variables
쿼리의 동적 값들을 사전으로 전달하는 방법을 사용하는데, 이러한 값들을 의미한다

#### Variable definitaions
```graphql
($episode: Episode)
```
- `$episode` : 변수, `Episode` : 변수의 타입
- 변수는 scalar, enum, input object 타입이어야 한다
- 변수 정의는 선택 사항이거나 필수 사항일 수 있다(`!`가 있으면 필수 사항)
  
#### Default variables
```graphql
($episode: Episode = JEDI)
```

### Directives
- 변수를 사용하여 쿼리의 구조와 모양을 동적으로 변경하는 방법
- field, fragment에 사용될 수 있다
- `@include(if: Boolean)` : 인수가 true인 경우에만 결과에 이 필드를 포함
- `@skip(if: Boolean)` : 인수가 true인 경우 이 필드를 건너뜀

### Mutations
데이터 수정을 위한 Operation Type

#### Multiple fields in mutations
query와 마찬가지로 여러 필드를 포함한 요청을 할 수 있는데, query는 병렬로 실행되는 반면 mutation은 차례로 실행된다. 따라서 N번의 mutation 요청을 보내면 먼저 실행된 요청이 완료된 후 다음 요청을 실행하는 것이 보장되기 때문에 race condition이 발생하지 않도록 해준다

### Inline Fragments
interface 또는 union 타입을 반환하는 필드를 조회하는 경우, inline fragement를 사용해서 구체적인 데이터 타입을 요청해야 한다

#### Meta fields
어떤 타입을 반환할지 모르는 상황이 있을 때 클라이언트에서 해당 데이터를 처리하는 방법을 결정하는 방법으로 쿼리의 어느 지점에서든 `__typename` 메타 필드를 요청하여 해당 지점의 객체 타입 이름을 가져올 수 있다

## Schema, Type

### Type system

### Type language
특정 프로그래밍 언어에 의존하지 않는 "GraphQL schema language"를 사용

### Object types and fields
GraphQL 스키마의 가장 기본적인 구성 요소는 가져올 수 있는 객체의 type과 객체가 포함하는 필드들이다
```graphql
type Character {
    name: String!
    appearsIn: [Episode!]!
}
```
- `Character` : GraphQL Object Type. 일부 필드를 가지는 type이며, 스키마에 있는 대부분의 타입은 Object type이다
- `name`, `appearsIn` : `Character` 타입의 필드. `Character` 타입에 대한 쿼리 연산으로 나타날 수 있는 필드
- `String` : 내장된 Scalar 타입 중 하나
- `String!` : 해당 필드가 non-nullable 임을 의미
- `[Episode!]!` : `Episode` 객채의 배열을 의미. 또한 null을 허용하지 않기 때문에 `appearsIn` 필드를 쿼리할 때 항상 0개 이상의 항목을 포함하는 배열이 기대된다

### Arguments
- Object 타입의 모든 필드는 0개 이상의 인수를 가질 수 있다
- 모든 인수는 이름을 가지며, 인수는 이름으로 전달된다
- 인수는 선택 또는 필수 사항일 수 있으며, 인수가 선택 사항인 경우에는 기본 값을 정의할 수 있다

### The Query and Mutation types
- 스키마의 대부분의 유형은 Object 타입이지만, 모든 GraphQL 서비스에는 query 타입이 있으며 mutation 타입은 있을 수도 있고 없을 수도 있다
- 쿼리의 진입점을 정의한다


## resolver
- 데이터를 가져오는 구체적인 과정을 담당
- resolver를 통해 데이터 source의 종류에 상관없이 데이터를 가져올 수 있다(데이터베이스, 일반 파일, http 프로토콜 활용 등)
- 쿼리 필드에 존재하는 각각의 함수

## introspection
- 현재 서버에 정의된 스키마의 실시간 정보를 공유할 수 있게 한다
- 클라이언트 사이드에서는 API 연동규격서를 요청할 필요없이 스키마 정보로 쿼리문을 작성
- introspection용 쿼리가 따로 존재

<br/>

---

<br/>

출처 및 참고
- [GraphQL](https://graphql.org/)
- [GraphQL 개념잡기](https://tech.kakao.com/2019/08/01/graphql-basic/)