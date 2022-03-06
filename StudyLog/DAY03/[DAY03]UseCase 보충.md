# UseCase
-	UseCase를 왜 사용하는지에 대한 궁금증으로 정리한 내용입니다. 클린아키텍처에서 설명했듯이, 안드로이드는 Presentation Layer, Domain Layer, Data Layer로 구성합니다.

![](https://media.vlpt.us/images/cchloe2311/post/eb0f9af1-a862-4e5a-98d8-8ae965a1f2d7/image.png)

-	위 그림에서 Application Layer는 Domain Layer로의 데이터 흐름을 이어 소프트웨어가 해야 할 일을 정의하는 계층입니다.
-	일반적으로 Domain 패키지와 함께 비즈니스 로직을 담기 때문에 함께 분류한다고 합니다. 
-	도메인 비즈니스 로직의 경우 data뿐만 아니라 process도 포함하는 model을 이용합니다.
-	애플리케이션 비즈니스 로직은 Domain Model을 검색하고 저장하기위해 UseCase를 이용합니다.
## UseCase 사용 이유
-	관심사를 분리하지 않으면 모든것을 다 알고 모든 것에 의존성을 갖는 객체가 생성됩니다.
-	만약 UseCase를 사용하지 않는 ViewModel과 UseCase를 사용하는 ViewModel이 있다면

-	UseCase를 사용하지 않는 뷰모델은 Repository를 포함해 모든것에 의존하게 됩니다.

-	UseCase를 사용하는 뷰모델은 의존도가 UseCase로 이동하여 data logic이 이동해 더 가벼운 뷰모델을 갖게 됩니다.

-	만약 Repository에 다른 Repository의 결과가 파라미터로 필요하다면, Usecase를 사용하지 않는 경우 ViewModel에 이를 추가해야 합니다. 하지만 UseCase를 사용한다면 뷰모델에 전혀 영향을 주지 않고 받아들일 수 있습니다.

-	만약 비슷한 뷰모델이 존재한다면 UseCase를 재사용하면 된다는 장점이 있습니다.



