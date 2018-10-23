---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 27 클라우드 서버 가입하기
category: nemv
tag: [nemv,server,toast,cloud]
comments: true
sidebar:
  nav: "nemv3"
---

이제부터 3부 응용편 시작합니다.

보통 응용편이라 하면 뷰나 몽고 노드등을 더 심화 학습하는 것이 먼저지만..

너무 학습하는 느낌이 나서 지치죠..

로컬로 작업하는 것 보다는... 

실제 서버에서 구동되는 모습을 보며 "지금까지 작업한게 정말 서비스 할 수 있는 것이구나~" 라는 느낌을 먼저 심어 주고 싶습니다.

서버 구성을 나중에 하실 분은 다음 강좌를 기다리세요~  

{% include toc %}

# 클라우드 서버란

보통 넙적한 서버(렉타입)를 직접 사다가 세팅하고 IDC센터에 두던 회사에 두던 하고 라우터니 방화벽이니 세팅하는 것이 일반적인 것이죠.

CPU 2코어에 메모리 4기가인데 사용자가 많아져서 용량을 증설하고 싶으면..

IDC센터 가서 서버 끄고 메모리, 하드 추가하고 켜는 것이죠..

클라우드 서버는 클릭으로 마음껏 서버를 생성할 수 있습니다.

CPU나 메모리도 클릭 몇변이면 변경이 됩니다.

매우 편리한데 가격이 문제이겠죠?

# 클라우드 서버 비교

간단히만 써보고 비교해보면..

| 클라우드서버 | 회사 |	가격  | 국내 서버 | 편의 | 무료 |
| --- | --- | --- | --- | --- | --- |
| AWS	| 아마존 |	100% | 있음  | 복잡함, 어려움 | 12개월 무료인데 못써먹을 수준의 서버 |
| GCP | 구글 | 	90% | 없음 | 그럭저럭, 아직 영어임 | 무료인데 메모리가 500메가 서버 |
| UCloud | KT | 90% | 있음 | UI가 후지고 개선이 없음 | 없는 듯? |
| TOAST	| NHN | 80% | 있음 | 한글이고 단순함 | 20만원 주고 시작 |

전부 맛보기 정도로는 써봤습니다. 현재 KT 쓰고 있는데 그닥입니다..

커리어 쌓기에는 AWS가 분명 좋습니다만, 비싸고 복잡합니다.. 물론 쉽게 쓰면 뭐든 쉽지만..

구글은 곧 국내서버 둔다고 하는데 제일 가까운 일본서버는 쓰기가 싫어서 패스..

토스트는 한글이고 단순해서 사용합니다. 현재 KT에서 옮기는 중입니다.  

# 토스트 서버 가격은?

![alt 클라우드 서버 가격](/images/nemv/스크린샷 2018-10-19 오후 12.16.11.png)

보통 다른 클라우드들도 시간당으로 받습니다

u2.small로 한번 해볼까하는데 시간당 16원이네요..

16 X 24 X 30 = 11520 이네요 한달에 이것저것(아이피등)해서 2만원 나오니.. 20만원으로 10달은 쓸 수 있겠네요..
 
하지만 사용하면서 나오는 가격을 항상 예의 주시해야합니다.

20만원 다 소진되면 등록된 카드에서 돈나가기 때문에 6개월 후 알람 맞춰놓고 서비스 종료하시기 바랍니다.

# 회원가입

핸드폰 이메일 인증이 필요합니다.

그 중 곤란한 것이 신용카드 등록인데요.. 뭐 이건 어느 클라우드서버나 마찬가지로 하게 됩니다.

공짜로 이것저것 하게 유도해 놓고는 까먹고 있을 때 슬금슬금 카드로 돈나가게 하는 구조죠..

> 저도 AWS 뭣모르고 쓰다 폭탄 맞은 적이 있습니다.  
사용도 안했는데 200만원 나와서 뭐라했더니 대인배 아마존이 다 환불은 해줬습니다.

직접 신규 가입하는 모습을 영상에 담아봤습니다.

개인적인 내용이 들어가서 편집을 간만에 해봤습니다.

꼭 토스트를 사용할 필요는 없습니다. 

다른 것도 비슷하니 직접 서버 구성하는 것을 보면서 참고하시면 됩니다~

# 영상

{% include video id="S4mj-hJ9YV0" provider="youtube" %}   



