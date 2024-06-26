# 도매인 이해

미세 먼지의 종류는 크게 초미세먼지, 미세먼지로 두 가지가 있음.

미세먼지를 아예 없애는 것은 불가능하기에 이를 예방하기 위해서 예보하는 것이 필요함. → 정확도가 생명

# 데이터 분석

- 학습 데이터
    - air_2022.csv : 2022년 미세먼지 데이터
    - weather_2022.csv : 2022년 날씨 데이터
- 테스트 데이터
    - air_2023.csv : 2023년 미세먼지 데이터
    - weather_2023.csv : 2023년 날씨 데이터
- 미세먼지에 영향을 미치는 기상인자 : 온도, 습도, 풍향, 풍속, 강수량
- 미세먼지에 영향을 미치는 대기오염물질 : CO, NO2, SO2
- 위 두 유형의 변수들을 제외하고는 대부분 결측치 비율이 절반이 넘음 → 그대로 다 drop하는 것도 좋을듯

# 데이터 전처리

1. air, weather에서 결측치 비율이 절반 이상인 데이터들을 drop(근데, 강수량은 꽤나 중요한 데이터 같은데, 결측치가 너무 많았음. 따라서, 결측치인 값은 비가 안온 날이라고 판단하여, 0으로 채움)
    1. 결측치 비율이 절반 이하이지만 꽤 많은 변수 : 일조(hr), 일사(MJ/m2), 운형(운형약어), 최저운고(100m )
    2. 일조 : 일조량이 많을수록 자외선(UV)이 증가하고 대기 중의 먼지와 미세먼지를 제거하는 데 도움이 될 수 있음. 따라서 보다 맑고 청정한 날씨에서는 미세먼지 농도가 낮을 가능성이 높음. → 가져간다
    3. 일사 : 미세먼지와 일사량 사이에는 일반적으로 역상관 관계가 있음. 일사량이 늘어날수록 자외선(UV)이 증가하게 되면서, 자외선이 미세먼지를 살균하는 효과가 있기 때문임. 따라서 보다 맑고 밝은 날씨일수록 미세먼지 농도가 낮을 가능성이 있음. → 가져간다
    4. 운형(운향약어) : 미세먼지와 운향(운향약어) 사이에는 직접적인 관련이 없어 보임. 운향은 바람이 불어오는 방향을 나타내는 지표이며, 미세먼지는 대기 중에 분산된 미세한 입자들을 가리킴. 따라서 운향이 미세먼지 농도에 직접적인 영향을 미치지 않음. 더군다나, 풍향이라는 더 좋은 데이터가 있음. → 버린다
    5. 최저운고(100m) : 미세먼지와 최저운고(100m) 사이에는 직접적인 관련이 없음. 최저운고(100m)는 대기 중의 구름이나 안개 등이 지표면으로부터 상승한 높이를 의미하지만, 미세먼지는 대기 중에 분산된 미세한 입자들을 의미함. 이 두 변수는 서로 다른 기상 현상을 나타내기 때문에 직접적인 연관성이 없음. → 버린다
2. 1에서 조금 더 알아본 데이터 중에 상관이 없어보이는 데이터들을 drop
3. 각 관측일시 데이터의 형태가 다르기 때문에 이를 통일시키고 merge
4. String형인 지역, 망, 측정소명, 주소, 지점명 데이터 인코딩
5. 결측치 비율이 상대적으로 적은 SO2, CO, O3, NO2, PM10, PM25 데이터들은 최빈값으로 채우고, 나머지 일조(hr), 일사(MJ/m2)은 평균값으로 채움
6. shift 메서드를 통해 전날 미세먼지 column 추가
    1. 2022-01-01 날짜의 전날 미세먼지는 데이터가 없어서 채울 수가 없음
    2. 2023-01-01 날짜의 전날 미세먼지는 2022-12-31의 데이터로 채움
7. shift 메서드를 통해 한 시간 후의 미세먼저 column 추가
    1. 2022-12-31 23:00 날짜의 한 시간 후의 미세먼지는 2023-01-01 00:00의 데이터로 채움
    2. 2023-12-31 23:00 날짜의 한 시간 후의 미세먼지는 데이터가 없어서 채울 수 없음
8. 6과 7에서 이전 미세먼지 값을 채울 수 없는 결측치 row는 모조리 삭제

# 모델링

1. LinearRegression - MSE: 47.70150373074605, R-squared Score: 0.9574
2. RandomForestRegression - MSE: 70.39589195816187, R-squared Score: 0.93713
3. GradientBoosting - MSE: 85.05735179148051, R-squared Score: 0.92404
4. VotingRegressor(KNN, LR, Lasso, Ridge, Elastic_Net, DTR, RFR, GBR) - MSE: 55.73796390843093, R-squared Score: 0.95022

→ 가장 결과가 좋은 모델은 LinearRegression

모든 모델에서 가장 중요도가 가장 높은건 NO2로 밝혀짐(PM 10이 너무 압도적으로 높게 나와서 제거함)

# 결과 이해

![Untitled](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/b83c9911-998a-4596-b0e8-c8b0ae385db8)

PM 25, CO, Month도 중요도가 높음

12, 1월에 PM 10값이 상당히 높은 것을 알 수가 있음 → 미세먼지와 계절은 영향이 있다.

겨울철에 미세 먼지 농도가 높음. 그리고 겨울철에 CO의 농도 역시 높음

CO는 연소 과정에서 많이 발생하는데, 아마 난방과 관련이 있을 것으로 추정됨. 인위적인 결과

### 결론 : 인위적인 요소가 많이 포함되어있으므로, 기상 변수 데이터만으로는 미세먼지를 예측하기가 어렵다.

# 타팀 결과

데이터의 결측치가 너무 많았다. → 결측치의 비율이 적은 것은 선형보간법을 사용하여 결측치를 채웠다.
