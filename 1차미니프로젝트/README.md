# 미션 1 주제 : 버스 노선 추가가 필요한 서울 시 내 자치구를 선정하자

통신사의 데이터를 통해서 서울 유동 인구(서울 생활 이동 데이터)를 알 수가 있음.

주말에 명동, 홍대에 이동하는 사람들은 뭘까 ? → 아마 서비스업 종사자일 것이다.

서울은 몇 개의 구 ? → 25개

인구가 가장 많은 구 ? 적은 구 ? → 송파구, 중구

면적이 가장 큰 구 ? 작은 구 ? → 서초구, 중구

도로 길이의 합이 가장 긴 구 ? → 성북구

도로 면적 합이 가장 큰 구 ? → 강남구

도로 길이에 따라서 인도가 활성화 되거나 상업 지구가 활성화될 수 있다.

서울 지선+간선 버스 노선의 수 ? → 375개

서울 소속 광역버스 노선의 수 ? → 11개

야간 노선의 운행 시작 시간은 ? → 자정

KT 광화문 지사의 버스정류장 번호는 ? → 01118

시 군구 코드에서 세번째 네번째 숫자가 01 ~ 25인 것이 서울시 버스

## 1-1 파일

![1](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/af911ba9-2e65-4c28-a2bc-9574da6dbbfa)

1. 중구가 인구가 제일 적은 구인 만큼 정류장의 수가 가장 작았다. 하지만, 송파구가 가장 인구가 많다고 해서 정류장 수가 제일 많은 것도 아니였다.

2. 가장 많은 정류장을 가진 구는 서초구였다. 서초구가 서울에서 면적이 제일 큰 구이다. 면적과 정류장 수는 비례하는 것으로 봐도 좋겠다.

3. 강남구가 도로 면적이 가장 넓으나 정류장의 수가 제일 많지는 않았다. 아마, 자가용 및 택시를 이용하는 비중이 높아서이지 않을까 생각이 든다.

**결론** : 동대문구에 정류장을 추가로 설치하는 것이 좋아보인다. 왜냐하면 정류장 개수를 기준으로 내림차순했을 때, 동대문구는 뒤에서 3번째로 상당히 적다. 하지만, 승차평균승객수, 하차평균승객수, 승차총승객수, 하차총승객수는 각각 2, 2, 8, 8번째로 상위권에 위치하고 있기 때문이다.

## 1-2 파일

![2](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/59e93786-c116-404d-b903-1417d73b4e80)

1. 이동인구의 수는 1.5명 정도가 압도적으로 많았다. 그 다음은 6명 정도였다.

2. 이동인구의 수가 가장 많은 요일은 월요일이었다. 이것을 보고 관광 목적보다 출퇴근 목적으로 서울 이동이 많다고 생각할 수 있다. 하지만, 월요일과 금토일의 이동인구 차이는 20만 정도였다. 24년 1월달에 월요일이 5번, 금토요일이 4번 있는 것을 감안하면, 월요일에는 1일 당 98000명이 이동한 셈이고, 금토요일은 1일 당 10만명이 넘는다. 따라서 관광 목적이 좀 더 비율이 높은 것을 알 수가 있다.

3. 새벽 5시 이내에 이동하는 심야 이동 횟수는 2868922이며, 토요일이 가장 많았다.

4. 유출이 제일 많은 구는: 강남구
     유출이 제일 적은 구는: 금천구

5. 유입이 제일 많은 구는: 강남구
     유입이 제일 적은 구는: 금천구

6. 그래프를 그려보았을 때, 평균 이동 시간은 거의 다 비슷비슷해서 의미가 없어보인다.

**결론** : 동대문구에 정류장을 추가로 설치하는 것이 좋아보이는 의견은 좋지 않아보인다. 왜냐하면, 생각보다 동대문구를 가는 인원이 매우 적기 때문이다. 또한 출발 시군구를 기준으로 작성한 이동 인구 합계와 도착 시군구를 기준으로 작성한 이동 인구 합계는 큰 차이가 없기 때문에 타당성이 있다. 오히려 강남에 정류장을 설치해야하지 않나 생각이 든다. 

## 1-3 파일

![3](https://github.com/justgotothedesk/KT_AIVLE/assets/114928709/39548980-7e38-42d2-83c7-943cfe64069e)

1. 서울에 거주하는 남녀의 비율은 상당히 동일하다. (여자가 조금 더 높긴함)

2. 해당 자료만을 사용해서는 의미 있는 해석은 어려울 듯하다.

**결론** : 딱히 없음

## 1-4 파일

1. 직업 종사 종류 중에서 유동 인구에 영향을 주는 데이터는 ['한식 일반 음식점업', '두발미용업', '커피전문점', '기타주점업', '일반 교과 학원', '한식 육류요리 전문점']라고 생각했음.

2. 따라서 해당 데이터를 기준으로 각 자치구에 종사하는 인구 수를 합산함.

3. 여기서 문제가 발생함. 데이터가 문자열이다보니, 합산시 글자가 더해지는 현상이 발생.

4. int형으로 변경이 안되는 문제로 인해서 더 이상 진행을 못했음.

5. 하지만, 데이터를 보았을 때, 양천, 금천, 강서구에서 종사자 수가 많은 것을 확인할 수 있음.

**결론** : 강서구를 제외하고 양천, 금천구에는 정류장 숫자가 적음. 아마 직업 종사와 유동 인구는 큰 관련이 없는 것으로 예상됨. 혹은 해당 직업군을 잘못 선정한 원인인듯함.

# 미션 2 주제 : 서울시 공유자전거 '따릉이' 수요 예측 프로젝트

## 2-1 파일

### 데이터 전처리

결측치를 가진 column은 PM10, PM2, Ozone.

 → 5827 중에 100개 정도이기 때문에 최빈값으로 결측치를 채웠음

### 데이터 그래프 분석

평균 날짜와 시간은 크게 의미가 없음.
PM10, PM2, Ozone 작을수록 대여 수가 많음. 왼쪽으로 치우친 정규분포곡선임.
temperature가 25도 정도일 때 대여 수가 많음.
당연히 비가 올 때보다 안올 때, 대여 수가 압도적으로 많음.
풍속은 왼쪽으로 치우친 정규분포곡선임.
습도는 70프로일 때, 대여 수가 많음.
시정(가시성)은 2000 정도일 때(최대치), 가장 많음.
대여를 안 한 비율이 훨씬 크다.

## 2-2 파일

### 가설 설정

- 귀무가설 1: 온도와 따릉이 대여량간에는 관계가 없다.
- 대립가설 1: 온도와 따릉이 대여량간에는 관계가 있다.
- 귀무가설 2: 강우 여부와 따릉이 대여량간에는 관계가 없다.
- 대립가설 2: 강우 여부와 따릉이 대여량간에는 관계가 있다.
- 귀무가설 3: 오존 수치와 따릉이 대여량간에는 관계가 없다.
- 대립가설 3: 오존 수치와 따릉이 대여량간에는 관계가 있다.
- 귀무가설 4: 습도와 따릉이 대여량간에는 관계가 없다.
- 대립가설 4: 습도와 따릉이 대여량간에는 관계가 있다.
- 귀무가설 5: 미세먼지와 따릉이 대여량간에는 관계가 없다.
- 대립가설 5: 미세먼지와 따릉이 대여량간에는 관계가 있다.

### 데이터 상관분석

강수 여부와 대여량 간의 t-검정 : 

t-statistic: -21.389614151911022
p-value: 8.86239184041254e-98

'hour', 'temperature', 'windspeed', 'humidity', 'visibility', 'ozone', 'PM10', 'PM2.5'와 대여량 간의 상관분석 :

hour과 'count'간의 상관관계 : 0.586435028391939
humidity과 'count'간의 상관관계 : -0.4789554265904134
ozone과 'count'간의 상관관계 : 0.31209082644233227
temperature과 'count'간의 상관관계 : 0.2771692363089791
visibility과 'count'간의 상관관계 : 0.2658277832748878
windspeed과 'count'간의 상관관계 : 0.22372189900674885
PM2.5과 'count'간의 상관관계 : 0.036802074024602144
PM10과 'count'간의 상관관계 : 0.02816570231012749

### 가설 채택

- 대립가설 1: 온도와 따릉이 대여량간에는 관계가 있다.
- 대립가설 2: 강우 여부와 따릉이 대여량간에는 관계가 있다.
- 대립가설 3: 오존 수치와 따릉이 대여량간에는 관계가 있다.
- 대립가설 4: 습도와 따릉이 대여량간에는 관계가 있다.
- 귀무가설 5: 미세먼지와 따릉이 대여량간에는 관계가 없다.

### 타팀 가설

5월, 9월에 대여량이 매우 낮았다. → 5월, 9월은 특히나 미세 먼지의 농도가 높은 달이었음.

가장 대여량이 많은 날은 9월 2일이었음. → 이는 월드컵교가 개통하는 날이었음.

평일의 대여량이 주말보다 많음.

계절 중에서도 가을이 특히나 대여량이 많음. → 가을은 미세 먼지의 농도가 낮은 계절이기 때문으로 추정.

오존 농도는 질소산화물과 연관이 있는데, 출퇴근 시간에 많이 사용하는 자가용으로 인해서 다음날의 14-17시에 영향을 많이 끼칠 것이다.

또한, 오존은 자외선량과도 연관이 있기 때문에, 자외선량이 높은 여름철에는 오존량이 많이 증가하였다.
