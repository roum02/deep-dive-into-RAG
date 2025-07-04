# 02.프롬프트와 출력 파서

> 프롬프트 단계는 리트리버에서 검색된 문서들을 바탕으로 언어 모델이 사용할 질문이나 명령을 생성하는 과정이다.

## LLM 프롬프트 템플릿

: AI 모델과의 상호작용을 표준화하고 효율적으로 만들어 주는 구조화된 텍스트 형식.

이 과정을 통해 언어 모델은 사용자 질문에 대해 최적화된 방식으로 응답을 생성함.

- 프롬프트 템플릿의 구조
  > 지시사항, 사용자가 입력한 질문, 검색된 정보인 문맥

![](https://blog.kakaocdn.net/dn/cFjPaH/btsDDZQbbF3/Cjz61kToKmCECPf5IB4NKK/img.png)

## 퓨샷 기법

: gpt는 예시를 참고해 답변을 잘 따라하는 성향이 있기 때문에 예시를 제공하여 답변 형식을 미리 알려 주면 더 나은 결과를 얻을 수 있다.

- 예제 선택기
  : 예시를 모두 프롬프트에 들어가면 비용이 발생되므로, 질문과 유사한 예시만 넣는 기법

- MMR 알고리즘
  : 정보 검색과 문서 요약을 할 때 다양성과 관련성을 모두 고려하여 결과를 선택하는 방법이다.

---

## 출력 파서

: LLM의 출력값을 구조화된 형식으로 변환하고, 답변에서 우리가 원하는 정보만 뽑아낼 때 유용하게 사용

---

13장까지는 선수과목임 (part5)

1. 프롬프트 이해

- llm 만들 때 프롬프트 중요함.
  chp.5-6이 프롬프트에 대한 이야기인데, 왜 써야하는지에 대한 당위성에 대한 이야기이다.

잘 물어보는 프롬프트/잘 대답하는 프롬프트

2. LLM 모델의 이해
   : 모델별 특성/모델 서비스별 특성

막연하게 모델들의 특징을 이해하고 있음. 일반인처럼 이해하고 있음..

3. 컨텍스트(메모리)
   컨텍스트? : 한국어로 번역이 안됨. 문맥이라기에는 미묘함. 맥락이 아니니까. 배경 느낌.

이 배경에 들어가는게 너무 많음. 감정, 의학적인 질문? 이걸 제공해주는거.

- 제공방식별/저장방식별 특징

4. 외부 데이터 가공

- 데이터로더/데이터가공

-> 여기까지가 이번 스터디의 범위!

## 프롬프트 작성 기본

이 책의 단점: 각 장에 대한 중요성이 살짝 부족하다. 이 부분을 보충해드리겠당

### 프롬프팅 기술이 실제 작동하는 원리

LLM이 뭔지를 이해해야함. LLM은 토큰생성기임. attention(가중치)기반의 토큰 생성기임. 내가 질문으로 200개 토큰 던짐, 답변을 50개의 토큰으로 함. 이 중에서 가중치 있는 것을 기반으로 . 그다음의 토큰을 만든다.

입력~출력의 모든 토큰 중에, 가중치 높은 토큰을 중심으로 동작함. 어떤 토큰이 . 더 가중치가 높음. 모든 모델이 어텐션 기반임! 어텐션 기반이 아닌 제너레이터 모델이 없당

1. 셀프어텐션: 입력토큰을 참조하므로 역할/지시 등이 갱성에 계속 영향을 줌. - 너는 친절한 사람이야 하면 생성 내내 영향을 줌

2. 위치 우선: 어텐션은 보통 앞에 나온 토큰이 . 더강하게 반영됨. 그래서 만약 중요한 게 있으면 앞에다가 부여해야함. 앞에다가 중요한 지시를 두는 프롬프트를 짜야함.

3. 의미유사헝: 역할/지시 등이 생성된 토큰과 상호작용해 생성방향을 유도함. 과거에 생성된 것과 생성된 토큰이 랜덤하지 않고, 이 생성 방향을 유도함.

4. 학습적합성: 우리 가 쓰는 모델도 학습된 데이터셋인데, 데이터셋과 유사한 질문을 물어볼수록 더 대답을 잘함. 모델별로 잘 질문하는 방법이 다름.
   p. 227 참고
   LM 스튜디오

2) LLM 생성 원리에 따라 단순 질의가 아닌 유리한 질의를 만들 수 있음.

3) 무작위적인 토큰 생성을 특정 구조의 토큰 생성으로 유도할 . 수있음.

4) 모델 내 학습 내용 외적으로 컨텍스트 추가해줄수잇음

결론: 프롬프트는 효과가 있다.

LLM을 함수처럼 사용한다면? ! 생성방향 유도가 우리 생각보다 훨씬 정교함.

20기가 모델: 데브 스트랄 모델.. 우리 못이김 ㅠㅡㅠ

프롬프트는 원리가 있기 때문에, 이미 이론화 되어있음. 공학적인 방법을 사용한다는 것.

- 프롬프트 엔지니어의 업무일지
- 최고의 프롬프트 강의

## 프롬프트의 5대 요소

1. instruction: 구체적인 요구사항이나 지시사항
2. context: 생성시 참고할 추가 정보 제공
3. example(shot): 입력과 출력에 대한 구체적인 예시이면서 동시에 추가정보임 -> 지시와 컨텍스트 모두 영향을 끼침
4. input data: 사용자 질의의 형식
5. output data: 생성결과의 형식

### 잘 알려진 기법

1. example추가 : 안주면 제로샷, 샘플 1개 주면 원샷, 여러개 주면 퓨샷 -> 입출력포멧 점검과 동시에 추가 컨텍스트 제공함.
   예제를 안주면 생성 편향이 안생김. 제로샷이 좋을 수도 나쁠수도있음. 고객에게 헛소리 안하게 하려는 상담은 샷을 왕창 준다.
2. chain of thought: 문제를 단계별로 해결하도록 지시하는 기법 -> COT방식(생각의 나무) p.147) COT 방식에 가까움

- 스티븐잡스 죽은거 해결 > 00 죽은거 해결 > 누가 . 더빨리 죽엇나 비교 >> 개별 질문이 . 더쉽다. 그 체인을 이용해서 답을 낸 케이스임!!

3. generated knowledge : 정확한 형태의 정보제공
4. prompt chaining : 문제를 세분화하여 해결함

-> 프롬프트 엔지니어링 책을 우습게 보지마라 이거 엔지니어링 책이다.

이 수많은 템플릿들은 알고보면 텍스트의 조합임. 하도 잘 통하니까 함수화 시켜놓은 것 뿐임. 어떤 영어 단어가 어텐션을 잘 받는지 이미 알려져잇음.

## 모델에 대한 이해

vllm/lm studio/deep speed(gpu병렬화)
학습구조에 따른 모델선택 : 학습구조에 따라서 모델 선택해야함

1. pretrained-only(seed 모델) - 기초지식만 학습한 상태(한국어, 등) 편향/지향이 낮고 중립적임. 좋게 이야기하면 편향 없고, 나쁘게 말하면 딥하지 않음. 학습하기 좋은 시드모델 (네이버 클로바 -> 한국어 학습만 시킨 시드모델이 없었음. 대단한거. )
2. supervised finetuned - 특수목적에 맞게 추가 튜닝(넌 코딩 전문가야..)
3. aligned/instruction tuned - 2번에서 prompt에 반응하게 정렬됨. 넌 의학이야기가 나오면 . 더 깊게 말해, 너 친절하게 말해 등 편향을 가지고 있음. (dpo, rlhf) 지식이 늘어난게 아니라 편향된 것. 그런데 우리는 이게 . 더편함. 우리는 모두 편향된 사람임.
4. role-optimized - 특정 역할에 특화되어 학습됨. 특정 역할에 편향시킴. (MS의 사업분야임. 미국산업에서 사용되는 산업전용 ai공급을 MS가 엄청나게 함. B2B의 왕자임)
5. mixture of experts - 특정 역할에 특화되어 학습됨. 모델의 학습방향이 특정 role학습시킴. 통합적 사고, 캡스톤 사고시에 사용. 생물학자이자 건축학자
6. continually pretrained - 1번에 일반 지식을 . 더밀어넣어서 강화함. 여전히 편향이 없음. 상식이 풍부한데 . 더똑똑해짐. 우리가 일반적으로 쓰는 모델. 라마3 > 라마 3.4... 이거 거의 한계임 더 밀어넣을 지식이 없음. 이미 gpt4는 세상의 모든 지식을 가지고 있는 수준임. gpt5는 더 많은 지식을 가질 . 수없음.
7. agentic - 외부의 툴과 연동 가능한 옵션이 제공됨. 툴이나 펑션을 연결할 . 수있는 능력을 모델 자체가 가지고잇음.

구글은 굉장한 회사이다. 오픈 api 맞다이 뜰 . 수있는 회사가 엔트로피랑 구글임. 대등한 수준의 회사가. 큰 차이가 있음. 구글은 TPO?임. 구글을 제외하고 다른 회사들은 엔비디아 GPU에 의존할수밖에 없음. 엔비디아 GPU가 아닌 것으로 자체 머신으로 구현한 유일한 회사임. 그래서 가치가 다름. 그래서 제미나이가 더 저렴함. 가격경쟝을 할 . 수있는 이유임. 나중에 물량공세 가능함. 구글은 하드웨어도 자기껄로 개발함 ㄷ ㄷ
국내 리벨리원, 퓨리오사? NPU임 쓸데없음.
퀄컴도 퀄컴칩으로 데이터센터 구축햇는데 고작해야 8기가 모델임. 경량수준임.

## 모델 튜닝

full fine, continued pretraining - 바닥 근처 레이어의 지식 학습 : 우리가할게 아님, 하고싶으면 1억 있어야함 ㅎㅎ 우리는 여기 가면 안됨 . ai 스타트업들이 투자받아서 이런거 한다구 설침. 카카오도 이거해서 욕먹음. 우리는 경량튜님함~

### 경량튜닝

: 프롬프트로 학습시킴) 컴플리션 질의(질문+답) 기본적인 단위임

타이레놀은 약이다: 팩트임.

- 경량튜닝: 몸이 아플때 감기약을 찾는다면 타이레놀 (컴플리션 질의로 바꾼 것. 이게 데이터셋을 만든다는 거고 데이터 가공임)
  - 어떻게 컴플리션을 만들 것인가!! 이게 어려움. 어떻게 학습시킬것인가?

1. LoRa - 특정 행렬 저차원만 업데이트하게 학습

llm은 다차원구조의 향렬로 되어있는데, 특정 레이어만 강화하는 것임

2. QLoRa - lora를 4비트로 압축한거. 우리가 할 . 수있는 가장 경량 모델임. 원래 모델에 얇게 튜닝. 4비트로 압축해 빠르고 가벽게 학습
3. prompt tuning - 프롬프트로만 학습

instruction/alignment 튜닝

1. SFT 컴플리션으로 지도 학습
2. instruction - 1인데 하나의 답에 여러개의 질문으로 구성됨.

- 타이레놀 답은 하나인데 질문을 여러개 줘서, 여러 상황에서의 답변을 구축함. 몸이 으슬으슬할땐? 타이레놀. 두통에는 타이레놀 ... .

3. RLHF - 사람이 선호도를 주어 튜닝

- 컴플리션을 구성해서 사람이 만들어줌. 너 이건 좋은 결과 이건 나쁜 결과... 사람이 지도학습을 한거.

4. DPO - RLHF 없이 승/패 쌍으로만 선호도를 튜닝
5. RLAIF - aI가 스스로 생성한 피드백 튜닝

## 컨텍스트

주요 논제

1. 프롬프트에 컨텍스트를 포함할수록 비용이 커짐
2. 공급 데이터의 효과적인 주입방식
3. 공급 데이터의 압축법 > 효과적인 주입방식!

ㅎ결책

1. llm을 통한 요약생성
2. llm으로 정제한 데이터만 사용
3. 데이터 의미구조를 해석하여 변형 (그래프) -> 그래프가 llm 시대 가장 좋은 생성용 데이터!

- 시맨틱 구조 (html5) -> llm 이 엄청나게 잘 소비할 . 수있음.
- 내 웹사이트의 시멘틱 구조를 잘 검색할 . 수있음. geo가. geo 최적화 봇. 시멘틱 태그 중요함.
- 이제 검색엔진인 seo의 시대는 갔다...

4. 인메모리와 영구저장소의 효과적인 활용

- 기억은 방대하게/필요한것만 빼서 저장

5. 검색을 통해 필요한 부분만 로딩

-> 어처피 컨텍스트는 쌓이니까 자체 구동모델은 QLoRa를 통해 계속 몯델을 튜닝해주고 컨텍스트를 줄이는게 이득. 정기적으로 컨텍스트가 많이 쌓이면 저 복잡한 메모리 다루는게 아니라 튜닝으로 밀어넣으면 됨.

이 책은 랭체인 쓰는 것에 집중하고 잇음. 인공지능 사전지식이 많이 안나옴.
자바를 파듯이 llm을 파지 않았으니까 잘 모르는것임. 기술적 디테일들을 잘 봐야함.

## 외부데이터로딩

data import

1. 주요한 외부 데이터는 대부분 시맨틱적이지 않음. 형식 구조가 있을 뿐이지 의미론적이지 않음. json이 그나마 나음. 수 많은 옵셔널 필드를 가ㅣ고 있는 겨웅가 많음.
2. 다양한 포멧해석, 비텍스트 처리, 대용량분산로딩, 여러 IO처리를 요구함.
3. 로딩한 데이터는 정합성 검사, 일반화변환, 특수 변환 등을 정제를 해야함.
4. 알고리즘적인 해결책과 LLM을 통한 해결책을 믹스하여 최적화함.
   \*\*\* ai 시대 개발자란?
   : 바이브 코딩은 중요한게 아님. 그게 그렇게 대단한 일은 아님. 사실 코드로 문제를 해결하지 않고, ai를 활용하여 문제를 활용하는게 더 중요함. 코드를 ai가 짜준게 중요한게 아님.

5. 이를 지속적으로 처리할 . 수있는 구조와 운영 및 모니터링 필요.

split

-> 데이터 전처리 ㅎㅎ 이제 곰돌이 눈붙이기 전에 데이터 전처리 세상임/ 옛날 머신러닝에서는 데이터 노동 햇는데 이게 ai시대에 컴플리션 노동으로 바뀐거임. -> 이 동네는 완전 노하우의 영역(노가다)

허걱.. 대부분의 언어는 고등학교 이상의 지식을 공부할 수 없어서 영어로 공부함. 한국어는 축복의 언어 중 하나다

1. 전처리가 끝난 데이터를 기반으로 다시 분할을 시행
2. 명확한 분할 목적 정의 > 왜 분할하고 싶은지 목적이 중요함. 이걸 굉장히 정교하게 짜놔야한다.
3. 분할 목적과 데이터의 형태가 조합되어 다양한 분할 전략을 구성함.
4. 분할 결과 평가방법 정의 > 이건 잘 된거고 이건 잘 안 된거고 정의해야함. 평가 방법을 정의해야함.
5. 4번에 의해 결과가 안 좋으면 계속 전략 변경
6. 한번 잘 되어도 계속 감시해야함.

\*\*\*\* 이모티콘이 포함한 지시를 넣으면 어텐션이 더 크게 먹는다?! 그래서 이모지 나오는듯 holy molly..... ...
프롬프트 책 꼭 읽으세용

<!-- https://docs.google.com/presentation/d/1uCDDYPRTZ3rza77BTg5K6XnfgeYGoeVwzsm1X3O4A3s/edit?slide=id.g36752c57757_0_140#slide=id.g36752c57757_0_140 -->
