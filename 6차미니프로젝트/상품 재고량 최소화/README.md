# 목표

우리의 매장 번호는 44번이다

유통 매장에서 상품별 재고문제 해결이 필요하다

매장에는 항상 과재고와 재고 부족 문제가 존재한다

넉넉한 재고는 결품 문제를 해결할 수 있으나, 과도한 재고는 자금이 묶이게 되고 회사 현금 흐름에 문제가 발생한다(기업에서의 재고는 자산이지만, 적당한 것이 중요)

따라서, 수요량을 예측하는 모델을 만들어서 재고량을 관리하자

3개의 핵심 상품은 아래와 같다

| Product_ID | Product_Code | SubCategory | Category |
| --- | --- | --- | --- |
| 3(노재훈, 박유빈, 조성민) | DB001 | Beverage | Drink |
| 12(박상현, 신진철, 이석준) | GA001 | Milk | Food |
| 42(배성준, 이준석) | FM001 | Agricultural products | Grocery |

# 도매인 이해

마트를 이용하는 고객 관점에서 생각하는 것이 중요하다

휘발유 가격이 높으면 대형마트를 방문하는 고객의 수가 줄어든다

여기서의 휘발유 가격은 절대적으로 비교하는 것이 아니라, 상대적으로 비교해야한다 → 고객의 심리를 잘 이해하는 것이 중요(소비 심리학)

같은 지역 매장의 고객 방문객 수가 증가하면 해당 매장의 방문객 수 역시 증가하지 않을까?

# 데이터 분석

- oil_price_train : 휘발유 가격
    - 유일하게 결측치가 존재하는 데이터셋임
    - 유가의 경우 전날의 값에 영향을 받기 때문에 선형보간법을 응용하면 괜찮을 듯함
    - 하지만 그래프 결과, 딱히 상관이 없는 것으로 판단됨
    - 상관관계는 -0.24316879234321337
    
    ![Untitled](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/69d745f3-cef1-4019-ab29-37278b5110f2)

    
- products : 상품 목록
    - 3번(DB001, Beverage, Drink)
        - DB001의 데이터 Decomposition
        
        ![1](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/013e2d8d-3aaa-4856-8732-0fc84b2d9e56)

        
        - SubCategory Beverage 상품은  DB001 하나만 존재
        - Category가 Drink인 SubCategory는 Beverage, Wine이 존재
        - 이 역시도 SubCategory가 각각, DB001, DA001 하나만 존재
        - 판매량 순위는 DB001(Beverage) → DA001(Wine)
    - 12번(GA001, Milk, Food)
        - GA001의 데이터 Decomposition
        
        ![2](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/adad9868-2cb1-4e87-b6d3-e4c682023deb)

        
        - SubCategory milk 상품은 GA001 하나만 존재
        - Category가 Food인 SubCategory는 Bakery, Frozen, Yogurt, Prepared가 존재
        - 이 역시도 SubCategory가 각각 FB001, FF001, FM001, FP001 하나만 존재
        - 판매량 순위는 GA001(Milk) → FM001(Yogurt) → FF001(Frozen) → FB001(BAKERY) → FP001(Prepared)
    - 42번(FM001, Agricultural products, Grocery)
        - FM001의 데이터 Decomposition
        
        ![3](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/d009c823-b768-4520-b6aa-96570d2d4829)

        
        - FM001 하나만 존재
        - 겨울철에 판매량이 매우 떨어지는 것을 확인할 수 있었다. → 아마 농산품이기 때문에 공급 역시 떨어지는 것이 원인이 아닐까 생각이 듦
    - 세 가지 데이터 비교(GA001 → DB001 → FM001)
    - 대부분 토, 일요일에 판매량이 많았음. 의외로 금요일이 적고 월요일이 3번째로 많았으며, 수요일도 꽤나 많음. 가장 적은 날은 목요일
    
    ![4](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/5ba6c716-3293-4477-aaba-963b0bea7bbc)

    
- orders_train : 매장별 구매량
    - 방문 고객수와 상품 판매량은 관계가 있는 것으로 보여짐
    - 상관관계는 0.6698874743950217
    
    ![5](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/81238738-92ef-4a84-9927-1b558b2fd827)

    
- sales_train : 매장별 상품 상세 구매량
- stores : 매장별 위치 및 타입

# Feature 선정

- 3번
    - 추수감사절 다음 날은 블랙 프라이데이임
    - 구매에 대해 큰 영향을 끼칠 것 같아서 블랙 프라이데이의 유무와 공휴일의 유무를 추가하였음
    - inner 조인을 사용하니 사라지는 데이터가 많아서 left 조인 후, 선형보간법을 이용하여 결측치를 채움
- 12번(Milk)
    - 계절 변수를 추가하자
        - 여름철일 경우에 판매량이 감소하는 것을 확인할 수 있었음
        - 이는 유제품의 특성상, 부패 우려 때문일 것이라고 추정
        - 따라서, Date feature를 토대로 Spring(3, 4, 5), Summer(6, 7, 8), Autumn(9, 10, 11), Winter(12, 1, 2)로 계절을 나누는 것이 좋을 듯하다
    - 휘발유 값을 변수로 추가하자
        - 전날 휘발유 값과 판매량을 상관관계 분석한 결과, 수치상으로는 큰 차이가 없는 것을 확인할 수 있었음
        - 허나, 모든 매장의 판매량을 기준으로 분석한 결과였고, 미국이라는 거대한 대륙 특성상 주마다 휘발유 값이 다를 수 있다고 생각함
        - 미국은 각 주마다 세금 정책, 공급과 수요의 차이, 지역적 요인 등에 따라 휘발유 가격이 상이할 수 있기에 잘못된 접근 방법이었다고 생각함
        - 따라서, 어쩌면 휘발유 값이 영향이 있을 것
        - 이론상, 휘발유 가격과 물가 간의 관계는 일반적으로 비례하는 경향이 있음
        - 휘발유는 교통수단을 이용하는 데 필수적이며, 경제적 활동에 중요한 역할을 하기 때문에 휘발유 가격이 상승하면 이는 소비자들의 생활비 부담을 늘릴 수 있고, 이는 물가 상승으로 이어질 수 있음
        - 또한, 이는 운송 및 운송에 따른 비용이 증가하고, 생산 및 유통 과정에서 소비자에게 전가될 수 있음
    - 주(State)를 변수로 추가하자
        - 미국은 평균적으로 나이가 어린 인구층의 우유 소비량 역시 많음
        - 그리고 주마다 평균 연령의 차이가 많이 때문에 아이가 많은 주, 노인이 많은 주로 나뉘어져 있음
        - 실제로 주마다 우유 소비량이 유의하게 차이나는 것을 확인할 수 있음
        - 미네소타 주(target인 44번 가게가 위치)는 미국 내에서 낙농업 규모가 큰 곳
            
        ![12](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/25d8c0d6-9ee4-4e18-b815-84b881965426)

            
        - 근데 이 접근 방법은 잘못되었음. 주마다 가게 분포가 동일한 것이 아니라 미네소타가 제일 많기 때문에 총 합이 아닌 평균치로 분석해야함
        - 근데 그래도 미네소타가 가장 우유 소비량이 높다
        
        ![13](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/9d84d789-d6e1-4fcc-9b41-f33b19c32ee2)

        
- 42번
    - 휘발유 값이 감소할 때, 상품 구매량 역시 감소하고 휘발유 값이 증가할 때, 상품 구매량 역시 증가한다.

# 간단한 모델링

- 3번
    - CNN 모델의 R2 Score가 0.77로 꽤나 높다
- 12번(Milk)
    - 기본 모델링
        - Mean Squared Error (MSE): 0.07658388443452803
        - Mean Absolute Percentage Error (MAPE): 75.50862909994768
        - R2 Score: -9.44594265960134
    - LSTM
        - Mean Squared Error (MSE): 0.006456208867175978
        - Mean Absolute Percentage Error (MAPE): 20.464153273353194
        - R2 Score: 0.09704075722708871
    - CNN
        - Mean Squared Error (MSE): 0.004787334900434998
        - Mean Absolute Percentage Error (MAPE): 22.352526703178167
        - R2 Score: 0.3304478858212736
    
    → CNN 모델이 성능이 가장 우수하다
    
- 42번
    - 12번과 결과가 유사하다

# Feature 재선정

1월 1일은 신년이기 때문에 1월 1일의 판매량은 없음

- 3번(BAKERY)
    - Qty, WTI_Price, CustomerCount, Price, Weekend(주말 여부), Holiday(공휴일 여부), B_Friday(블랙프라이데이 여부), Total_Sales, target
- 12번(Milk)
    - Qty, WTI_Price, Year, Month, Day, Spring, Summer, Autumn, Winter, target
- 42번
    - Qty, CustomerCount, Year, Month, Day, Day_of_week, Day_of_week2, isWinter, isSummer, target

# 모델링

- 이전 모델링을 토대로 생각해보았을 때, 문제는 44번의 3, 12, 42번의 상품만 예측한다는 것
- test 데이터가 25142개인데, 모두 44번 가게가 아니라는 점이 신경쓰인다
- 그러면, 주(State) 데이터로 학습을 할 수 없는 것 아닌가. 그리고 학습할 데이터도 상당히 작아지기 때문에 3, 12, 42번의 데이터를 나누어서 학습하는 게 아니라, 동일한 columns을 지정하여 합쳐서 학습하면 더 성능이 좋아질 듯하다. → But 학습 결과 매우 정확도가 낮은 모습
- 3번
    - 노트북 파일 참고
- 12번
    - 노트북 파일 참고
- 42번
    - 노트북 파일 참고