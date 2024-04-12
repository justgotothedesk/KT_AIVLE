# 목표

폭염을 대비하기 위해서 건물 옥상에 푸른색의 페인트를 바르는 것을 쿨루프라고 한다.

인공위성 데이터를 통해서 쿨루프를 찾아내는 Object Detection 모델을 만드는 것이 목표이다.

# 도매인 이해

데이터가 사진으로 시각 데이터만 존재하기 때문에 따로 신경쓸 요인은 없는 듯하다.

# 데이터 전처리

- YAML 파일을 활용하여 train, val 데이터셋의 경로와 label의 종류를 지정해주었다.
- 그 외에도 추가적으로 cool roof, generic roof에 해당하는 데이터들을 수집하여 라벨링해주었다.
- 추가적인 자료 2100개를 7:2:1로 train, val, test 데이터셋으로 설정하여 roboflow에 등록해주었다.

→ Roboflow의 API를 통해서 빠르게 colab으로 데이터를 옮길 수 있다는 장점이 있음

# 모델링

- Roboflow를 사용하여 자동으로 데이터 증강을 해주고 resizing을 해서 인공지능 모델을 설계했다
- 초기에는 바운딩 박스를 무분별하게 판단하여 정확도가 매우 낮고 가독성이 떨어지는 결과가 발생
    
    ![스크린샷 2024-04-12 오후 3 33 11](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/bf1d5fe8-25cc-46a3-b927-d241d962b312)
    
- 이를 보완하기 위해서 IoU와 confidence score를 수정
- 모델은 총 두 가지로 Nontransfer learning model과 Transfer learning model(YOLOv8)를 비교하였음

# 결과

- Nontransfer learning model
    - 건물에 대해 비교적 Bounding Box를 잘 찾는 것을 확인할 수가 있음
        
        <img width="614" alt="스크린샷 2024-04-12 오후 3 33 50" src="https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/d4a1eff9-7214-4dc9-96c0-f41576c6916d">
        
- Transfer learning model
    - 반면, Bounding box를 아예 잡지 못하는 것을 확인할 수 있었음
        
        ![스크린샷 2024-04-12 오후 3 33 59](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/ade8ee7d-ef7f-4e63-9d13-09985106d68d)
        
    - 아마, 위성 사진에 대해 훈련이 되어있지 않기 때문에 성능이 좋지 않은 것으로 생각됨
    
    → 따라서 .tune() 메소드를 활용하여 하이퍼파라미터 튜닝을 하기로 함. 이 과정에서 성능이 가장 좋은 모델을 best model로 선정하여 학습 및 예측을 진행시킴
    
    <img width="1135" alt="스크린샷 2024-04-12 오후 3 34 37" src="https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/7cc947aa-72e3-4991-b1de-9025e15f3b76">
    
    - 그 결과, Detection은 물론이고 Classification도 훌륭하게 해내는 것을 확인할 수 있었음
    
    # 타팀 결과
    
- 라벨링을 일일이 하는 것이 너무 귀찮다. 따라서 이 마저도 자동화하는 하는 것이 좋아보인다. 심지어 자동화를 했을 때에 결과도 나쁘지 않다.
- 데이터 증강 하이퍼파라미터도 학습을 하여 최적의 성능을 내는 인자 값을 찾았다. 결과값은 WandB라는 툴을 사용해서 시각화하였다.
- YOLOv8 nano보다는 YOLOv8 small 모델을 사용했을 때가 성능이 더 좋았다.
- 위성 사진에 ‘ㅇㅇ아파트’와 같이 글자가 있는 경우에 인식을 잘 못하는 문제가 많았다.
- 모델 중에 사이즈가 제일 큰 YOLOv8x를 사용했는데도 불구하고 성능이 가장 좋지는 않았다.
- 흰색과 초록색 지붕은 인식을 잘 하지만, 검은색 지붕은 인식을 아예 못하는 경우가 많았다.
- 위성 사진이 기울어져있으면 인식을 잘 못하는 경우가 많았다.
