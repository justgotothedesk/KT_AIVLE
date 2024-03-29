# 도매인 이해

인간 행동 인식은 다양한 센서를 활용하여 사람의 모션에 관련된 정보를 수집하고 해석하여 행동을 인식하는 기술

스마트 워치를 그 예로 들 수 있는데, 인간의 행동을 인식하고 비상 상황임을 판단할 수 있다

근데 해당 행동이 비상 상황이 아니라면 ? → 잘못된 신고 .. 따라서, 성능을 향상시켜서 정확도를 올리자

목표 : 스마트폰 기반의 센서 데이터를 활용해 동작을 분류하는 모델을 완성하자

# 데이터 분석

- 센서를 바탕으로 사용자의 행동을 예측하자
- 가속도 센서, 자이로스코프 센서 등에서부터 푸에리에 변환을 통해 전처리한 데이터가 X 데이터임
- tBodyAcc(가속도 센서) - mean() - X (변수는 Sensor, Aggregation, Axis 순으로 이루어져 있다)
- fBodyGyro(자이로 센서) - std() - Y
- 가속도는 Axis 방향으로 이동하는 것이고 자이로는 Axis 방향으로 회전하는 것이다
- X 데이터가 561개로 너무 많다 .. 모든 feature가 모델링이 필요할까? → 변수 중요도를 구하여 상위 N개의 feature를 구하자
- Label에서 정적인 동작과 동적인 동작으로 나누어서 모델을 2개 나누는 것은 어떨까?
- 데이터의 결측치가 존재하지 않음
- RandomForest의 결과, 센서 중요도가 가장 높은 것은 0.033213으로 tGravityAcc-mean()-X
- RandomForest의 결과, 센서 중요도가 가장 낮은 것은 0.000037으로 tBodyAccJerkMag-max()
- sensor에서 가장 중요도가 높은 것은 tGravityAcc

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e81b6490-64f0-4868-94c8-f9c1978f04a3/a0a68b74-257c-4fa5-9ad2-d66981e59245/Untitled.png)

- sensor + agg에서 가장 중요도가 높은 것은 tGravityAcc-arCoeff()

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e81b6490-64f0-4868-94c8-f9c1978f04a3/064baaa0-42b7-41ee-9066-017291ca2356/Untitled.png)

# 데이터 전처리

1. Label에서 정적인 동작과 동적인 동작으로 나누자(여기서 배운 점, .copy()를 안쓰고 대입할 경우, 해당 객체를 가르키므로 새로운 column 값이 그대로 추가된다.)
    1. 값 0 : STANDING, SITTING, LAYING
    2. 값 1 : WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS
    
    → 동적, 정적인 동작을 예측하는데 중요한 변수 5개
    
    tBodyGyroJerk-iqr()-Z    0.059999
    fBodyAccJerk-std()-X    0.049973
    fBodyAcc-entropy()-X    0.049795
    fBodyAccJerk-max()-X    0.040065
    tBodyAccJerk-entropy()-X    0.039923
    
2. Label을 개별 행동 여부로 구분하자
    1. is_standing을 예측하는데 중요한 변수 5개
        
        tGravityAcc-max()-Y    0.066655
        angle(Y,gravityMean)    0.063110
        tGravityAcc-mean()-Y    0.047829
        tGravityAcc-min()-Y    0.042679
        angle(Z,gravityMean)    0.029686
        
    2. is_sitting을 예측하는데 중요한 변수 5개
        
        angle(Y,gravityMean)    0.051174
        tGravityAcc-max()-Y    0.043982
        tGravityAcc-min()-Y    0.041279
        angle(X,gravityMean)    0.040435
        tGravityAcc-mean()-Y    0.027529
        
    3. is_laying을 예측하는데 중요한 변수 5개
        
        tGravityAcc-mean()-X    0.108937
        tGravityAcc-max()-X    0.104807
        tGravityAcc-energy()-X    0.076181
        angle(X,gravityMean)    0.065231
        tGravityAcc-energy()-Y    0.064073
        
    4. is_walking을 예측하는데 중요한 변수 5개
        
        fBodyAccMag-std()    0.032203
        fBodyAcc-bandsEnergy()-1,16    0.021938
        fBodyAcc-max()-X    0.020874
        tBodyAcc-correlation()-X,Y    0.020669
        tGravityAcc-arCoeff()-X,2    0.019141
        
    5. is_walking_upstairs을 예측하는데 중요한 변수 5개
        
        tGravityAcc-min()-Y    0.033113
        tGravityAcc-arCoeff()-Z,2    0.026411
        tGravityAcc-arCoeff()-Z,3    0.025354
        fBodyGyro-maxInds-Z    0.025266
        tGravityAcc-arCoeff()-Z,1    0.023002
        
    6. walking_downstairs을 예측하는데 중요한 변수 5개
        
        tBodyAccMag-std()    0.090652
        tGravityAccMag-std()    0.054868
        tBodyAccMag-mad()    0.043855
        fBodyAccMag-energy()    0.037850
        tGravityAccMag-mad()    0.035167
        
3. 561개의 features를 모두 사용하지 말고, 상위 n개만 사용
4. 10, 20, 50, 100, 200, 300개로 선정하고 다섯 개의 모델로 비교
    1. RandomForestClassifier : 0.9847068819031436
    2. SVC : 0.9813084112149533
    3. KNeighborsClassifier : 0.9634664401019541
    4. LogisticRegression : 0.983857264231096
    5. GradientBoostingClassifier : 0.994052676295667
    
    대부분 200개에서 성능이 가장 좋고 그 이후로부터는 성능이 같거나 오히려 떨어지는 모습을 보여줌
