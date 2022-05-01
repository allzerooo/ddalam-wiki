# JIT(Just In Time) Compiler

Java는 애플리케이션을 실행할 때 [컴파일 방식과 인터프리터 방식](https://github.com/ddalam/ddalam-wiki/blob/master/CS/etc.md#%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%96%B8%EC%96%B4%EB%A5%BC-%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95)을 같이 사용한다. .class 파일로 컴파일한 후(Bytecode로)에 이를 기계어로 인터프리트한다.

JIT 컴파일러는 자주 실행되는 코드를 기계어로 번역해서 캐싱을 해놓고 재사용할 수 있도록 해준다. 따라서 반복되는 코드를 인터프리터가 매번 기계어로 번역할 필요가 없어지기 때문에 실행 속도를 높일 수 있다.