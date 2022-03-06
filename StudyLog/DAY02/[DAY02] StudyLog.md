# 2일차

## 오늘 수행한 업무
1.	SourceTree 환경 설정 및 실행
2.	Git Flow 키워드 학습 및 SourceTree를 활용한 Git Flow 적용
3.	MVC, MVP, MVVM, UseCase 키워드 학습 

## 내일 수행할 업무
- 업무 정리
- 간단한 MVP 제작 
- UseCase 및 클리 아키텍쳐 키워드 학습

## 업무를 수행함 느낀점

- 기존 CLI를 통한 깃관리보다 Source Tree를 활용한 깃 관리가 훨씬 간편하고 쉽게 이용할 수 있었습니다. 처음 접하는 프로그램 임에도 가이드를 읽고 누구나 쉽게 사용할 수 있도록 되어있어 편리하게 사용할 수 있었습니다.
- 기존에 프로젝트를 진행할 당시에도 여러 브랜치를 만들어 PR하는 과정으로 프로젝트를 진행하였으나 깃 플로우와 같은 좋은 가이드라인이 있다는 것을 알 수 있었습니다. GitFlow라는 깃 브랜치 관리 기법은 실제 사용자를 대상으로 한 서비스에서 반드시 필요하다고 생각합니다.
- MVC, MVP, MVVM에 대한 이론을 다시한번 정리하고 실제 정형화된 MVP구조에 대해 간단하게 살펴보았습니다. 인턴 기간 이전 디자인패턴에 대해 공부할 당시에도 디자인 패턴 적용에 대한 막막함이 있었습니다. 당장 실제로 디자인 패턴을 적용하기 위해서는 클린아키텍쳐와 패키지를 어떻게 관리해야 하는지 등을 습득해야한다고 생각합니다.

### SourceTree

깃을 사용하는 방법에는 크게 커맨드 라인 인터페이스인 CLI과 별도의 버튼으로 기능을 실행하는 GUI가 있습니다. 소스트리는 Git관리를 쉽게 하기위한 GUI입니다.

우선 소스트리를 설치한 후, 간단하게 깃허브 레포지토리를 생성한 후 소스트리와 연동하는 작업을 진행하였습니다. 레포지토리 생성 후 우선 URL을 복사합니다.

<img width="452" alt="image" src="https://media.oss.navercorp.com/user/29230/files/254a7e80-6d90-11ec-983c-2a8469953a21">


 










복사한 URL을 소스트리의 새로만들기 -> URL에서 복제에 입력합니다.

<img width="230" alt="image" src="https://media.oss.navercorp.com/user/29230/files/33989a80-6d90-11ec-8983-0ff6d240dcbb">

 

목적지 경로와 이름을 설정한 후 클론을 클릭합니다.
 
 <img width="452" alt="image" src="https://media.oss.navercorp.com/user/29230/files/4317e380-6d90-11ec-8bab-bfedb9c4549d">

위와 같은 작업이 끝나면 폴더와 Github의 연동이 성공적으로 이루어졌습니다. 해당 폴더에 새로운 안드로이드 프로젝트를 생성해보았습니다.

<img width="452" alt="image" src="https://media.oss.navercorp.com/user/29230/files/5165ff80-6d90-11ec-8e20-7f0f4533b0b7">
 

위와 같이 지정된 이름의 소스트리 목록이 생성되고 목록을 클릭하면


<img width="452" alt="image" src="https://media.oss.navercorp.com/user/29230/files/5e82ee80-6d90-11ec-9faf-94025722c165">


 

위와 같은 인터페이스를 확인할 수 있습니다. 테스트를 위해 모든 항목을 커밋하고 메인 브랜치에 푸시하였으나 아래와 같은 오류가 발생하였습니다.


### 소스트리 사용중 오류사항 

 <img width="452" alt="image" src="https://media.oss.navercorp.com/user/29230/files/70fd2800-6d90-11ec-9a8f-f58b18463301">
 
깃허브에서는 2021년 8월 13일부로 Password Authentication을 공식적으로 제거하였습니다. 따라서 Personal Access Token을 발급받아 사용해야 합니다. 이 사실을 모르고 처음 비밀번호로 인증하였으나 인증이 실패했다는 메세지만 반복될 뿐 재입력 메세지가 보이지 않아 이를 해결하는 방법을 찾아보았습니다.
패스워드 초기화를 위해 /user/라이브러리/Application Support/SourceTree 위치의 계정파일을 삭제하였습니다. 삭제이후에 정상적으로 패스워드 요청을 다시 받을 수 있으며 일반 패스워드가 아닌 토큰을 사용해야 합니다. 토큰을 사용하기 위해서는
깃허브 Settings -> Developer settings -> Personal access tokens ->Generate new token 후 여러가지 권한에 대해 설정한 후 토큰을 패스워드에 입력하여 정상적으로 푸시할 수 있었습니다.

그 외에 브랜치를 생성하거나 브랜치를 병합하는등의 작업은 상단 메뉴에서 확인할 수 있었습니다.


## Git Flow

GitFlow는 소스코드를 관리할 때 브랜치 기능을 효과적으로 사용하는 전략입니다. 깃 플로우는 크게 5개의 브랜치를 사용하여 코드를 관리합니다.

1.	Master
정식으로 배포될 수 있는 안정적인 버전의 소스코드가 이곳에 관리됩니다. 따라서 마스터 브랜치에는 배포가 가능할만큼 안정성이 충분히 검증된 코드들이 저장되어야 합니다. 마스터 브랜치의 헤드에는 최신배포판의 소스코드 버전이 포함되어 있으며 지난 배포 버전의 소스코드를 추적하기 위해 태그가 추가되어 있습니다.
2.	Develop
각종 버그들을 수정하기 위한 코드와 새로운 기능을 추가하기 위한 코드, 성능을 개선하기 위한 코드들이 검증이 완료되고 PR요청을 거치게 되면 이 곳으로 병합됩니다.
개발자는 feature	브랜치에서 소스코드를 수정하고 develop 브랜치로 Pull Request를 요청하게 됩니다. 검토자가 기능들을 검토하고 최종적으로 develop 브랜치에 병합됩니다. 새로운 기능을 위한 feature	브랜치는 develop 브랜치의 HEAD에서 생성됩니다.
3.	Feature
새로운 기능 개발이나 버그 수정을 위한 코드 수정은 이 브랜치에서 이루어집니다. 개발자 혼자 작업을 할 수도 있으며 여러명의 개발자들이 공동으로 작업할 수도 있습니다.
4.	Release
Git으로 관리되는 소프트웨어는 정기적으로 성능 개선과 기능 추가, 버그 수정을 반영하면서 릴리즈됩니다. release 브랜치는 develop 브랜치를 기반으로 생성되며 다음 릴리즈에 포함되어야 하는 기능셋과 버그픽스들이 확정된 이후 생성됩니다. 릴리즈 브랜치가 생성된 다음 QA 테스트가 릴리즈 브랜치를 기준으로 진행됩니다. 이 과정에서 발견된 버그 수정 사항들이 릴리즈 브랜치와 develop 브랜치에 적용되며 주요한 기능들이 중간에 추가되지는 않습니다.

5.	Hotfix
기존의 작업은 PR, QA테스트등의 작업이 필요하지만 긴급한 문제가 발생할 경우에 테스트를 진행할 수 없는 상황이 발생합니다. Hotfix브랜치는 이런 상황에서 버그 수정을 반영하기 위해 생성되는 브랜치입니다. 
Hotfix브랜치는 마스터 브랜치를 기반으로 생성되며 긴급한 문제를 수정한 후 마스터브랜치에 병합되고 태그가 생성됩니다. 수정사항은 develop브랜치로도 병합되어 긴급 수정사항이 이후 릴리즈에도 반영되도록 합니다.


깃 플로우의 작업 진행상황을 요약하면 아래와 같습니다.

1.	우선 아무것도 없는 마스터브랜치에서 시작합니다.
2.	동일한 브랜치를 develop에도 생성합니다. 개발자가 개발을 시작하는 브랜치는 이 develop브랜치 입니다.
3.	개발 중 각자의 역할 분담이 필요한 경우 이 develop브랜치에서 feature브랜치를 생성하여 각자의 기능을 구현합니다.
4.	기능 개발이 완료된 feature브랜치는 검토를 거쳐 다시 develop 브랜치에 merge합니다.
5.	목표한 모든 기능을 구현하였으면 develop 브랜치를 realease브랜치로 만들고 QA를 하며 버그픽스와 보완할 점을 보완합니다.
6.	모든 작업이 완료되면 release브랜치를 master 브랜치와 develop 브랜치로 보냅니다. Master브랜치에서 버전추가를 위해 태그를 하나 생성하고 배포합니다.
7.	배포를 완료했는데 예측하지 못한 버그가 발생한 경우, hotfixes 브랜치를 만들어 긴급 수정 후 태그를 생성하고 바로 수정 후 배포합니다.

SourceTree에서 Git flow를 사용하는 방법은 아래와 같습니다.
1.	소스트리 창에서 커맨드+옵션+F 키를 눌러 깃 플로우를 열 수 있습니다.

 <img width="239" alt="image" src="https://media.oss.navercorp.com/user/29230/files/7eb2ad80-6d90-11ec-9c09-50f273bd42c6">

2.	기존에 master branch가 하나 필요하기 때문에 기존의 main 브랜치를 master로 변경한 후 깃플로우 기본값으로 확인을 실행합니다. 실행 후 develop브랜치가 생성됩니다.
3.	아래와 같이 커맨드+옵션+F를 실행하면 새 기능 시작을 할 수 있습니다. 원하는 이름을 넣고 실행할 수 있습니다.
 
 
<img width="204" alt="image" src="https://media.oss.navercorp.com/user/29230/files/883c1580-6d90-11ec-8a08-02194275a9cc">
 
4.	새 기능 시작으로 브랜치를 생성하면 feature/{설정한이름} 형태로 브랜치가 생성됩니다. 기능을 ui개선이라고 가정하고 생성해보았습니다.
5.	UI개선 작업을 진행했다고 가정하고, 다시 커맨드+옵션+F를 실행하면 현재 작업을 완료할 수 있습니다.

 <img width="283" alt="image" src="https://media.oss.navercorp.com/user/29230/files/8ffbba00-6d90-11ec-8fc9-5c35b599ae89">

Release Branch 생성

1.	마찬가지로 커맨드+옵션+F를 실행한 뒤 새 릴리즈 시작을 실행합니다.

<img width="452" alt="image" src="https://media.oss.navercorp.com/user/29230/files/99852200-6d90-11ec-8e35-392c5f734890">

 
 
2.	확인을 실행하면 릴리즈 브랜치가 생성되고 이 상태로 QA작업 및 버그 픽스 등의 작업을 하게됩니다. 작업 이후 다시 커맨드+옵션+F를 실행하여 작업을 종료합니다.

3.	아래와 같이 태그로 버전이 생기게 되고 마스터브랜치와 develop브랜치 역시 새롭게 생성됩니다.
    
<img width="395" alt="image" src="https://media.oss.navercorp.com/user/29230/files/a0ac3000-6d90-11ec-84a5-04a45ea9d1c0">


### MVC

MVC패턴은 모델, 뷰, 컨트롤러의 접근방식입니다. 각 요소의 특징은 아래와 같습니다.

-	모델 : 데이터를 가지며 앱 내에서 사용되는 데이터와 데이터를 처리하는 역할을 담당합니다.
-	뷰 : 사용자에게 보이는 UI를 담당하며 상호작용을 위해 사용자의 입력을 받는 컨트롤러와 이벤트를 주고받습니다. 또한 사용자가 어떤 입력을 했는지 뷰는 알 수 없습니다.
-	컨트롤러 : 사용자로부터 입력을 받고 이 입력을 모델의 데이터 변화에 따라 뷰를 선택하여 뷰에게 전달합니다.

하지만 안드로이드에서는 뷰와 컨트롤러의 역할을 모두 액티비티 또는 프래그먼트가 진행하므로 뷰와 컨트롤러의 구분이 어렵습니다. 이러한 MVC패턴은 구현하기 쉽고 단순하며 액티비티에 대부분의 기능을 구현하기만 하면 되므로 개발 기간이 짧아질 수 있습니다. 하지만 앱의 규모가 크면 클수록 코드의 가독성이 떨어지며 스파게티코드가 될 가능성이 높습니다. 또한 모델과 뷰 사이의 의존성으로 유지보수가 어려워집니다.


### MVP

MVP는 뷰와 모델의 의존성이 높은 MVC를 보완하기 위해 뷰, 모델 사이에 Presenter를 두어 뷰와 모델이 어떤 동작 시 Presenter를 통해서만 동작할 수 있도록 하여 뷰와 모델 사이의 의존성을 제거한 디자인패턴입니다. 또한 컨트롤러에서 받던 사용자의 입력을 뷰가 받도록 하였습니다.

우선 MVP패턴을 사용할 경우, 구현할 각 UI에 어떤 기능들이 필요한지 생각해야합니다. 예를들어 EditText로 입력을 받고, Submit 버튼을 누르면 입력한 값이 TextView	에 보여지는 화면을 만들고, 이 값을 로컬에 저장하는 작업을 한다면 
1.	이 값을 로컬에 저장하는 기능
2.	이 값을 ui에 보여주는 기능
으로 나눌 수 있습니다. 이처럼 View와 Presenter의 역할을 분명히 하기위해 	MVP패턴에서는 Contract를 작성하여 각 뷰와 프레젠터의 역할을 구분합니다.



 

### Android Architecture Sample – Todo app-MVP분석

우선 Android Architecture Sample의 Todo app-MVP브랜치를 다운받아 빌드 해보았습니다.

<img width="312" alt="image" src="https://media.oss.navercorp.com/user/29230/files/b7eb1d80-6d90-11ec-96a0-e75438a60de4"> 

처음 빌드 시 실행되는 TasksActivity입니다. Layout 리소스 파일을 살펴보니 material.io 라이브러리에서 사용할 수 있는 topAppbar와 같은 뷰를 사용하였습니다. 그외에 DrawerLayout, NavigationView등을 사용하여 ui를 구성하였습니다.
 
<img width="287" alt="image" src="https://media.oss.navercorp.com/user/29230/files/c76a6680-6d90-11ec-8e89-0aafda3f173a"> 
 
Tasks 패키지를 보면 Activity 내의 Fragment, 그리고 Contract를 볼 수 있습니다.

<img width="422" alt="image" src="https://media.oss.navercorp.com/user/29230/files/d18c6500-6d90-11ec-9e34-5c7289415850">


Contract	 내부에는 TaskFragment에서 담당할 View의 역할을 정의해놓은 인터페이스가 있으며

<img width="434" alt="image" src="https://media.oss.navercorp.com/user/29230/files/db15cd00-6d90-11ec-8ea2-b181a6ae15ed">

 
TaskPresenter에서 담당할 Presenter 인터페이스가 존재합니다. 

TaskFragment는 TaskContract.View를 상속받으며 TaskPresenter는 TaskContract.Presenter를 상속받아 각 기능을 구현해야 합니다.






### MVVM

MVVM은 뷰, 뷰모델(ViewModel), 모델로 이루어진 디자인패턴입니다. 

 <img width="452" alt="image" src="https://media.oss.navercorp.com/user/29230/files/ee289d00-6d90-11ec-9e31-70eca0bef627">

안드로이드에서는 안드로이드에서 제공하는 AAC를 활용하여 MVVM에 최적화된 여러 컴포넌트를 제공합니다. 각자의 역할을 살펴보았습니다.
-	View (UI Controller) : UI를 담당하는 액티비티나 프래그먼트입니다. 뷰는 뷰모델을 관찰하는 형태이며, 뷰모델에서 데이터의 변화가 존재하면 이를 감지합니다.
-	ViewModel : 뷰모델은 UI를위한 데이터를 가지고 있습니다. 안드로이드 AAC에서는 이를 위한 LiveData를 제공하고 있으며 AAC에서 제공하는 뷰모델은 액티비티의 생명주기를 따르지 않고 액티비티와 분리되어 있어 화면 회전 등의 액티비티가 파괴되는 경우에도 데이터를 여전히 지니고 있습니다.
-	LiveData : 라이브데이터는 관찰 가능한 데이터 홀더 클래스입니다. 뷰에서 뷰모델의 라이브데이터를 관찰하게 되며 데이터가 변경될 때 내부적으로 자동으로 알려주게 됩니다. 또한 라이브데이터는 액티비티나 프래그먼트의 생명주기를 알고 있기 때문에 액티비티가 활성화상태일때만 UI변경 등의 기능을 동작하게 되고 Destroy상태에서는 동작하지 않기 때문에 메모리 릭을 방지할 수 있습니다.
-	Repository : 뷰모델은 레포지토리를 관찰하여 API를 호출합니다. 레포지토리는 앱에 필요한 데이터(내장 Room데이터베이스, Retroift 등)을 가져옵니다. 뷰모델은 서버나 내장DB에 직접 접근하는것이 아닌 레포지토리를 통해 접근합니다.



### UseCase

UseCase란 제작중인 서비스를 사용하는 사용자가 이 서비스를 통해 실행하고자 하는 것을 말합니다. 음식점에서의 손님은 음식 주문, 가격 지불, 식사 등 사용자가 시스템에 요청할 수 있는 사항이 Use Case입니다.

UseCase	는 클린아키텍쳐 중 Domain Layer	에 속해있고 이 레이어는 안드로이드에 의존하지 않는 순수한 자바 또는 코틀린 모듈입니다.

유스케이스에 대한 정보를 깊게 알기 위해서는 클린아키텍쳐에 대한 공부가 우선 시행되어야 할 것 같습니다.


# DAY2++

# 2일차 개인적으로 진행한 간단한 MVP 예제

- MVP 패턴을 이용하여 정말 간단한 예제를 만들어보고자 합니다. 빠른 습득과 이해를 위해 개인적으로 진행하였습니다.

## 기능
- 정말 단순하게 EditText에 텍스트르 입력하고, 버튼을클릭하면 텍스트의 내용을 아래 텍스트뷰에 보여주고, 입력한 값을 SharedPreference에 저장합니다. 
만약 추후에 앱을 다시 시작했을때 저장된 값이 있다면 저장된 값을 불러오는 기능을 구현하려고 합니다.

<img width="291" alt="image" src="https://user-images.githubusercontent.com/58676668/148058631-3d904285-b24a-4b93-8159-99425ab9c7c2.png">


- 추가적으로 소스트리를 이용하여 커밋, 푸시하는 것 까지 진행하려고 합니다. 현재 main의 기능 구현을 위해 master, develop, feature/main 이 생성되어 있으며 현재 main 작업중이므로 main에 작업합니다.

- 기능을 구현하기 전 어떤 기능들이 필요한지 Contract에 작성합니다.

1. EditText의 값을 SharedPreference에 저장하느 기능
2. EditText의 값을 Textview에 보여주는 기능
3. SharedPreference의 값을 확인하여 값이 존재한다면 Textview에 보여주는 기능
- 패키지는 간단하게 main, data 로만 구분되어 있습니다. 위 기능에따라 아래와같이 Contract 를 구성합니다.

```
interface MainContract {
    interface View{
        //텍스트뷰에 데이터를 보여준다
        fun showInfo(info : String)
    }
    interface Presenter {
        //onCreate시 SharedPreference를 확인하여
        //저장된 데이터가 있는지 Model에서 확인하고
        //결과에 따라 저장된 데이터가 존재하면 View에 어떤 내용을 보여줄지 지시함
        fun initInfo()
        
        //TextView에 info 데이터를 보여주라고 View에게 지시한다
        fun setInfo(info : String)
        
        //EditText에 입력된 데이터를 저장하라고 Model에게 지시한다.
        fun saveInfo(info : String)
    }
}
```

이에 맞추어 Presenter는 View와 Model 사이의 중재자 역할을 해야하므로 Presenter의 생성자에 View와 Model(repository)을 넣어주며 MainContract.Presenter를 상속받습니다.

```
class MainPresenter(
    val view : MainContract.View,
    val repository : InfoRepository
) : MainContract.Presenter{
    override fun initInfo() {
        TODO("Not yet implemented")
    }

    override fun setInfo(info: String) {
        TODO("Not yet implemented")
    }

    override fun saveInfo(info: String) {
        TODO("Not yet implemented")
    }
}
```

현재 InfoRepository가 구현되어 있지 않으므로 이를 먼저 구현합니다.
Presenter는 직접 데이터에 접근하지 않고 Repository를 이용해 접근하게 됩니다. 이를 위해서 Repository가 구현해야하는 함수를 정의한 InfoDataSource를 정의합니다.

```
interface InfoDataSource {
    interface LoadInfoCallback{
        fun onInfoLoaded(info : String)
        fun onDataIsNull()
    }
    fun getInfo(callback : LoadInfoCallback)
    fun saveInfo(info : String)
}
```

데이터를 불러올 때 성공, 실패여부를 구분하기 위해 LoadInfoCallback리스너를 추가하였고 데이터를 불러올 getInfo와 saveInfo를 정의합니다
이를 Repository와 local/remote Data Source에 상속하여 사용합니다. 현재 remote data source는 사용하지 않으므로 local만 작성합니다.

```

class InfoLocalDataSource(context : Context) : InfoDataSource {
    private val sharedPreferences = context.getSharedPreferences("info",Context.MODE_PRIVATE)
    private val editor = sharedPreferences.edit()
    override fun getInfo(callback: InfoDataSource.LoadInfoCallback) {
        var info = sharedPreferences.getString("info",null)
        if(info!=null){
            callback.onInfoLoaded(info)
        }
        else{
            callback.onDataIsNull()
        }
    }
    override fun saveInfo(info: String) {
        editor.putString("info",info)
        editor.commit()
    }
}
```

다음으로 Repository를 작성합니다. 

```
class InfoRepository(context : Context) : InfoDataSource {
    private val infoLocalDataSource = InfoLocalDataSource(context)
    override fun getInfo(callback: InfoDataSource.LoadInfoCallback) {
        infoLocalDataSource.getInfo(callback)
    }

    override fun saveInfo(info: String) {
        infoLocalDataSource.saveInfo(info)
    }
}
```

Repository구성을 완료하였으니 다시 Presenter로 돌아와 각 기능들을 구현합니다.

```

class MainPresenter(
    val view : MainContract.View,
    val repository : InfoRepository
) : MainContract.Presenter{
    override fun initInfo() {
        repository.getInfo(object: InfoDataSource.LoadInfoCallback{
            override fun onInfoLoaded(info: String) {
                Log.d("TAG", "onInfoLoaded: called")
                view.showInfo(info)
            }
            override fun onDataIsNull() {
                Log.d("TAG", "onDataIsNull: called ")
            }

        })
    }
    override fun setInfo(info: String) {
        view.showInfo(info)
    }

    override fun saveInfo(info: String) {
        repository.saveInfo(info)
    }
}
```
마지막으로 View역할을 하는 Activity를 구현합니다.
```
class MainActivity : AppCompatActivity(), MainContract.View{
    private lateinit var mBinding : ActivityMainBinding

    private lateinit var presenter: MainPresenter
    private lateinit var repository: InfoRepository
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        mBinding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(mBinding.root)

        repository = InfoRepository(this)
        presenter = MainPresenter(this,repository)

        //처음 저장된 값이 있다면 불러온다
        presenter.initInfo()

        clickBtnListener()
    }

    override fun showInfo(info: String) {
        mBinding.text.text = info
    }
    fun clickBtnListener(){
        mBinding.btn.setOnClickListener {
            var info = mBinding.edittext.text.toString()
            presenter.setInfo(info)
            presenter.saveInfo(info)
        }
    }
}
```

- 최종적으로 소스트리에 git Flow를 이용하여 release1.0으로 저장하고, master브랜칭 저장되었습니다.



