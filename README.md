# 📈 Stock-Price-Prediction

## 목차
1. [🛸 학습용 clone 모델](#1-학습용-clone-모델)
2. [🚀 구현한 모델](#2-구현한-모델)
   - [2-1. 데이터 로딩 및 기본 탐색](#2-1-데이터-로딩-및-기본-탐색)
   - [2-2. 특성 엔지니어링 및 데이터 전처리](#2-2-특성-엔지니어링-및-데이터-전처리)
   - [2-3. 모델 학습 및 평가](#2-3-모델-학습-및-평가)
   - [2-4. 예측 결과 분석 및 인사이트 도출](#2-4-예측-결과-분석-및-인사이트-도출)
   - [2-5. 미래 주가 예측](#2-5-미래-주가-예측)

## 1. 🛸 학습용 clone 모델
출처 - https://github.com/NGYB/Stocks

## 2. 🚀 구현한 모델
### 2-1. 데이터 로딩 및 기본 탐색

<br>

![image](https://github.com/djLee77/Stock-Price-Prediction/assets/117016295/b500a149-b8a7-407c-8fea-53082ab51efb)

- 데이터 불러오기:
```
yf.download: Yahoo Finance에서 주식 데이터를 다운로드합니다.
여기서는 '005930.KS' (삼성전자의 주식 코드)에 해당하는 데이터를
2018년 11월 1일부터 2023년 11월 1일까지 다운로드합니다.
```
- 기본 데이터 탐색:
```
samsung_stock.head(): 데이터프레임의 처음 몇 행을 출력하여 데이터의 개요를 살펴봅니다.
samsung_stock.dtypes: 각 컬럼의 데이터 타입을 출력합니다.
samsung_stock.describe(): 수치형 컬럼들에 대한 요약 통계를 제공합니다.
이는 평균, 표준편차, 최소값, 최대값 등의 정보를 포함합니다.
```
- 종가 데이터 시각화:
```
plt.figure: 시각화의 크기를 설정합니다.
plt.plot: 삼성전자의 종가 데이터를 선 그래프로 그립니다.
plt.title, plt.xlabel, plt.ylabel: 그래프의 제목과 x, y축의 레이블을 설정합니다.
plt.show(): 설정한 그래프를 화면에 표시합니다.
```

### 2-2. 특성 엔지니어링 및 데이터 전처리
- 특성 엔지니어링:
```
새로운 특성 High-Low와 Open-Close를 생성합니다.
이는 각각 하루 중 주가의 최고가와 최저가 간의 차이, 그리고 시가와 종가 간의 차이를 나타냅니다.
이러한 특성들은 주가의 일일 변동성을 측정하는 데 유용할 수 있습니다.
```

- 필요한 특성 선택:
```
모델에 사용할 특성을 선정합니다.
여기서는 'Open', 'High', 'Low', 'Close', 'Volume', 'High-Low', 'Open-Close' 등이 선택됩니다.
```

- 타겟 변수 설정:
```
타겟 변수로서 다음 날의 종가(Close)를 예측하기 위해 Target 컬럼을 생성합니다.
shift(-1) 함수는 각 행의 Close 값을 한 칸씩 위로 이동시켜 다음 날의 종가를 나타내도록 합니다.
```

- 결측치 제거:
```
dropna() 함수를 사용하여 결측치가 있는 행을 제거합니다.
이는 데이터의 정확성을 유지하기 위해 필요한 단계입니다.
```

- 특성과 타겟 분리:
```
데이터를 특성(X)과 타겟(y)으로 분리합니다.
```

- 데이터 분할:
```
train_test_split 함수를 사용하여 데이터를 훈련 세트와 테스트 세트로 나눕니다.
여기서는 데이터의 20%를 테스트 세트로 사용합니다.
```

- 데이터 정규화:
```
StandardScaler를 사용하여 특성 데이터를 정규화합니다.
훈련 데이터에는 fit_transform을, 테스트 데이터에는 transform을 사용합니다.
이렇게 하는 이유는 모델이 테스트 데이터에 대한 정보를 미리 알지 못하게 하기 위함입니다.
```
### 2-3. 모델 학습 및 평가

<br>

![image](https://github.com/djLee77/Stock-Price-Prediction/assets/117016295/ccbc8b6e-d2d9-4d65-a762-0dc37c23a129)

XGBoost 모델을 사용하여 주식 가격을 예측하고, 테스트 데이터에 대한 예측 결과의 RMSE (Root Mean Squared Error) 값을 계산합니다.
또한, 실제 값과 예측 값의 비교를 위한 시각화도 포함되어 있습니다.

- 데이터셋 준비:
```
예측 모델에 사용할 특성(features)과 타겟 변수(target)를 정의합니다.
X는 모델의 입력 특성, y는 예측하려는 타겟 변수(여기서는 'Close' 종가)입니다.
```
- 데이터 정규화:
```
StandardScaler를 사용하여 특성 데이터를 정규화합니다.
```
- 시계열 교차 검증 설정:
```
TimeSeriesSplit을 사용하여 시계열 데이터의 교차 검증을 설정합니다.
시계열 데이터의 경우, 순차적인 정보가 중요하기 때문에
일반적인 교차 검증 방법 대신 시계열에 특화된 방법을 사용합니다.
```
- 훈련 및 검증 세트 준비:
```
교차 검증에서 생성된 각 fold의 훈련 및 검증 인덱스를 저장합니다.
마지막 fold를 훈련 및 검증 세트로 사용합니다.
```
- 모델 정의 및 훈련:
```
XGBRegressor를 사용하여 XGBoost 회귀 모델을 정의하고 훈련합니다.
여기서는 100개의 추정기와 최대 깊이 3, 학습률 0.1을 사용합니다.
```
- 모델 예측 및 평가:
```
개발(검증) 세트에서의 예측 값을 구하고, 실제 값과 비교하여 평균 제곱근 오차(RMSE)를 계산합니다.
```
- 테스트 데이터 준비 및 예측:
```
데이터의 마지막 20%를 테스트 데이터로 가정하고, 이에 대한 예측을 수행합니다.
```
- 결과 시각화:
```
훈련, 검증, 테스트 세트 및 모델 예측 결과를 시각화합니다.
```
- 성능 지표 출력:
```
개발(검증) 세트에 대한 모델의 RMSE를 출력합니다.
주식 가격 예측을 위한 전형적인 머신러닝 워크플로우를 따르며,
시계열 데이터의 특성을 고려한 교차 검증 방식을 채택합니다.
RMSE는 모델의 예측 정확도를 측정하는 데 중요한 지표로 사용됩니다.
```

### 2-4. 예측 결과 분석 및 인사이트 도출

<br>

![image](https://github.com/djLee77/Stock-Price-Prediction/assets/117016295/93a5d5f3-6533-4136-98a9-b1346647cc28)

![image](https://github.com/djLee77/Stock-Price-Prediction/assets/117016295/7c42ed55-5bc3-46ca-8671-acd404b94b14)


실제 주가와 예측 주가 사이의 오차를 계산하고, 이 오차의 평균과 표준 편차를 출력합니다. 
또한, 오차의 분포와 시간에 따른 변화를 시각화하여, 모델의 예측이 어느 정도 일관되게 벗어나는지, 또는 특정 패턴이 있는지를 분석합니다.

이러한 분석을 통해 모델의 성능을 더욱 세밀하게 이해하고, 예측이 언제 정확하고 언제 오차가 큰지에 대한 통찰을 얻을 수 있습니다. 

- 예측 결과와 실제 데이터 비교:
```
comparison_df 데이터프레임을 생성하여 실제 값(y_test)과 예측 값(test_predictions)을 비교합니다.
오차는 실제 값과 예측 값의 차이(Actual - Predicted)로 계산됩니다.
```

-오차 분석:
```
오차의 평균(error_mean)과 표준편차(error_std)를 계산하여
모델의 예측 오차에 대한 일반적인 정보를 제공합니다.
평균 오차는 모델이 얼마나 정확하게 예측하는지, 표준편차는 예측의 일관성을 나타냅니다.
```

- 오차의 분포 시각화:
```
sns.histplot을 사용하여 오차의 분포를 히스토그램으로 시각화합니다.
KDE(Kernel Density Estimate)는 오차의 밀도 추정치를 함께 제공합니다.

시간에 따른 오차를 시각화합니다.
```

- 인사이트 도출:
```
오차 데이터를 분석하여 모델의 성능에 대한 추가적인 인사이트를 도출할 수 있습니다.
예를 들어, 오차가 특정 기간에 집중되어 있는지, 일정한 패턴을 보이는지 분석할 수 있습니다.
이러한 분석은 모델이 특정 상황에서 더 잘 또는 못하는 경향이 있는지 파악하는 데 도움이 됩니다.
```

### 2-5. 미래 주가 예측

![image](https://github.com/djLee77/Stock-Price-Prediction/assets/117016295/52672d3f-06eb-42c7-b87f-a863ebfa01de)

