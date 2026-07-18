# 옷장패션 데이터 정제 보고서

## 1. 데이터 개요
- 행/열: 1,500 × 8
- 주요 컬럼: order_id, customer_age, category, channel, price, quantity, amount, return_amount

## 2. 진단 결과
- 결측: amount 3.4%, price 0.33%
- 이상치(IQR): customer_age(999·0, 5건), quantity(200, 1건), amount(50,000,000, 1건)
- 의심되는 결측 유형: amount는 app 채널에 쏠림 → MAR, price는 소량 결측, 무작위로 보임 -> MACR

## 3. 처리 결정과 근거
| 이슈 | 결정 | 근거 | 한계 |
- amount 결측 | 채널별 중앙값 대체 | app 채널의 결측 비율이 높음 -> MAR 가설 부합 | 채널 외 다른 원인은 검토 안 함 
- price 결측 | 카테고리별 중앙값 대체 | 비율이 낮고 카테고리별 가격대 보존 | 표본 적어 카테고리 통계 불안정 
- customer_age 이상치 (999,0) | NaN 표시 -> 중앙값 대체 | 물리적으로 불가능한 나이, 입력 실수 추정 | 외부 인증 데이터 없음 
- quantity 이상치 (200) | NaN 표시 -> 중앙값 대체 | 일반 소비자 외의 값으로 보임 | 도매 물량인지 확인이 불가능 (도매이면 제거 손실) 
- amount 이상치 (50,000,000) | 유지하고 flag에 표시 | 도매 물량일 가능성이 있음 | 도매 물량인지 세부 검토 안 함  

## 4. 처리 후 검증
- 결측 0건(설계상 NaN 유지가 필요한 컬럼 제외)
- customer_age 범위: 5 ~ 60 (정상)
- amount_outlier 플래그 145건 보존

## 5. 후속 권고
- outlier 추가 검증을 위해, customer_id 단위 과거 이력 확보 및 도매 가능 고객 식별 필요함 