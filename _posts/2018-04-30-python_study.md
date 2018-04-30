---
layout: post
---
# 파이썬

https://wikidocs.net/6  
https://wikidocs.net/77  
https://wikidocs.net/5763  


https://www.codecademy.com/learn/learn-python  
https://doc.scrapy.org/en/latest/intro/install.html#installing-scrapy  


https://code.tutsplus.com/ko/tutorials/how-to-write-your-own-python-packages--cms-26076  
https://doc.scrapy.org/en/latest/intro/overview.html  
https://tonyne.jeju.onl/2015/02/04/%EC%98%A4%ED%94%88%EC%86%8C%EC%8A%A4-scrapy-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9B%B9%ED%81%AC%EB%A1%A4%EB%9F%AC/  


scrapy shell   #scrapy xpath,css 찾을때 사용  
fetch ('url....')  
view(response)   #html 웹페이지로 띄워 줌  
print response.text  
response.css('hr').extract_first()  


-- 샘플 페이지 (3초뒤 내용 변경되는..)  
http://pythonscraping.com/pages/javascript/ajaxDemo.html  



abs(x)  
절대값 반환  

hex(x)  
16진수 값을 문자열로 반환  

bin(x)  
주어진 수의 2진수 값을 문자열로 반환  

oct(x)  
주어진 수를 8진수 값을 문자열로 반환  

round(number[, ndigits])  
주어진 숫자를 반올림, []부분은 생략가능  

pow(x,y[,z])  
거듭제곱  
예) pow(2,8) = 2**8,   pow(2,8,3) = 2**8%3  

** 거듭제곱  

% 나머지  

str.isupper()  
문자열이 모두 대문자이면 true 반환  

str.islower()  
문자열이 모두 소문자이면 true 반환  

str.isalnum()  
문자열이 알파벳과 숫자로 이루어져 있으면 true 반환  

str.isalpha()  
문자열이 알파벳으로 이루어져 있으면 true 반환  

str.isdecimal()  
문자열이 모두 숫자이면 true 반환  

str.isprintable()  
문자열이 출력 가능하면 true 반환  

str.isspace()  
문자열이 공백 문자들이면 true 반환  

str.istitle()  
문자열의 첫번째 글자가 대문자이고 나머지가 소문자이면 true 반환  

len(문자들..)  
문자열의 길이 값 반환  

str.find(sub[,start[,end]])  
문자열에서 sub에 해당하는 문자가 위치한 인덱스 값 반환  

str.index(sub[,start[,end]])  
.find와 같은 동작 실행, 대신 찾는 문자열이 없으면 ValueError가 발생함  

str.rfind(sub[,start[,end]])  
.find와 같은 동작,  뒤에서부터 검색  

str.rindex(sub[,start[,end]])  
.index와 같은 동작, 뒤에서부터 검색  

str.count(sub[,start[,end]])  
문자열의 개수를 센다  

str.startswith(sub[,start[,end]])  
문자열이 prefix로 시작하는지 확인  

str.endswith(sub[,start[,end]])  
문자열이 suffix로 끝나는지 확인  

str.join(리스트)  
리스트로 된 목록을 하나의 문자열로 합친다  
 
str.partition(구분자)  
문자열을 sep를 중심으로 앞뒤로 나눈다 (구분자가 포함되어 나뉘어진다)  

str.split(구분자=none, maxsplit=-1)  
문자열을 sep 중심으로 압뒤로 나눈다 (maxsplit은 분리할 횟수를 지정한다)  

str.splitlines([keepends])  
문자열이 여러줄일때 각 줄을 분리한다. 마지막 라인이 공백이면 제거  

str.rpartition(구분자)  
partition과 동일한 기능, 뒤에서 검색을 시작  


---------------

### 파이썬 스크래핑 오픈소스

pipe2py  
야후 파입스는 놀라우면서 유용한 툴이지만 더 큰 통제력을 행사하거나 자신의 머신에서 파이프를 운용하고 싶을 때가 있다. 그 해답은 바로 야후 파이프의 세부사항을 (id를 통해) 수집하고 이것을 파이썬으로 컴파일하는 파이프투파이(pipe2py)라는 툴이다. 이 파입스 엔진은 파이프투파이의 이행으로 야후 파이프 ID를 수용하며 구글 앱 엔진(Google App Engine)에서 요청에 따라 새로운 파이프를 컴파일, 저장, 운용한다. 참고로 현재 파이프 엔진에 새로운 파이프 추가 시 문제가 있어 보이며, 기존의 많은 파이프가 동작하지 않는다. 아마도 더 이상 존재하지 않는 자원이 존재하기 때문인 것으로 보인다).


Racktables  
소규모 IT 환경에서도 모든 장비를 추적하는 것은 쉽지 않은 일인데, 이 경우 웹 인터페이스 데이터센터 자산 관리 시스템을 제공하는 무료 오픈소스 애플리케이션인 랙테이블스(Racktables)가 바로 그 해답이다. 랙테이블스는 PHP5 및 MySQL 서버 버전 5.x가 탑재된 아파치 웹 서버와 함께 이노DB(InnoDB) 및 유니코드 지원을 필요로 한다. 랙테이블스를 이용하면 모든 기기, 랙, 엔클로저 목록을 작성할 수 있을 뿐 아니라 어떤 랙에 어떤 기기가 있는지, 그 물리 포트와 링크를 명시할 수 있으며, IP 주소를 관리하고 NAT 규칙을 기록하며 시스템의 다양한 객체에 파일을 첨부하고 사용자를 생성 및 관리하며 사용자 권한을 할당하고 사용자를 포함한 시스템의 모든 것에 태그를 정의할 수 있다. 라이선스: GPL(각 소스에 열거)


ownCloud  
자체 스토리지 클라우드는 거부하기 힘든 매력이 있다. 스토리지 클라우드를 손쉽게 생성할 수 있는 많은 상용 제품이 존재하기는 하지만 무료 오픈소스 클라우드 플랫폼을 사용할 때의 이점은 엄청나다. 여기에는 그 메커니즘의 이해, 문제를 논의할 수 있는 사용자 커뮤니티, 맞춤형 기능 및 시설 구축을 통한 자체 비즈니스 목표 이행 등이 포함된다. 관심이 있다면 웹 및 WebDAV 접속을 지원하여 기기들을 동기화하며 웹 상의 기본 편집도 가능한 무료 오픈소스 클라우드 플랫폼인 오운클라우드(ownCloud)를 살펴보자. 심지어 플러그인과 API가 있으며 안드로이드와 iOS를 지원한다. 그리고 기업용 버전은 9,000달러로 50명의 사용자가 사용할 수 있다. 라이선스 : 다양함 (아페로 퍼블릭(Affero Public) v3 및 GPLv2)


Dataflow  
데이터플로우(Dataflow) 언어는 프로그래밍에 대한 전혀 새로운 사고방식이다. 알고리즘의 기능은 시각적 객체로 캔버스(Canvas)에 위치하고 있으며 코드(Cord)를 통해 서로 연결되어 있다. 데이터는 이런 코드를 통해 하나의 객체에서 다른 객체로 흐르며, 객체는 그 종류에 따라 데이터를 조작한다. 따라서 오디오 애플리케이션에서는 객체가 음원으로 연결하는 코드를 통해 유입되는 데이터 스트림에 잔향을 더하며 다시 코드를 통해 데이터 스트림을 기록하는 객체로 전달할 수 있다. 퓨어 데이터(Pure Data)는 윈도우, OS X, 리눅스에서 사용할 수 있는 무료 오픈소스 프로젝트로 데이터 플로우 언어의 좋은 예이다. 꽤 탁월하다. 라이선스 : PD 라이선스(PD License)


Huggin  
아마도 이 분야에서 가장 뛰어난 IFTTT(If This Then That) 서비스를 살펴보고, 또 매우 유용한 야후 파입스(Yahoo Pipes)에 매료되었을 수도 있다. 하지만 이들과 유사하면서도 약간의 수정을 가하고 특수 기능을 추가하고 싶다고 생각해 본 적이 있다면, 허긴(Huginn)이야말로 바로 그것이다. 루비 온 레일즈 기반으로 구축된 이 야심 찬 프로젝트는 웹 콘텐츠를 읽고 이벤트를 모니터링 및 포착하며 동작을 수행할 수 있다. 이 프로젝트는 설치와 운용이 꽤 복잡하지만 뛰어난 자동화 툴을 찾고 있다면 그만한 가치는 충분하다. 라이선스 : MIT


Docker  
독커(Docker)는 소프트웨어 패키지화의 미래라는 생각이 든다. 독커는 리눅스 애플리케이션을 의존성에 따라 "컨테이너(Container)"로 묶는 과정을 자동화한다. 이런 컨테이너(특히 가벼운 형태의 가상화)는 앱을 호스트와 그 자원으로부터 고립시켜 배치 속도를 향상시키고 휴대성을 확보하며 앱과 그 호스트 환경을 더욱 견고하게 한다. 독커가 미래적인 방식이라는 증거는 아마존 웹 서비스에서 확인할 수 있으며, 해당 서비스는 얼마 전 독커를 엘라스틱 빈스토크(Elastic Beanstalk)에 통합했다고 발표한 바 있다. 라이선스 : 아파치(Apache) v2.0


Scrapy  
스크래피(Scrapy)는 웹 사이트를 돌아다니면서 구조화된 데이터를 추출하기 위해 파이썬(Python)으로 작성한 애플리케이션 프레임워크이다. 그리고 나서 이 데이터를 데이터 마이닝, 정보 처리, 이력 기록 등 일련의 유용한 애플리케이션에 활용할 수 있다. 본래 화면 스크랩(좀 더 정확히 말하자면 웹 스크랩)용으로 개발된 스크래피는 API를 이용한 데이터 추출(아마존 어소시에이츠 웹 서비스(Amazon Associates Web Services))에 사용하거나 일반 용도의 웹 크롤러(Web Crawler)로 사용할 수 있다. 스크래피는 스크래핑허브(Scrapinghub), 플래스(Flax), 고스크레이프(GoScrape) 등 많은 기업들이 상용 지원을 제공하고 있다.  
라이선스 : https://github.com/scrapy/scrapy/blob/master/LICENSE





