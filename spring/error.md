## no suitable constructor found for type

fetch API 를 사용하여 body 에 파라미터를 담아 POST 요청을 할 때

응답 클래스로 매핑되는 시점에 생성자가 없어서 발생하는 오류

디폴트 생성자 (롬복 라이브러리를 사용한다면 @NoArgsConstructor, 아니면 기본 생성자) 를 생성하면 됨.