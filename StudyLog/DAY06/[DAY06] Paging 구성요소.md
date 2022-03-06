# Paging

페이징은 데이터를 작게 나누어 로드하기위해 사용되는 라이브러리 입니다.

데이터를 나누어 로드하면 시스템 자원과 네트워크 대역폭의 사용을 줄일 수 있습니다.

간단한 과정은 DataLayer내의 DataSource로부터 얻은 데이터를 PageList형태로 관리하고 PagedListAdapter를 통해 최종적으로 RecyclerView에 표현됩니다.

페이징 컴포넌트의 의존성은 Google Maven저장소에서 제공되며 앱 모듈의 build.gradle에 의존성을 추가합니다.

```
implementation "androidx.paging:paging-runtime:$paging_version"
```

페이징 라이브러리는 아래와 같은 데이터 구조를 갖는 모델을 대상으로 설계되었습니다.

- 네트워크로부터 데이터를 가져오기

- 로컬 데이터베이스로부터 데이터를 가져오기

- 네트워크에서 얻은 데이터를 로컬 데이터베이스에 캐싱하여 사용하기

페이징 라이브러리는 데이터를 작게 나누어 로드하며 얻은 데이터의 마지막 지점에 도달하면 로컬이나 서버에서 더 많은 데이터를 요청하게 됩니다.

## 페이징 컴포넌트의 구성요소

1. DataSource

데이터를 작게 나누어 불러온 다음 PagedList로 로드하는 기본 클래스입니다. 더 많은 데이터를 로드할수록 PagedList가 커지며 로드된 데이터를 갱신할 수 없습니다.

데이터의 재정렬, 삽입, 삭제, 갱신 같은 변경사항이 발생하면 새로운 PagedList와 DataSource를 생성해야 합니다.

DataSource가 데이터를 이어서 불러들일 수 없는 상황이면 invalidate()를 호출해야 합니다. 그러면 새로운 PagedList와 DataSource쌍이 새로운 상태로부터 데이터를 로드합니다.

변경되지 않는 데이터를 페이징한다면 단일 DataSource를 생성할 수도 있습니다.

업데이트를 제공하는 소스의 데이터를 페이징하려면 DataSource.Factory를 만들 수 있습니다. 

팩토리에서 생성된 각 DataSource는 데이터 세트에 대한 변경 사항이 발생하면 현재 스냅숏을 무효화 시킬 수 있습니다.

예를들어, 데이터베이스에서 쿼리를 페이징하고 쿼리 중인 테이블에 추가 또는 제거와 같이 데이터 세트가 변경되는 상황에서 invalidate()를 호출할 수 있습니다.

당겨서 새로고침과 같은 작업으로 모든 데이터를 다시 로드하는 경우에도 invalidate()를 호출할 수 있습니다.

2. DataSource.Factory

DataSource인스턴스를 만들어 제공하는 팩토리클래스 입니다. 

create()메소드에서 DataSource를 반환하며 데이터 세트가 무효화될 때마다 create()메소드에서 새로운 DataSource객체를 만들 수 있습니다.

3. PagedList

DataSource 에서 변경 불가능한 콘텐츠의 페이지를 지연 로드 목록으로 표시합니다.

PagedListsms DataSource에서 페이징된 데이터들을 관리하는 리스트입니다. get(int)로 아이템에 접근할 수 있으며 loadAround(int)를 사용하여 추가 로드를 트리거할 수 있습니다.

4. LivePagedListBuilder

DataSource.Factory와 PagedList.Config를 생성자 매개변수로 가지며 LiveData<PagedList>를 생성하는 빌더 클래스입니다.

필수 매개 변수는 생성자에 있고, 선택적으로 초기 로드 키 또는 BoundaryCallback등을 구서 및 활성화하여 빌드할 수 있습니다.

5. PagedListAdapter

PagedList에서 페이징된 데이터를 표시하는 RecyclerView.Adapter기반의 클래스입니다.

아이템 카운트를 계산하고, PagedList의 상태 변화를 체크하는 일반적인 동작에 대한 구현을 담당하는 AsyncPagedListDiffer객체를 내부에 가지고 있고

새오누 목록 사용이 가능할때 submitList(PagedList) 메소드를 호출할 수 있습니다.

PagedListAdapter를 구현해야 할 때 주의해야할 점은 새로운 PagedList에 대한 백그라운드 계산을 위해 DiffUtil.ItemCallbackd을 필수적으로 구현해야 한다는 점입니다.

