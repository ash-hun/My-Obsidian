
--- 

#  Agenda 
	* LLM 기반 임베딩 모델
	* Query Document Relevance Labeling
	* 서비스 적용사례



## 검색 품질 평가?
- 대부분 외주를 주어 전문 인력의 수동 평가
	- 작업자들간의 불일치율 (20 ~ 35 %)
	- 비용대비 정확도의 한계 효용이 점차 감소
	- 시간 대비 작업량의 peak를 찍고 감소함

고품질 임베딩 모델에 대한 Needs
- 검색 도메인에서는 임베더의 용처가 다양함

Embedding Model
- Encoder기반의 모델
- MTEB 리더보드 -> 현 시점 상위 10개가 모두 llm 기반의 임베딩 모델

양질의 학습 데이터 확보
- Hard Negative Keyword가 빈번하게 등장
- 임베딩 학습에 있어 양질의 데이터란?
	- Negative Pair가 지나치게 쉽다면, 학습의 여지가 그만큼 적어짐
	- 문서 Pool 확보
	- Retrieval을 이용해 초벌로 relevance가 높은 문서 파싱
	- Reranking을 이용해 High-Relevance Document 확보
	- Hard Negative를 판별하기 위한 threshold??
		- (R(Q-D') + R(D-D'))/2 > Threshold
- LLM기반 Synthetic Hard Negative tod
	- Prompting을 활용 -> Query와 Positive Doc을 넣어주고 Hard-Negative Doc을 생성
		- hard_nagative document
	- Bi-directional Attention 적용
	- 핵심은 Contrastive Term을 어디에 둘 것이냐?
- 임베딩을 어디서 추출할 것이냐
	- mean pooling 방식

- 그후 SFT(embedder로 adaptation)
- LLM의 dimension은 실시간 서비스 관점에서 부담스럽다
	- 3584 ~ 8192
- Linear Projection layer를 하나 달아서 차원축소
	- Representational Instruction Tuning ~~ (Arxiv)
	- 임베딩 차원을 base model의 25 % 축소하는 경우 평균 약 1.6% 성능손실
	- 서비스 요구사항에 맞추어 Embedding down-projection

- Latency
	- 만약 실시간으로 embedding을 계속 찍어내야한다면, BERT-BASE 모델이 더 적합할 수 있다.


## Query Document Relevance Labeling

if) 사용자 검색어가 제네시스 g80 페이스리프트 가격
	Top 1 Document가 키워드는 모두 들어있으나 의미상적으로 맞지 않다.

검색 품질 평가 (외주)
- Relevant
- Normal
- Irrelevant

Hard Negative 규칙/정의
입력 질의 / 문서 유형 정의 및 예시 (ex. 정답형, 리뷰형, 사이트형) 세분화
Few-Shot Examples
False Negative 방지가 중요함.
Query only < Query w/ Positive Document

관련성 점수 정규화 (RankLlama)

저품질을 판별하는 능력이 강화됨

Large Language Models can Accurately Predict Searcher Preferences
Improving Zero-Shot LLM Raners via Scoring Fine-Grained Relevance Lab els (Google, AcL' 24)

Temperature = Degree of creativity freedom
- 0~2 사이가 아닌 Temperature을 6까지 실험

파인튜닝한 모델을 이용한 inference시 학습데이터가 적을때는 Prompt를 자세하게 달아주는것이 성능향상에 도움이 되나 이후에는 special token을 가지고 하는것과 큰 차이가 나지 않음.

Re-ranker / 생성 모델 간 비교 우위는 명확하지 않음.

## 서비스 적용 사례
- 지식 스니펫 저품질 문서 클렌징
- 질의 - 문서간 semantic을 정확히 이해해야만 선별할 수 있는 저품질 케이스 필터링


## Click Feature
- 사용자가 클릭한 문서의 경우 더 관련있는 문서일 것이다!

## Query Rewriter 모델 평가
- 검색까지 이어진 후 역순으로 평가

## Multi-Modality / Feature 확장
- 이미지 기반 Relevance Labeling
- 정보의 신뢰성, 최신성, 가독성 등 평가 Feature 확장

