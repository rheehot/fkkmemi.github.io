---
layout: single
title: database 안정성
category: talk
tag: [talk,idea,database]
comments: true
---

noSQL mongoDB를 사용하며 사람들로 부터 가장 많이 들은 부분은 안정성이었다.

사실 데이터베이스가 뭐하는 건지도 잘 모르는 사람들이었는데..

예전에 처음 node+mongo로 웹서비스를 했을때 mongo데이터를 시원하게 두번이나 날려 먹은 적이 있다.

갑자기 안정성이 곤두박질 치는데.....

이유는? 

{% include toc %}

## 1번째 날림

개발서버이다보니 뭐 백업, 용량도 생각없이 운영했는데 

mongo 데이터가 꽉꽉 차서 리눅스의 cp -r /src /dst 를 걸어두고 화장실 갔다와서 rm -rf /src를 한 것임 :)

그럼에도 불구하고 개발중인 데이터라 크게 위기감을 못 느끼고.. 

마침 잘되었다 싶어서 db initial code(생성시 컬렉션 체크해서 만들고 키부여)를 작성하고 있는데...

평소엔 그다지 관심도 없던 사람들이 의외로 난리가 난거임..  

당황한 나는 휴지통을 찾았지만.. 있을리가 :/

## 2번째 날림

개발 아이피대역에서만 접속하게 방화벽을 설정해둔 줄 알았는데.. > 111.111.111.0/24

이거였다.. 111.111.111.0/0

해당포트로 아무나 열람할 수 있는 공공재 디비를 구축한 꼴이 된것 *계정 조차 없이*

다들 랜섬이라고 얘기하지만 랜섬은 아니다 만인에게 문을 열어줬으니 쉽게 들어와서 훅 지우고 간것임

랜섬과의 공통점은 돈요구 뿐

{% include figure image_path="/images/mongo_sharding/5.png" alt="ransom" caption="탈탈탈 털린 기억" %} 

## 판단

위의 문제는 db의 안정성 같은 문제가 아님을 개발자들이라면 알 수 있는 것인데..

개발서버가 아닌 상용서버라면 당연히 백업, Replica set(복제셋)을 구성했을테지만...

그냥 단순히 개발서버에 개발용도라 데이터가 소중하지 않았고 개발자가 일을 대충하다 보니 생긴일 일 뿐임.. *나만 욕먹음 될 문제*

어처구니 없지만 대부분 죄 없는 mongoDB의 탓으로 돌렸다. 

ACID, 트랜잭션, 성능, 안정성(의미가 있는)등 이런 문제를 거론하는 게 아닌.. 

위의 단순한 개발 실수가 여전히 mongoDB에 불만이 많고 믿음을 주지 못하는 데이터베이스가 되게 만들었다..

*그러게 mysql 쓰지.. 그러게 oracle 쓰지..*

국내 정서상 어딘가 서비스를 납품하고 그쪽 idc 들어가려면 famous rdbms에 맞춰야 하는 것은 당연하지만..

스타트업이면서 직접 서비스 한다면 

- rdbms(index) + noSQL(store)
- noSQL(index+store) 

가 더 좋다고 생각한다..

> 검증되지도 않은 noSQL을 쓰면 큰일 날 것 같다고 말하는 개발자가 종종 있다..  
본인이 rdbms를 사용해 경력을 쌓고 DBA로 훌륭한 커리어가 쌓였다면 이제는 세상을 둘러보길 권하고 싶다.. 

점유율을 보면 성장세를 확인 할 수 있다..

{% include figure image_path="/images/talk/6.png" alt="database market share" caption="database market share" %} 

추가로 대기업에서는 안 쓴다고 해서 링크 하나 걸어 둔다..

일반적인 인덱스 용도가 아니더라도 인터넷 공룡들이 사용하고 있음을 볼 수 있다..

{% include figure image_path="/images/talk/7.png" alt="mongodb customers" caption="mongodb customers" %} 

[https://www.mongodb.com/who-uses-mongodb](https://www.mongodb.com/who-uses-mongodb)

## 결론

비개발자, 클라이언트는 화면에 출력되는 것만 보면 되는데... 

일일히 설명해봐야 입만 아프다는 것을 깨달았다..