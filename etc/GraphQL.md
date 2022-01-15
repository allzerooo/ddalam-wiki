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
    - [Scalar types](#scalar-types)
    - [Enumeration types](#enumeration-types)
    - [Lists and Non-null](#lists-and-non-null)
    - [Interfaces](#interfaces)
    - [Union types](#union-types)
    - [Input Types](#input-types)
  - [Validation](#validation)
  - [Execution](#execution)
    - [Root fields & resolvers](#root-fields--resolvers)
    - [Asynchronous resolvers](#asynchronous-resolvers)
    - [Trival resolvers](#trival-resolvers)
    - [Scalar coercion](#scalar-coercion)
    - [List resolvers](#list-resolvers)
  - [resolver](#resolver)
  - [introspection](#introspection)
  - [Java Library](#java-library)
    - [GraphQL Spring Boot](#graphql-spring-boot)

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
```graphql
type Starship {
  id: ID!
  name: String!
  length(unit: LengthUnit = METER): Float
}
```

### The Query and Mutation types
- 스키마의 대부분의 유형은 Object 타입이지만, 모든 GraphQL 서비스에는 query 타입이 있으며 mutation 타입은 있을수도 있고 없을수도 있다
- 모든 GraphQL 쿼리의 진입점을 정의한다
```graphql
type Query {
  hero(episode: Episode): Character
  droid(id: ID!): Droid
}
```
```graphql
query {
  hero {
    name
  }
  droid(id: "2000") {
    name
  }
}
```

### Scalar types
- 스칼라 타입을 정의할수도 있다. 정의한 타입을 직렬화, 역직렬화, 검증하는 방법은 구현에 달려 있다. 예를 들어, Date 타입을 항상 정수 타임스탬프로 직렬화 되도록 지정할 수 있다.

### Enumeration types

### Lists and Non-null
- ex1) List 자체는 null일 수 있지만 null 멤버는 포함할 수 없다
    ```graphql
    myField: [String!]
    ```
    ```graphql
    myField: null // valid
    myField: [] // valid
    myField: ['a', 'b'] // valid
    myField: ['a', null, 'b'] // error
    ```
- ex2) List 자체는 null일 수 없지만 null 멤버는 포함할 수 있다
    ```graphql
    myField: [String]!
    ```
    ```graphql
    myField: null // error
    myField: [] // valid
    myField: ['a', 'b'] // valid
    myField: ['a', null, 'b'] // valid
    ```

### Interfaces

### Union types
- 타입들의 공통 필드를 지정하지 못한다
- 스키마에서 SearchResult 타입을 반환할 때마다 Human, Droid, Starship을 얻을 수 있다
  ```graphql
  union SearchResult = Human | Droid | Starship
  ```
- union 타입의 구성은 구체적인 object 타입이어야 한다

### Input Types
- 복잡한 객체를 전달
- `type` 대신 `input` 키워드를 사용한다
- 필드는 인수를 가질 수 없다

## Validation

## Execution
- type system 없이 쿼리를 실행할 수 없다
- 쿼리의 각 필드는 function 또는 method로 생각할 수 있다
- 각 필드는 GraphQL 서버 개발자가 제공하는 resolver라 불리는 함수로 뒷받침된다
- 필드가 실행되면 해당 리졸버가 호출되어 값을 생성한다
- 쿼리는 항상 스칼라 값에서 끝나고, 스칼라 값에 도달할 때까지 계속된다

### Root fields & resolvers
- 모든 GraphQL 서버의 최상위 수준에는 GraphQL API에 대한 모든 가능한 진입점을 나타내는 타입이 있으면 이를 root 타입 또는 query 타입이라고 한다
- JavaScript 예제
  ```graphql
  Query: {
    human(obj, args, context, info) {
      return context.db.loadHumanByID(args.id).then(
        userData => new Human(userData)
      )
    }
  }
  ```
  - human이라는 필드를 제공하는 query type
  - resolver 함수는 데이터베이스에 액세스해서 Human 객체를 구성하고, 반환한다
- resolver 함수는 4개의 인자를 받는다
  - obj : root query 타입 필드에서는 자주 사용되지 않는 이전 객체
  - args : 쿼리 필드에 제공된 인수
  - context : 모든 리졸버에 제공되며 현재 로그인한 사용자 또는 데이터베이스 액세스와 같은 중요한 컨텍스트 정보를 보유하는 값
  - info : 현재 쿼리와 관련된 필드별 정보 및 스키마 세부 정보를 담고 있는 값

### Asynchronous resolvers
- 데이터베이스에서 로드하는 것은 비동기 작업이므로 리졸버는 Promise, Futures, Deferred와 같은 개념을 사용해 객체를 구성, 반환한다
- 리졸버 함수는 Promise, Futures, Deferred 등을 인식해야 하지만, query는 그렇지 않다
- GraphQL은 Promise, Futures와 같은 작업이 완료될 때까지 기다리며 최적의 동시성을 사용하여 완료한다

### Trival resolvers
```graphql
Human: {
  name(obj, args, context, info) {
    return obj.name
  }
}
```
- GraphQL 서버는 다음에 수행할 작업을 결정하는데 사용되는 type system으로 구동된다
- name 리졸버가 호출되고 obj 인수는 이전 필드에서 반환된 새로운 Human 객체이다
- 많은 GraphQL 라이브러리에서는 간단한 리졸버를 생략할 수 있으며 필드에 리졸버가 제공되지 않으면 동일한 이름의 속성을 읽고 반환해야 한다고 가정한다

### Scalar coercion
- name 필드가 해결되는 동안 appearsIn, starships 필드가 동시에 해결될 수 있다

### List resolvers


## resolver
- 데이터를 가져오는 구체적인 과정을 담당
- resolver를 통해 데이터 source의 종류에 상관없이 데이터를 가져올 수 있다(데이터베이스, 일반 파일, http 프로토콜 활용 등)
- 쿼리 필드에 존재하는 각각의 함수

## introspection
- 현재 서버에 정의된 스키마의 실시간 정보를 공유할 수 있게 한다
- 클라이언트 사이드에서는 API 연동규격서를 요청할 필요없이 스키마 정보로 쿼리문을 작성
- introspection용 쿼리가 따로 존재

<br/>

## Java Library

### GraphQL Spring Boot
- GraphQL Java Tools로 스키마 기반 API 사용

<br/>

---

<br/>

출처 및 참고
- [GraphQL](https://graphql.org/)
- [GraphQL 개념잡기](https://tech.kakao.com/2019/08/01/graphql-basic/)