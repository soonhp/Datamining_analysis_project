# **IBM HR Analytics Employee Attrition [Management]**



## 1. Dataset Preprocessing



데이터에 불필요한 컬럼, 즉 의미없는 변수들은 제거하고 실험을 진행했습니다. 이렇게 의미없는 변수들을 제거함으로써 추후 모델의 성능을 높이고자 하였습니다.

<img src="README_PNG\image_050.png" alt="image_050">

## 2. Stable_DT

Decision tree는 data에 조금만 변화가 일어나도 결과가 극적으로 바뀔수 있고 결과가 자주, 극적으로 바뀌게 되면 해석적 관점에서 결과를 신뢰하기 힘들기 때문에 DT 보다는 stable_DT 모델을 활용하여 예측하였습니다. 정확도는 0.85이고 f1_score는 0.2456으로 나왔습니다.
밑에는 confusion matrix로 아래와 같은 결과가 나왔습니다.

<img src="README_PNG\image_049.png" alt="image_049">





트리를 시각화한 그래프입니다.총 10개의 depth로 이루어져있고 overtime을 루트노드로 해서 전체근속연수와 월수입으로 나뉘어지고있습니다. 
leaf node는 범주형변수를 숫자로 변환하지 않은 상태로 돌렸기 때문에 타겟변수의 값이 오리지널하게 yes와 no로 나오는 것을 확인할 수 있습니다.

<img src="README_PNG\image_051.png" alt="image_051">



## 3. **CRI /** **Stable_DT** **Rule List (y = 1)**



다음은 concise rule induction과 stable dt의 rule list입니다.
Stable_DT는 범주형 변수를 숫자 변환하지 않아도 코드 에러가 안나서 그대로 돌렸지만, CRI 범주형변수 같은 경우는 숫자로 변환해서 돌려야지 코드가 돌아가서 두 모델간의 RULE 값이 범주형과 숫자로 다르게 나타나는 것을 확인할 수 있습니다.
y=1, 즉 퇴사한 경우의 rule list이고 cri같은 경우는 rule이 분리가 안되서 하나의 룰에 나온 결과를 확인할 수 있습니다.
stable_dt에서는 대부분의 rule에 overtime 즉 야근을 한 경우와 월수입 2800달러 이하인 경우가 포함되어 있어 야근을 하면서 2800달러 한화로 월 354만원 이하이면 퇴사가 이루어진다고 예측한 것을 확인할 수 있습니다.

<img src="README_PNG\image_052.png" alt="image_052">

<img src="README_PNG\image_053.png" alt="image_053">





다음은 y=0, 즉 퇴사하지 않은 경우의 rule list이고 이번에는 cri가 각각 하나씩 룰로 배치가 된 것을 확인할 수 있습니다.
cri에서는 yearswithcurrentmanager가 homogenity가 가장 높은 변수인데 변수를 해석해보면 현재 관리자와 함께 보낸 시간입니다. 

이것이 10.5  이상이면 퇴사를 하지 않는 것으로 예측되고 월수입이 13710달러 이상이면 퇴사하지 않는 것으로 예측할 수 있습니다.
옆에 stable_dt에서는 야근은 하지 않고 근속연수가 2년 이하인 경우에 퇴사를 하지않는 것으로 예측할 수 있습니다.

<img src="README_PNG\image_054.png" alt="image_054"> 





## **4. Rule Quality(y=1)**



다음은 y=1 일 때 weigth relative accurcy를 표로 나타낸 것입니다.
y=1일때 사전확률은 0.1612이고 y=0일 때 사전확률은 0.8388이고 아래와같이 homogenity와 coverage, WA를 확인할 수 있습니다.

<img src="README_PNG\image_055.png" alt="image_055">



## **5. Random Forest**

다음은 랜덤포레스트 모델 구축과 최적의 하이퍼파라미터에 대한 코드 및 성능 입니다.

<img src="README_PNG\image_056.png" alt="image_056">



랜덤포레스트 모델을 통해 중요변수 top 20을 그래프로 나타내보았습니다. 타겟변수 attrition 에 있어 가장 중요한 변수는 overtime으로 야근의 여부가 퇴사에 중요한 변수임을 확인할 수 있습니다. 그 다음은 월수입입니다. 상식적인 선에서도 월수입의 크기가 퇴사의 여부에 영향을 끼치는 것을 알 수 있습니다.



<img src="README_PNG\image_057.png" alt="image_057">



## **6. RF y=0/1** **을** **Pr(y/x)로 대체**



Concise rule induction 모델을 돌릴 때 X_test 데이터셋에서 룰에 포함되지 않은 데이터를 랜덤포레스트로 대체한 표입니다. 
첫번째 컬럼 proba_0dms y가 0일 확률, 두번째 컬럼 proba_1은 y가 1일 확률입니다.
세번째 컬럼은 예측값이고 attrition이 실제 y값입니다.

<img src="README_PNG\image_058.png" alt="image_058"><img src="README_PNG\image_059.png" alt="image_059">

## **7. Pr(y/x) to Regression Tree** 

다음은 Concise rule induction 모델에서 룰에 포함되지 않은 데이터셋을 regression tree로 돌려본 트리입니다. 왼쪽은 depth가 1일때고 오른쪽은 2일때입니다. 작업환경에 대한 만족도가 루트 노드인것을 확인할 수 있습니다.

<img src="README_PNG\image_060.png" alt="image_060">



## **8. GradientBoosting**

<img src="README_PNG\image_061.png" alt="image_061">





Iteration별로 트레이닝셋과 테스트셋의 편차를 확인하기 위한 그래프입니다.
보시는 것 처럼 테스트 데이터셋의 편차가 iteration을 계속 돌아도 트레이닝 셋처럼 편차가 줄어들지 않습니다.
아마도 데이터 클래스 imbalance 문제로 이러한 결과가 나온다고 생각하여 오버샘플링을 통해 Smote 방식을 통해 오버샘플링을 진행하였고 클래스 레이블 값 0과 1의 수를 맞춰주었습니다.
맞춰주고 편차 그래프를 다시 그려보니 전보다는 나아졌지만 iteration이 얼마 안가 일정한 값으로 수렴하는 것을 확인할 수 있습니다.

<img src="README_PNG\image_062.png" alt="image_062">



## **9. Oversampling**

<img src="README_PNG\image_063.png" alt="image_063">

<img src="README_PNG\image_064.png" alt="image_064">



StockOptionLevel : 스톡옵션 정도,  직업만족도, 월수입 같은 경우는 오버샘프링 전후 모두 상위권임을 알 수 있습니다. 

반면 overtime인 야근변수는 오버샘플링 후 하위권으로 내려간 것을 알 수 있습니다.

<img src="README_PNG\image_065.png" alt="image_065">

## 10. KNN

앞서 보신 바와 같이 오버샘플링 하기 전의 성능이 더 좋기 때문에 더 이상 오버샘플링은 진행하지 않았습니다.
Knn 모델 성능 결과도 확인해보았습니다.
오버샘플링 하기 전의  knn모델 성능 결과입니다. 오버샘플링 후의 knn 모델성능보다 전이 역시나 성능이 좋았기 때문에 오버샘플링 하기 전의 성능만 보여드리겠습니다.
최적의 파라미터는 metric은 유클리디안이고 weight은 거리이고 최근접이웃의 수는 20개입니다.
최적의 파라미터를 적용하여 test데이터셋에 돌려본 결과 정확도는 0.8810으로 이전 모델과 비교하였을 때 준수한 결과가 나왔습니다.

<img src="README_PNG\image_066.png" alt="image_066">



## 10. Peformance comparsion

**성능 비교 결과 네 개의 모델 중 Gradient Boosting이 제일 좋은 성능을 나타냄.(최종 모델 채택)**

<img src="README_PNG\image_067.png" alt="image_067">

