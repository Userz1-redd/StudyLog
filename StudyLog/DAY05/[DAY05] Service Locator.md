# Service Locator

Koin은 코틀린의 다양한 언어적 장점을 활용한 라이브러리 입니다. Koin은 DI를 제공하지만 DI 라이브러리 라기 보다는 ServiceLocator 패턴의 구현체입니다.

Service Locator는 중앙 등록자입니다. Service Locator를 통해 요청이 들어올 경우, 특정 인스턴스를 반환합니다.

Service Locator는 모든 클래스가 서비스 로케이터에 종속되어 있습니다. 기존의 Dagger는 일부 핵심 클래스에 종속성을 주입하는 것과는 다른 개념입니다.

또한 Dagger와 달리 Service Locator패턴은 인젝터를 직접 호출해야 합니다. Koin의 경우 by inject를 통해 직접 호출합니다.

