---
title: GET 통신으로 query string을 넘길때 특수 문자 사라짐 현상
categories:
- REST API
feature_image: "https://picsum.photos/2560/600?image=872"
---

특수 문자 검색이 되지 않는다는 이슈가 있었다. DB 탐색을 통해 문서에 있는 특수 문자를 검색하기 위해, client에서 GET 방식으로 특수 문자를 넘겨줄때, WAS의 Controller에서 인식하지 못하고 있었다...

#### URL 인코딩

- 일반 text는 인식을 잘하는데 왜 특수 문자를 보내는 경우에는 인식하지 못할까?
- 구글링을 해본 결과 인코딩 문제라는 것을 알았다. 
- URL 인코딩 방식인 퍼센트 인코딩으로 인코딩을 해주면된다. 

##### 퍼센트 인코딩?

- <span style="color:tomato; background-color:#fff5b1" >퍼센트 인코딩(percent-encoding)은 URL에 문자를 표현하는 문자 인코딩 방법이다.</span> 이 방법에 따르면 알파벳이나 숫자 등 몇몇 문자를 제외한 값은 옥텟 단위로 묶어서, 16진수 값으로 인코딩한다.
- 퍼센트 인코딩 규약은 <span style="color:tomato; background-color:#fff5b1" >RFC 3986</span>에 정의되어 있다. 이 RFC에 따르면 URL에서 중요하게 사용되는 예약(reserved) 문자가 있고, 또한 인코딩이 필요하지 않은 비예약(unreserved) 문자가 존재한다.
    - 예약 문자 : ! * ' ( ) ; : @ & = + $ , / ? # [ ]
    - 비예약 문자 : A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
a b c d e f g h i j k l m n o p q r s t u v w x y z 0 1 2 3 4 5 6 7 8 9 - _ . ~
- 예약 문자 중 일부는 URI에서 중요한 문법적 의미를 가지고 있기 때문에, 그 의미로 사용할 것이 아니라면 반드시 인코딩을 해야 한다.

#### JavaScript API

- 퍼센트 인코딩으로 인코딩 및 디코딩을 해주는 함수가 javascript에 4종류가 있다.
- encodeURI( uri ) : URI에서 자주 사용하는 : ; / = ? & 등을 제외하고 인코딩하는 함수
- <span style="color:tomato; background-color:#fff5b1" >encodeURIComponent( uri )</span> : 모든 문자를 인코딩하는 함수
- decodeURI( uri ) : encodeURI의 결과물을 디코딩하는 함수
- decoudeURIComponent ( uri ) : encodeURIComponent의 결과물을 디코딩하는 함수

- 검색할 텍스트에 대해 encodeURIComponent 함수를 사용해 이슈를 해결하였다!
