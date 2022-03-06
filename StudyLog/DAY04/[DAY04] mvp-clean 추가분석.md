# addedittask
우선 addedittask패키지부터 살펴보려고 합니다. 우선 기존 mvp와 다르게 domain이라는 패키지, 그리고 내부의 usecase라는 패키지가 존재했습니다.

<img width="224" alt="image" src="https://media.oss.navercorp.com/user/29230/files/f3403600-6ecf-11ec-8f27-a4413ee48a8b">

Usecase내부에는 DeleteTask, GetTask, SaveTask로 작업의 역할에 따라 케이스를 분리한 것을 확인할 수 있었습니다.

이처럼 UseCase를 분리하여 도메인레이어를 추가로 두는것은 필요한 작업을 명확하게 정의하는 것 뿐만 아니라 UseCase를 재사용하여 사용성을 높일 수 있습니다.
아래의 CompleteTask는 TaskDetailPresenter와 TaskPresenter에서 모두 사용됩니다.
```
  public void completeTask() {
        if (Strings.isNullOrEmpty(mTaskId)) {
            mTaskDetailView.showMissingTask();
            return;
        }

        mUseCaseHandler.execute(mCompleteTask, new CompleteTask.RequestValues(mTaskId),
                new UseCase.UseCaseCallback<CompleteTask.ResponseValue>() {
                    @Override
                    public void onSuccess(CompleteTask.ResponseValue response) {
                        mTaskDetailView.showTaskMarkedComplete();
                    }

                    @Override
                    public void onError() {
                        // Show error, log, etc.
                    }
                });
    }
 ```
 
 또한 UseCase를 이용한 접근방법은 도메인코드를 UnitTest하는데 좀더 수월하다고 할 수 있습니다.
