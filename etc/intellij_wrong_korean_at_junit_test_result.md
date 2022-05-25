# IntelliJ 테스트 실행 시 한글이 깨져 보이는 경우

<img width="80%" src="https://user-images.githubusercontent.com/49870384/165988913-7151e035-c7ed-4cc7-bb53-fe1be2d9b16b.PNG">

IntelliJ 에서 테스트 실행 시 위의 사진처럼 테스트 결과의 한글이 깨져 보이는 경우가 있다.
이럴 경우,

1. Help - Edit Custom VM Options... 클릭
2. **-Dfile.encoding=UTF-8** 라인 추가
3. 저장 후 닫기
4. File - Invalidate Caches... 클릭
5. [Invalidate and Restart] 버튼 클릭

의 순서대로 진행해준다.

<img width="80%" src="https://user-images.githubusercontent.com/49870384/165988953-163f88a5-164f-43b3-aabe-bf4477bb0d53.PNG">


