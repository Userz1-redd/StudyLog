# MVP Clean 비교

- 구글 아키텍쳐 샘플에서 MVP와 MVP Clean을 비교해보았습니다. 우선 직관적으로 패키지를 보았을 때, UseCase, UseCaseHandler와 UseCaseScheduler, UseCaseThreadPoolScheduler가 존재하였습니다.

- MVP와의 비교를 위해 비교대상을 TaskDetail로 정하고 TaskDetailPresenter를 살펴보았습니다.

- TaskDetailPresenter에는 기존에 없던 UseCaseHandler가 선언되어 있었습니다. 

- UseCaseHandler를 보니 당장은 이해하기 힘든 여러 코드들이 나열되어 있었습니다. 

- 문서를 읽어보니 MVP모델의 중간에 도메인 레이어를 추가하여 사용한 것으로 설명되어 있습니다. Presenter가 직접 데이터에 접근하지 않고 UseCaseHandler를 사용하여 데이터에 접근하는 모습입니다. 
- 간단히 도식화하면 아래와 같이 구현되어 있다고 할 수 있습니다.
기존 MVP
TaskDetailPresenter <-> TaskRepository 상태였지만
```
MVP-Clean
                                           <-> UseCase1
TashDetailPresenter <-> UseCaseHandler     <-> UseCase2  <-> TaskRepository 
```

이러한 형태로 구성되어 있습니다.
- 각 UseCase는 단일 기능을 할 수 있으며 이러한 객체가 여러개 존재합니다. 이를 관리하는 UseCaseHandler가 존재하며 
- TaskRepository와 TaskDetailPresenter가 간접적인 관계에 있으므로 기능 하나를 수정할 때 UseCase하나를 수정하면 되므로 유지보수에 용이합니다. 
작업중
                                           
