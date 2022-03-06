# gitignore

gitignore는 깃에 파일을 업로드 할 때, 제외해야할 파일들의 파일명 또는 확장자가 담긴 파일입니다.

gitignore에 포함되어있는 파일명, 확장자명은 레포지토리에 pushg할 때 예외사항으로 등록되어 업데이트되지 않습니다.

## 사용법

.gitignore파일을 만들어 예외로 설정할 파일 목록을 작성하면 됩니다.

또한 gitignore파일은 항상 최상위 디렉토리에 존재해야 합니다.

## 문법

1. 특정 파일 제외하기

file명

2. 현재 경로에 있는 특정 파일만 제외하기

/file명

3. 특정 폴더 안의 파일 모두 제외하기

folder명/

4. 특정 경로의 특정 파일 제외하기

folder/file

5. 특정 경로 아래의 모든 file 제외하기

folder/**/files

6. 특정 확장자 파일 제외

*.txt

7. 예외만들기

!filename.txt


## SourceTree 에서 사용하는 방법

프로젝트의 설정 아이콘을 클릭합니다.

![image](https://media.oss.navercorp.com/user/29230/files/a078af80-7157-11ec-9468-94d6fa4a7b2d)

고급 -> 편집에서 gitignore파일을 설정할 수 있습니다.

<img width="540" alt="image" src="https://media.oss.navercorp.com/user/29230/files/d7e75c00-7157-11ec-82a9-5f5a32114c69">


이미 커밋, 푸시된 파일에는 적용되지 않고 앞으로 전송할 파일에만 적용됩니다.

