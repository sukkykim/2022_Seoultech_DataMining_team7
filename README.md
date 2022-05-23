# 2022_Seoultech_DataMining_team7
# 2022 데이터마이닝 7조 프로젝트
##### 김동주 김범수 김석희 박지혜
_________________
_________________
## 프로젝트 주제
## 서울시 상권 매출 예측
_______
_______
## 프로젝트 배경 및 목표
___________________
매년 높은 수치를 기록하는 폐업률,
예측하기 힘든 매출량으로
창업을 망설이는 예비창업자 多  
서울시에서 제공하는 예비창업자를 위한 창위험도를 알려주는 기존 서비스가 존재하지만, 상권 매출액 예측 서비스는 포함되어 있지 않기에 이를 제공하고자 한다.  
____________
____________
## 사용 데이터
___________________
위에서 언급한 서울시 제공 '우리 마을 가게 상권분석 서비스'에서 사용된 공공데이터셋 19종 중, 매출 예측에 활용될 수 있는 15종 데이터셋 사용.
[데이터셋](https://data.seoul.go.kr/dataList/OA-15582/S/1/datasetView.do) (및 연관데이터 항목 14종)
____________
____________
## 데이터 분석
___________
### EDA
총 19개 데이터셋 중 15개 데이터셋을 선택 및 사용  
해당 데이터셋들은 대부분 17~21년도 데이터로 구성  
상권코드, 분기, 년도의 행 정보 데이터를 통해 병합 후 본격적 전처리 진행  
  
_________________
### 데이터 전처리
_________________

### missing data 처리  
+ 15개의 데이터셋들을 파악할 때에, 한 데이터셋에서 변수 4개에 대해,
17년도 1분기 데이터가 없는 것을 발견.
+ 나머지 2~4분기 데이터셋을 비교해본 결과, 데이터의 분포가 대체로 비슷하여
2분기의 데이터를 1분기의 데이터로도 사용
  
_________________  
### 상관성 분석 - correlation 계수 ([코드](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/codes/correlation%20computing.ipynb))
+ 15개의 데이터셋을 병합하기 전, 각 데이터셋에 대해 상관성 분석을 진행.
  * 한 데이터셋 내의 각 변수 간의 correlation 계수를 구하여 변수 선별을 일차적으로 진행.
![이미지0](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC0.png)
+ 모든 데이터셋에 대해 correlation 값을 구해본 결과, 각 값들은 대부분
절댓값이 0에 가깝거나, 0.8 이상으로 나오는 두 가지의 경우로 나뉘었다.


+ 이에 후자의 경우, 대표되는 변수만을 선별하여 regression에 사용하고자 하였다.
  * Correlation의 절댓값이 높다면, 해당 변수가 타 변수를 잘 설명할 수 있다는 특성을 활용  


+ Ex) 상권_집객시설 데이터셋에서의 상관성 분석 – correlation 계수 0.8 이상 경우 존재 X
![이미지1](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC1.png)
+ Ex) 상권배후지_소득소비 데이터셋에서의 상관성 분석 – correlation 계수 0.8 이상 경우 존재 다수, 변수 선별 진행  
![이미지2](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC2.png)
지출_총금액 변수가
식료품_지출_총금액 변수 및
의류_신발_지출_총금액 변수를 대표  
  
_________________
### 모델링 
_________________
([코드](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/codes/linear%20regression%20model.ipynb))
#### 1) 데이터셋 분할
- Training dataset 
  * 17년도 1분기 ~ 21년도 2분기 데이터셋
- Validation dataset
  * 21년도 3분기 데이터셋
- Testing dataset
  * 21년도 4분기 데이터셋
_________________
#### 2) 앞서 전처리 및 import한 데이터셋을 통해 Linear regression 진행  
- MAE, RMSE
  * 오차 계산 방법론, 대체로 값이 작을수록 좋은 성능
- R_square
  * 결정계수 (원자료에 대한 회귀선의 설명력)
  * 값이 1에 가까울수록, 예측이 실제를 비슷하게 설명  
![이미지3](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC4.jpg)
- 그 결과, 약 0.47의 R_square 값을
validation dataset에 대한 값으로 획득.
- 성능을 높이기 위해, dimension을 줄이기 위한
후처리를 진행 후 다시 학습을 진행
_____________
#### 3) vif, p-value, PCA를 통한 다중공산성 측정 및 feature extraction  
_____________
3-1) 다중공산성 측정 알고리즘인 VIF를 통해 도출한 계수가 10이상인 경우,
_____________
해당 feature들에 대해서 extraction을 진행해볼 가치가 있다.  
![이미지4](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC5.png)
앞서 전처리한 데이터셋에 대해 VIF를 적용한 결과,
그 계수가 10을 넘기는 변수들을 제거 및 linear regression 적용 후,
앞서 사용한 비교분석 지표인 RMSE, R_squared 값의 변화를 관찰.  
![이미지5](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC6.png)

그 결과, 성능이 감소 (오차값 증가 및 R2 계수 감소) 하여, VIF를 적용해 변수를 제거하는 과정을 배제.
![이미지6](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC7.png)
  
_____________
3-2) 다른 다중공산성 측정 알고리즘인 p-value를 통한 dimension reduction 시도
_____________
- 비슷한 방식으로, P 값이 0.05 를 넘어가면 변수 제거를 고려해볼만 하다.
![이미지7](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC8.png)



다시 한 번 성능이 감소 (오차값 증가 및 R2 계수 감소) 하여, p-value를 적용해 변수를 제거하는 과정을 배제.  
![이미지8](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC9.png)


_____________
3-3) PCA를 통해 feature extraction 적용 후 학습 진행. (n_components = 0.95 로 설정) 
_____________
![이미지9](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC10.png)
다시 한 번 성능이 감소 (오차값 증가 및 R2 계수 감소) 하여, PCA를 적용해 변수를 제거하는 과정 또한 배제.  
______________
### 4) Training dataset에 대한 모델 평가  
앞서 3가지의 dimension reduction 방법론을 통해 성능을 증가시키지 못하여,
Correlation 계수를 구한 후 전처리 과정을 거친 초반의 데이터셋을 평가에 사용.  
![이미지10](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC11.png)
______________
### 후처리
### Clustering 및 군집별 학습/예측
- K-means를 활용한 군집화 진행 후, 비슷한 특성을 가진 각 군집에 대해 linear regression을 별개로 진행하여
발전된 결과 도출을 도모.
- 17년도 1분기 데이터에 대한 클러스터링 진행, 타 시기의 데이터셋에 대해
같은 상권코드를 가진 데이터에 같은 label을 부여.
  * 상권 코드가 같은 상권은 같은 클러스터를 가질 것이라 판단

 
### 과정 
([코드](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/codes/clustering.ipynb))
- MinMax Scaling 진행
- 여러 k 값에 대한 K-Means clustering 후, 군집 평가 지표인 silhouette score를 통해 k 결정
- 학습 및 평가
- 클러스터링 라벨 별 데이터셋 각각에, 앞서 진행한 dimension reduction 시도 및 평가
  * VIF, p-value, PCA
- label 별 데이터셋 각각 매출 최종 예측  


__________________
- DBSCAN을 사용해 실루엣스코어 측정
- 그 값이 두 번째로 높으며 가장 적절히 나뉜 군집화 결과 채택
  * 첫 번째로 값이 높은 군집화의 경우, 1089 개 데이터를 (1088 / 1) 로 군집화한 경우이기에, 무의미한 군집화로 판단
![이미지11](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC12.png)


- Agglomerative Clustering 
(n_clusters=2,linkage="ward")
- 같은 상권코드를 가지는 데이터에 같은 label을 부여,
각 군집별로 (2개의 클러스터, label = 0 / label = 1) 
dimension reduction 및 평가 등 이후 과정 진행  


- Label == 0 인 dataset (클러스터) 에 대해 학습 및 평가를 진행해본 결과, ([코드](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/codes/linear%20regression%20with%20cluster0.ipynb))
MAE는 다소 증가하였으나 RMSE 감소 및 R2 증가 결과를 도출하였다.
![이미지12](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC13.png)


- 앞서 사용하였던 dimension reduction 방법론을 적용한 결과, 평가 결과에 있어 다시 한 번 더 나은 결과를 도출하지 못했다.
- Dimension reduction을 적용하지 않은 label == 0 의 데이터셋에 대한 최종 예측 결과, 
![이미지14](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC15.png)


- 오차값이 소폭 증가하였으나 R2가 상당히 큰 폭으로 증가한 결과물을 도출할 수 있었다.  
- Label == 1 인 데이터셋에 대해 같은 과정을 반복한 결과, ([코드](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/codes/linear%20regression%20with%20cluster1.ipynb))


(p-value 이용 변수 제거 후 VIF 이용 변수 제거) 실험이 평가 성능을 증가시켜 이를 적용한 데이터셋에 대해 최종 예측을 진행.
![이미지15](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC16.png)


- 그 결과, 오차값이 다소 감소하였으나 R2가 상당히 큰 폭으로 감소하는 결과를 도출하였다.
___________________
### 기대 효과와 의의 및 한계점, 추후 개선 방안
________________
### 기대 효과
- 기존에 존재하는 위험도 예측 서비스를 넘어, 전반적인 예상 매출액을 알려줄 수 있는 서비스 제공  
### 의의 및 한계점 1
- Label로 0을 가진 특성의 데이터들에 대해서, 예측을 준수하게 해낼 수 있는 모델 구축에 성공
- Lable로 1을 가진 특성의 데이터들에 대해서, 동일 모델이 예측을 잘 해내지 못함
### 한계점 2 및 추후 개선 방안
- 사용한 데이터셋의 feature의 개수가 많아, 각 클러스터에 대한
특징 파악에 실패했다.
  * 시각화를 통해 직관적으로 이를 파악해본 결과,
푸른색 점으로 표시된 cluster 0의 경우
강서구 등을 포함한 주거단지의 경우가 많았고
붉은색의 점으로 표시된 cluster 1의 경우
강남구 등을 포함한 상가/직장단지의 경우가 많았다.
![이미지15](https://github.com/sukkykim/2022_Seoultech_DataMining_team7/blob/main/img/%EA%B7%B8%EB%A6%BC18.png)
- 추후 연구를 통해, 해당 클러스터들이 가지는 특성을
더 자세히 파악할 수 있다면, 소비자 맞춤 예측 정보를 제공할 수 있으리라 생각된다.
