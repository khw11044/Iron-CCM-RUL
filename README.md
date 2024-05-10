# 연속 주조기의 RUL 데이터 분석 
## RUL Dataset from Continuous Casting Machine

[캐글 페이지](https://www.kaggle.com/datasets/yuriykatser/rul-dataset-from-continuous-casting-machine)

## 도메인 설명 

연주공정: 연주공정은 철강산업에서 철 제품을 생산하는 공정중 하나로 연속주조의 줄임말이다. 액체상태의 용강을 주형에 넣어 고체가 되도록 식히는 과정이며 이 과정을 거쳐, 슬래브, 브룸, 빌렛의 반제품이 만들어진다.

![image](https://github.com/khw11044/Basic-RL-for-Process-Control/assets/51473705/bccffea7-d117-4844-aca9-8b419e198a51)

![image](https://github.com/khw11044/Basic-RL-for-Process-Control/assets/51473705/1e82679d-c97c-4e06-b8e3-f0d9a615dcf8)


![image](https://github.com/khw11044/Basic-RL-for-Process-Control/assets/51473705/91b7538c-d2c8-421a-a2bd-ce198bbeaebf)


![image](https://github.com/khw11044/Basic-RL-for-Process-Control/assets/51473705/15e525a3-628a-47fa-b533-9c9e6a8060b7)

![image](https://github.com/khw11044/Basic-RL-for-Process-Control/assets/51473705/d012d508-ac23-402b-b5f5-4a05347daf48)

주괴(鑄塊, ingot)란 상대적으로 순도가 높은 물질의 덩어리다. 보통 금속을 가공하기 좋게 주물로 뜬 것을 말한다.

____


연속 주조 기계(이하 'CCM')는 액체 상태의 철강을 주어진 단면의 고체 빌릿으로 변환하는 장치로, 이후 롤링 생산(예: 철근)이 이루어진다.

금형 슬리브는 CCM 금형의 가장 중요하고 빠르게 마모되는 부분이다. 슬리브는 둥근 형태나 프로파일 단면의 수냉식 구리 파이프이다. 용융 금속이 슬리브 벽과 접촉하면 결정화되며, 주괴의 기본 고체 껍질이 형성된다.

**슬리브 운영 중 주요 생산 문제**는 슬리브의 구리 파이프 표면에 결함이 생기고 내부 공간의 프로파일이 변형되어 열 조건이 방해받는 것이다. 이는 결과적으로 주괴의 품질에 영향을 미친다.  

형태 결함(예를 들어, 정사각형 주괴의 대각선이 같지 않아 이른바 ‘마름모 형태’가 발생할 수 있음), 변형된 면의 치수, 주괴 모서리에 균열이 생길 수 있다.

이러한 결함은 롤링 과정에서 추가 문제를 야기한다. 그리고 제품의 품질 저하와 불량률 증가는 생산의 경제 효율성에 부정적인 영향을 미친다. 이를 방지하기 위해 일정 간격으로 슬리브의 치수를 측정한다. 이 치수가 설계치와 다르면 슬리브는 폐기된다.

또 다른 문제인 **생산에 사용되는 금형의 구리 슬리브의 수명이 짧아지는 문제**는 종종 연속 주조 기계 자체의 운영 매개변수 변경과 관련이 있다. 이러한 매개변수에는 유입되는 용융 금속의 온도, 냉각수의 온도 등이 포함된다.

실제 금형 슬리브의 유용 수명은 제조업체가 명시한 것보다 종종 짧아, 추가 장비 다운타임을 초래하고 사고 및 추가 생산 비용의 가능성을 증가시킨다. 예상 유용 수명은 톤수로 다음과 같아야 한다:

- 180x180의 경우 17,000톤,
- 150x150의 경우 13,000톤.

## 데이터 취득 

CCM 운영 중에 주괴 주조 과정을 운영하는 자동 제어 시스템이 주조 매개변수의 데이터베이스를 생성한다.
수집된 매개변수는 각 주조의 모든 스트랜드에 대해 평균화된 데이터이며, 각 스트랜드의 슬리브 저항만이 다르다.
금형 슬리브를 제거하여 검사한 후에는 SCADA에서 주조 공정 매개변수, 획득한 주괴의 기하학적 형태 및 기타 속성에 대한 초기 데이터를 업로드할 수 있다.
데이터는 실제 생산 시설에서 수집되었으나 이후 저자들에 의해 처리되어, 정리되고, 집계되어 RUL 문제를 해결하기 위해 준비되었다.

## 데이터 features 

RUL column은 "저항, 톤 (resistance, tonn)" column에서 각 슬리브 (sleeve), 결정화기 번호 (num_crystallizer) 및 스트림 번호 (num_stream)에 대해 가장 높은 저항(파괴 순간, moment of breaking)의 현재 값을 뺀 값으로 구성된다.

(The RUL is formed from the column "resistance, tonn" where for each "sleeve", 'num_crystallizer' and 'num_stream' from the highest resistance (moment of breaking) current value is subtracted.)

$RUL = R_{max} - R_{current}$

$R_{max}$ :  'sleeve', 'num_crystallizer', 'num_stream' 조합에서의 최고 저항값

$R_{current}$ : 각 조합에서의 현재 저항값

## Tasks

이 데이터셋을 기반으로 주된 과제는 **결정화기 슬리브의 남은 유용 수명(톤 수 또는 남은 주조 횟수)을 결정하는 모델을 개발** 하는 것이다.

이 문제는 첫 주조부터 마지막 주조 전까지 각 주조에 대해 해결하는 것이다.

그러나 RUL 문제를 해결하는 것 외에도, 생산에서는 **남은 유용 수명의 감소와 연장에 영향을 미치는 주요 요인을 파악하는 작업**이 또한 중요하다.
이는 많은 슬리브가 위에서 언급한 예상 유용 수명에 도달하지 못하기 때문에 관련이 있다. 이를 위해 다음과 같은 과제를 해결하려고 할 수 있다:

- 남은 유용 수명(RUL)에 영향을 미치는 요인을 식별하기,
- 수명을 증가시키는 방법에 대한 권장 사항 개발하기,
- 목표 저항을 가진 슬리브와 그렇지 않은 슬리브의 성능을 비교하고 이를 초래한 매개변수를 결정하기.


-----------------------------

## + 수행 결과 

우선 steel_type이 Arm500인 데이터셋만 수행하였다. (가장 많은 비중을 차지해서)

![image](https://github.com/khw11044/Iron-CCM-RUL/assets/51473705/ceada6f2-ddef-4550-8ecd-29e97ceb9369)

> 모델 model.feature_importances_ 를 출력해보면 위와 같은 결과를 보여준다. 

이는 데이터 설명 (RUL 설명)에서 언급한 것 처럼 RUL이 'sleeve', 'num_crystallizer', 'num_stream'와 "resistance, tonn"의 관계식으로 나타낼 수 있기 때문으로 보인다.

이외에도 각종 원소들의 비율이 RUL에 영향을 줄 수 있음을 알 수있다.

적정한 원소 비율은 제품의 RUL을 늘릴 수 있다.

### 1. 낮은 RUL과 높은 RUL을 갖는 데이터셋으로 나눠서 진행해본다.

**낮은 RUL을 갖는 데이터셋의 크기가 더 크다**

- nomal_RUL : 6,706 개
- high_RUL : 1,210 개 

- nomal_RUL 평균 : 4,608
- high_RUL 평균 : 937,424

**낮은 RUL을 갖는 데이터셋 결과**

- acc: 0.9878
- MAE: 176.108754039883

![image](https://github.com/khw11044/Iron-CCM-RUL/assets/51473705/74ecdf7c-88e4-4463-a0cf-6ee36955641f)

**높은 RUL을 갖는 데이터셋 결과**

- acc: 0.9783
- MAE: 126728.43442353667

![image](https://github.com/khw11044/Iron-CCM-RUL/assets/51473705/a02e44c0-e064-4302-b9f5-5eaf6f53d14c)

### 2. 낮은 RUL과 높은 RUL을 0과 1로 나누어 분류문제로 해석한다. 

![image](https://github.com/khw11044/Iron-CCM-RUL/assets/51473705/563a7052-5bf3-45cf-a0e8-a106809a71df)

![image](https://github.com/khw11044/Iron-CCM-RUL/assets/51473705/8c93719f-db46-4685-8c0c-96964b09cefc)

복잡한 모델을 설계 하지 않아도 0 또는 1을 잘 분류하는 것을 볼 수 있다. 
즉, 이미 features들과 RUL간에 명확한 인과관계가 있음을 알 수 있다. 

![image](https://github.com/khw11044/Iron-CCM-RUL/assets/51473705/f5a541de-4bc6-42a3-ba11-50ce02c122d0)

위와 같은 인자들이 높은 RUL을 결정하는 것으로 보인다. 


