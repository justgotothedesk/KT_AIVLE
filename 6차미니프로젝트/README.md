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
