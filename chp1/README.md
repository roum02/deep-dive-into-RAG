# RAG （Retrieval-Augmented Generation）

## 검색, 증강, 생성

## 할루시네이션(hallucination)

## 언어모델의 성능향상을 위한 각 기술의 난이도 순서

- Prompt Engineering
- RAG : 난이도에 비해 성능 향상 비율이 높음 (압도적 성능)/ 할루시네이션 감소에 가장 큰 영향을 미침
- PEFT
- FULL FINE TUNING

## LangChain을 이용한 RAG 시스템 구축

- LangChain: 대규모 언어 모델로 구동되는 애플리케이션을 개발하기 위한 프레임워크. GPT와 같은 언어 모델과 우리가 만들고자 하는 서비스를 쉽게 연결해주는 도구
  c.f. GPT를 이용하여 고객 응대 챗봇 만들기 등 GPT의 두뇌를 원하는 작업 흐름에 접목하여 체인으로 엮는 형태
- ChatGPT: GPT라는 언어 모델을 기반으로 한 대화형 AI 플랫폼

![](https://images.contentstack.io/v3/assets/bltefdd0b53724fa2ce/blt1496b19e4c6f9e66/66ba412a46b3f4241b969f48/rag-in-action.jpeg)

## 리트리버(Retriever, 검색기)

: 해당 질문과 유사성이 높은 문서 찾아옴

다만, 정보에서 관련성 있는 정보만 리트리버에 전달하는 것이 필요 -> RAG 프로세스의 사전 단계

## 전처리 과정(사전단계) : 문서 로드- 텍스트 분할 - 임베딩- 저장

- 문서 로드 : 문서 로드 및 텍스트 분할
- 텍스트 분할 : 문서를 청크로 분할
  - 청크 오버랩: 청크 끝부분에서 맥락이 이어질 수 있도록 일부를 겹쳐서 분할
- 임베딩 : 청크를 컴퓨터가 이해할 수 있도록 임베딩
  - 임베딩이 비용이 들어가는 작업이기 때문에, 매번 할 수가 없어 DB에 저장
- 저장 : 임베딩된 청크를 DB에 저장

## 사전단계 이후의 실행 단계: 리트리버-프롬프트-LLM-체인생성

- 리트리버 : 질문이 주어지면, 이와 관련된 백터를 벡터 데이터베이스에서 검색함.
  - 사용자의 질문을 질문 벡터로 변환하고, 이 질문 벡터가 문서 벡터들과 비교되어 유사성이 계산됨. 이 과정에서 **코사인 유사도** 알고리즘 등 활용
  - 전체 시스템의 품질과 직결
- 프롬프트 : 검색된 정보를 바탕으로 언어 모델을 위한 질문을 구성함. 정보를 바탕으로 어떻게 질문할지를 결정하는 과정
- LLM : 구성된 프롬프트를 사용하여 언어 모델이 실제 답변을 생성함. 수집된 정보를 바탕으로 과제나 보고서를 작성하는 학생임.
- 체인생성 : 이전의 모든 과정을 하나의 파이프라인으로 묶어 주는 체인을 생성함
  - LCEL 문법 활용

## 토큰과 토큰화

- 토큰: 자연어 처리에서 텍스트를 처리하기 위해 나눈 작은 단위
- 토큰화: 텍스트를 토큰으로 나누는 과정

## 토큰화 방법

- 문자 기반 토큰화: 텍스트를 개별 문자로 쪼개어 각 문자를 하나의 토큰으로 취급하는 방식
  - c.f. hello -> 5개의 토큰으로 분리
- 단어 기반 토큰화: 텍스트를 단어 단위로 쪼개어 각 단어를 하나의 토큰으로 취급하는 방식
  - c.f. Hello, World! -> 4개의 토큰으로 분리

=> LLM은 먼저 나올 토큰을 생성하면 그 다음 나올 토큰을 확률적으로 계산하여 생성하는 로직을 가지고 있기 때문에, 문자 기반 토크화와 단어 기반 토큰화를 비교하면, 문자 기반 토큰화의 토큰이 많기 때문에 틀린 토큰을 생성할 가능성이 높음.

c.f. 12개의 토큰 예측이 4개의 토큰 예측보다 틀릴 확률이 높음. 문자 기반 토큰화가 예측을 더 많이 해야함.
더불어, 단어 기반 토큰화도 모든 단어를 저장한 단어 사전이 필요하기 때문에, 비효율적

- 서브워드 기반 토큰화: 단어를 처음에 문자 단위로 분해한 후, 자주 등장하는 문자 상을 결합해 더 큰 서브워드로 만드는 방식
  - 바이트 페어 인코딩(BFE) 알고리즘

## 토큰 사용량

- GPT 4.0 이하 버전의 경우, 한글른 영어보다 좀 더 토큰을 많이 소모함

## 모델의 입출력

- 컨텍스트 윈도우: 모델이 한 번에 처리하고 이해할 수 있는 전체 택스트의 문맥 범위
- 컨텍스트 길이: 한 번에 처리할 수 있는 입력과 출력의 총합 토큰 수
- max_tokens: 출력되는 최대 토큰 수
  - 출력이 길어질수록 입력으로 넣을 수 있는 텍스트의 양이 줄어든다
  - 입력토큰에 비해 출력토큰이 3배 비싸다
  - 따라서 방대한 정보를 다룰 때는 RAG가 낫다

## ChatGPT AI 주요 매개변수와 출력

- temperature: 얼마나 창의적이게 할 것인지
- max_tokens: 최대 토큰 수
- model_name: 적용 가능한 모델 리스트

# 시스템 프롬프트와 사용자 프롬프트

- 시스템 프롬프트: 전역 설정이 적용되므로, 모델의 역할을 나타내는 페르소나 지정
- 사용자 프롬프트: 재무제표 정리나 해석처럼 구체적인 작업

=> 프롬프트 템플릿을 만들어 활용이 가능하다.

# LCEL로 체인 생성하기

- 체인: 프롬프트를 LLM으로 전달하도록 연결하는 파이프라인
- LCEL: 여러 구성 요소를 하나의 체인으로 엮어 주는 표현
- 출력파서: LLM이 생성한 출력 결과를 필요에 맞게 가공하고 구조화하는 단계

# Runable 프로토콜

---

Rag의 의미

Retrieval - 끌어옴
Augmented - 강화
Generation - 생성

Retrieval

1. 텍스트를 특정 차원 벡터로 임베딩
2. 엠베딩 벡터로 이미 소유한 벡터 중 유사한 것 검색
3. 검색된 벡터에 연결된 정보를 가져옴

Augmented

1. 실제로 강화하는 대상은 생성이 아님
2. **보통 context(시스템 프롬프트)를 강화함**

같은 개념으로 Tool AC, Memory AC

- LLM과 무관하게 리트리버가 작동함. 심지어 리트리버는 GPU도 안씀. 이 둘은 다른 서버를 써도 됨
- 우리는 보통 LLM 사용할 때, invoke(질의/LLM) -> print(result)
- RAG로 LLM 사용하면, 질의 -> 엠베딩 [12.34, ...] -> result(db.search()) -> info = result.join -> 질의 + 추가정보 + info -> invoke(질의/ 이 때 LLM 사용)
  => 이 전 RAG의 과정이 LLM과 무관한 솔루션임!!!

# Embeding

: 텍스트를 특정 차원의 벡터로 치환하는 것

1. 문장을 토큰으로 분해하여 리스트로 생성함
2. 각 텍스트 토큰을 어휘 사전을 통해 정수로 치환함
3. 리스트를 특정 길이를 갖는 리스트로 치환함 = 엠베딩

c.f. 랜덤 토큰 버리기 기법 등등 있음

## 엠베딩 모델

1. 토크나이징 + 어휘 사전 + 엠베딩의 결합체
2. **특정 모델로 엠베딩하면 다른 모델의 엠베딩 결과와 호환이 안됨**
3. 어떤 모델로 RAG디비를 구축하면 사용자질의를 엠베딩할 때도 같은 모델을 사용
4. 엠베딩 모델을 교체하면 RAG디비 전체를 재 엠베딩 해야함
5. 엠베딩 모델은 신중하게 고를 것

# 질의 분해 기법

재미있는 기법들을 기업들이 도입하기 시작했음

질의를 하나로 엠베딩 하기 대신 여러 개의 질의로 분리한 뒤 각각 엠베딩 (LLM에게 시킴)

e.g. 삼성은 최근 비메모리 분야에서 크게 뒤쳐지고 있으며, 샤오미에게 밀린다. ai 칩셋도 개발하지 못했다.

1. 삼성은 최근 비메모리 반도체 분야에서 어떤 성과를 보이고 있는가?

-> 반드시 A는 B형태로 되어야 RAG에 걸림

1. 삼성은 최근 비메모리 반도체 분야에서 어떤 성과

이 문장으로 저렇게 바꿔주는게 질의 분해 기법임. 이 문장 안에는 질문자의 의도가 들어있음. 그러나 RAG는 기본적으로 팩트를 기반으로 만든다.

타이레놀의 용법은 00이다. 용량은 00 이다. -> A는 B이다 형태의 팩트

우리의 질문을 팩트 기반으로 변경해야 RAG기반에 잘 걸린다!

----- 수업 내용 정리

1. 소유한 데이터는 팩트 기반
2. 사용자 질의는 컨텍스트(의도나 배경) 내포
3. 따라서 백터 유사도가 일치할 확률이 낮아짐
4. 그래서 질의에서 컨텍스트를 배제히고 사실 기반 질의 생성
5. 그 질의를 바탕으로 리트리버하는게 일치율이 훨씬 좋음
6. 여러 질의의 리트리버는 GPU 자원을 소비하는게 아니라서 부담이 없음

# 프롬프트 캐쉬

1. 벤더마다 제공하는 경우도 있고 안해주는 경우도 있음
2. OpenAI도 특정 모델만 해주고 입력 토큰만 대상이 됨
3. 캐쉬 유지 기간이나 범위도 벡터 마음대로임
4. OpenAI는 prefix일치+특정 크기 이상의 입력으로 동작함
5. prefix일치는 기존 질의와 이번 질의가 앞에서부터 일치하는 부분까지 캐쉬
6. 달라지는 지점이 나오는 순간 캐쉬가 아님
7. system, user의 순서만 바꿔도 안됨

8. 최대한 똑같이 유지하고 제일 마지막에 달라지는 부분을 배치하는게 최고
9. 책 예제에서 <WANT TO CACHE HERE> 이런건 무의미. 마지막에 {} 로 캐쉬

# 랭체인

: 레그를 구축하기 편한 도우미 라이브러리

1. 파이프라이닝 라이브러리 - Runnable(JS Promise 정도, 함수 래핑 객체)로 정의된 Task를 연결하여 실행함
2. 동시성 관련 라이브러리 - 멀티 프로세스 기반으로 동시에 여러 Task 실행(성능 낮음)
3. LLM관련 태스크들을 사전에 많이 정의해서 제공해줌
4. 노트북처럼 보통은 실험실 용
5. 상용으로 도입했던 대부분의 업체가 성능문제로 서비스 사용 포기
6. 랭그래프는 순수하게 태스크 체인을 데이터로만 표현하고 실행기는 별도로 선택
7. 랭체인 팀은 동시성 수행이나 성능 개선을 안 할 생각인듯

랭체인과 랭그래프 차이??
랭체인은 실행기이지만 랭그래프는 실행기가 아님. 실행기 따로 붙여야함. 랭체인 자기들도 안 씀.. ㅠㅠ
그래서 너무 깊게 파고들 필요는 없다...

# 파이프라이닝

: 이전 함수의 출력타입이 다음 함수의 일치타입과 일치하면 함수 합성 가능

c = combine(a, b)

(랭)체인이 RAG의 전부이다!
