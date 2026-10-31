update : v_3

아래 매뉴얼은 owner(=osk) 허락 없이는 수정하지 말 것
ALL PRODUCT NUMBERS MUST BE READ AND COMPARED AS TEXT, NOT AS NUMBERS.

[environment_definition]

1. 기본 디렉토리 구조 및 역할
- Root Path: C:\Users\user\Desktop\wetakwang
- git: https://github.com/PARK-JUHWAN/wetakwang
  * private 이니 염려 마세요
- excel/ : B2B 대장 엑셀 파일 보관소 (일자별 대장 보관, 예: 20261024.xls)
- manual/ : 프로젝트 전체 운영 매뉴얼 보관소
  * 1_master_manual.md : 총괄 운영 규칙 (현재 파일)
  * 2_detail_gemini_manual.txt : 제미나이 상세 가이드
  * 3_detail_claude_manual.txt : 클로드코드 상세 가이드
  * 4_naver_manual.txt : 스마트스토어 상품등록 매뉴얼
  * 5_coupang_manual.txt : (쿠팡현재중지) 상품등록 매뉴얼
- mymd/ : 현재 스마트스토어/(쿠팡현재중지)에 판매 중인 활성 상품 md 파일 보관소 (파일명 = 상품번호 Text)
- soldout/ : 품절 처리된 상품 md 파일 보관소 (파일명 = 상품번호 Text)
- RESTRICTED/ : 민감 정보 및 브랜드 에셋 보관소
  * inform/ : 사업자 정보, 계약서, 통장사본 등 보안 문서
  * photo/ : 탁대표 프로필 사진(photo_profile_1~7) 및 서명 이미지
  * thumb_prompt/ : 썸네일 생성용 프롬프트 텍스트

2. 매뉴얼 내 핵심 변수 명칭 정의
- owner : 운영자(=osk)
- new : excel/ 폴더 내 가장 최신 날짜의 B2B 엑셀 원본 파일
- old : excel/ 폴더 내 'new' 바로 직전 날짜의 B2B 엑셀 파일
- new_r : 'new' 엑셀에서 1~4행 제거 후 BA열(네이버가), BB열((쿠팡현재중지)가) 판매가 수식을 계산하여 덮어쓴 변환 파일
- now : 현재 mymd/ 폴더에 존재하는 활성 상품 (A열 상품번호 Text 기준)
- soldout : 현재 soldout/ 폴더에 존재하는 품절 상품 (A열 상품번호 Text 기준)
- rebirth : soldout/ 에 있던 상품 중 최신 엑셀('new_r') A열에 다시 등장하여 재입고된 상품
- site_price : 도매콜 사이트의 실제 도매가. 엑셀 B열과 다를 수 있으며 owner(=osk)가 확인해 알려준다
- store_price : site_price 를 (daily_4-2-2) 공식의 B열값에 넣어 계산한 네이버 실제 판매가
* 변환·가격변동 비교(daily_7)의 기준점은 항상 엑셀 BA열이다. 엑셀 BA가 다음 날 달라지면 사이트 가격도 달라진 것으로 본다
* md 의 '## 가격' 섹션에는 엑셀 B·BA, site_price, store_price 를 모두 적고, 일자별 추이 표(일자 | 엑셀 B | 엑셀 BA | 사이트 도매가 | 스토어 판매가)에 매일 한 줄씩 누적한다 (예: mymd/1472238010.md)

3. 운영 사정 상, 네이버만 운영한다. (쿠팡현재중지)은 대기.

[daily_manual]

(daily_1)
git checkout main
git pull origin main
git log --oneline -5

(daily_2)
excel 폴더 내 최신 날짜의 엑셀 확인
ex) 파일명 : 20261225
이하 'new'(=최신 날짜의 엑셀) 라고 칭한다

(daily_3)
excel 폴더 내 'new' 바로 앞 날짜의 엑셀 확인
이하 'old' 라고 칭한다

(daily_4)
excel 폴더 내 'new' 를 다음과 같이 변환한다
이하 'new_r'(=최신 날짜의 엑셀을 변환) 라고 칭한다

(daily_4-1) 1,2,3,4 행을 지운다

(daily_4-2) BA열 "네이버(현재가)" 만들기

(daily_4-2-1) BA1 = "네이버(현재가)"

(daily_4-2-2) BA2부터 마지막 상품 행까지, 행마다 아래 값을 정수로 넣는다 (수식 아님, 쉼표·소수점 없음)
MAX( E열값, CEILING( (10000/0.85 + B열값*(1-1/11)) / (1-1/11-0.0663), 100 ) )
* B열 = 현재B2B가, E열 = 정찰가 (E열이 0이거나 비어 있으면 CEILING 값만)

(daily_4-2-3)
엑셀값과 실제 사이트 도매가가 다른 경우가 많은 관계로
owner(=osk) 에게 url 을 주고 검색하게 하기
url 형식 : https://www.domecall.net/goods/goods_view.php?goodsNo=상품번호
ex) https://www.domecall.net/goods/goods_view.php?goodsNo=1472233809

(daily_4-2-4)
owner(=osk)가 알려준 실제 도매가(site_price)는 해당 상품 md 의 가격 추이 표에 일자와 함께 기록한다
* daily_7 은 엑셀 BA끼리만 비교한다 (store_price 는 비교 대상이 아님)

<!-- (daily_4-3) BB열 "(쿠팡현재중지)(현재가)" 만들기

(daily_4-3-1) BB1 = "(쿠팡현재중지)(현재가)"

(daily_4-3-2) BB2부터 마지막 상품 행까지, 행마다 아래 값을 정수로 넣는다 (수식 아님, 쉼표·소수점 없음)
MAX( E열값, CEILING( (10000/0.85 + B열값*(1-1/11)) / (1-1/11-0.108), 100 ) )
* B열 = 현재B2B가, E열 = 정찰가 (E열이 0이거나 비어 있으면 CEILING 값만) -->

(daily_5)
mymd 폴더 안의 모든 md 파일의 이름을 읽는다
* 파일명 = A열의 상품번호이며, owner(=osk)가 네이버·(쿠팡현재중지)에 올려져 있는 상품이다
이하 'now'(=네이버·(쿠팡현재중지)에 올려져 있는 상품) 라고 칭한다

(daily_6)
'now' 가 'new_r' 의 A열에 있는지 확인한다

(daily_7)
'new_r' 에 있는 'now' 의 'old'(BA, BB)값과 'new_r'(BA, BB)값이 달라졌는지 확인한다

(daily_8)

(daily_8-1) soldout 폴더 안의 모든 md 파일의 이름을 읽는다
이하 'soldout' 이라 칭한다

(daily_8-2) 'soldout' 이 'new_r' 의 A열에 있는지 확인한다
있는 것을 'rebirth' 라 칭한다

(daily_8-3) 'rebirth' 각 상품의 md 파일에 적힌 가격(네이버, (쿠팡현재중지))을 읽어 'new_r' 의 (BA, BB)값과 달라졌는지 확인한다

(daily_9)
*new 날짜* 보고 (채팅으로 owner(=osk)에게 알린다)
품절 상품번호 : (daily_6) 에서 'now' 중 'new_r' 에 없는 상품번호. 없으면 "없음"
가격변동 상품번호 : (daily_7) 에서 값이 달라진 상품번호. 없으면 "없음"
재입고 상품번호 : (daily_8-2) 에서 찾은 'rebirth' 상품번호. 없으면 "없음"
재입고 가격변동 : (daily_8-3) 에서 'rebirth' 의 md 가격 → 'new_r' 가격. 같으면 "동일"

(daily_10)
'new_r' 을 excel 폴더 내 'new' 에 덮어씌우고 push
* 'new_r' 은 'new' 와 같은 형식·파일명으로 저장한다
* 커밋 메시지 = daily_ + 'new' 파일명 (예: 'new' 가 20261225 이면 daily_20261225)
* 브랜치는 main 이든 sub 이든 일단 push 하면 owner(=osk)가 알아서 처리한다

[soldout_manual]

(soldout_1)
daily_9 보고의 "품절 상품번호"가 "없음"이면 soldout_manual 을 진행하지 않고 끝낸다
* 있으면 soldout_2 부터 진행한다

(soldout_2)
owner(=osk)가 daily_9 보고의 "품절 상품번호"(daily_6 결과)를 보고, 네이버·(쿠팡현재중지) 플랫폼에서 해당 상품을 직접 품절 처리한다
(클로드코드 업무 아님)

(soldout_3)
클로드코드는 daily_9 보고의 "품절 상품번호"에 해당하는 md 파일(파일명 = 상품번호)을 mymd 폴더에서 soldout 폴더로 옮기고 push
* 커밋 메시지 = soldout_ + 'new' 파일명 (예: 'new' 가 20261225 이면 soldout_20261225)
* 브랜치는 main 이든 sub 이든 일단 push 하면 owner(=osk)가 알아서 처리한다

[change_manual]

(change_1)
daily_9 보고의 "가격변동 상품번호"가 "없음"이면 change_manual 을 진행하지 않고 끝낸다
* 있으면 change_2 부터 진행한다

(change_2)
owner(=osk)가 daily_9 보고의 "가격변동 상품번호"(daily_7 결과)를 보고, 네이버·(쿠팡현재중지) 플랫폼에서 판매가를 직접 수정한다
(클로드코드 업무 아님)

[rebirth_manual]

(rebirth_1)
daily_9 보고의 "재입고 상품번호"가 "없음"이면 rebirth_manual 을 진행하지 않고 끝낸다
* 있으면 rebirth_2 부터 진행한다

(rebirth_2)
owner(=osk)가 daily_9 보고의 "재입고 상품번호"(daily_8-2 결과)를 보고, 네이버·(쿠팡현재중지) 플랫폼에서 해당 상품의 판매를 다시 시작한다
(클로드코드 업무 아님)

(rebirth_3)
owner(=osk)가 daily_9 보고의 "재입고 가격변동"(daily_8-3 결과)을 보고, 가격이 달라진 상품은 네이버·(쿠팡현재중지) 플랫폼에서 판매가를 직접 수정한다
(클로드코드 업무 아님)
* "동일"이면 수정하지 않는다

(rebirth_4)
클로드코드는 daily_9 보고의 "재입고 상품번호"에 해당하는 md 파일(파일명 = 상품번호)을 soldout 폴더에서 mymd 폴더로 옮기고 push
* 커밋 메시지 = rebirth_ + 'new' 파일명 (예: 'new' 가 20261225 이면 rebirth_20261225)
* 브랜치는 main 이든 sub 이든 일단 push 하면 owner(=osk)가 알아서 처리한다

[digging_manual]

(digging_1)
ALL PRODUCT NUMBERS MUST BE READ AND COMPARED AS TEXT, NOT AS NUMBERS.
excel 폴더 내 가장 최신 날짜의 엑셀('new' 또는 'new_r')을 읽는다.

(digging_2)
기존 등록 상품 및 품절 상품 중복 제거 (Text 비교)
- mymd 폴더 안의 모든 파일명(확장자 제외한 상품번호 Text)을 읽는다.
- soldout 폴더 안의 모든 파일명(확장자 제외한 상품번호 Text)을 읽는다.
- 위 두 폴더에 존재하는 상품번호는 엑셀 A열(상품번호 Text) 대조 대상에서 즉시 제외한다.

(digging_3)
1차 기본 조건 필터링
- (digging_3-1) G열(반품불가): 비어있는(공란) 행만 선택한다. ("반품불가" 표시 상품 제외)
- (digging_3-2) B열(현재B2B가): 15,000원 이상인 상품만 선택한다. (B >= 15000)

(digging_4)
2차 품목 및 옵션 필터링 (상품명 I열, 카테고리 J~L열 기준)
- (digging_4-1) 식품 카테고리 제외: 영업신고증/위생증이 없으므로 식품, 가공식품, 농축수산물, 건강기능식품 관련 품목은 전부 제외한다.
- (digging_4-2) 다중 옵션/사이즈 분할 상품 제외: 사이즈(4XL, 5XL, 95, 100 등)나 색상(블랙, 화이트, 핑크 등)별로 상품번호를 쪼개놓은 의류, 신발, 잡화류는 전부 제외한다.
- (digging_4-3) 단일 완제품 우선 선별: 별도의 옵션 선택이 필요 없거나 단일 규격으로 판매 가능한 생활용품, 생활소형가전, 주방용품 등을 우선 추출한다.

(digging_5)
배송비 검증
- P열(배송비)을 확인한다. 기본 배송비는 3,000원이어야 하며, 3,000원이 아닌 상품(무료배송, 4,000원 등)이 있을 경우 디깅 보고 시 예외로 반드시 명시한다.

(digging_6)
이미지 URL 정제 및 유령 상품 검증용 링크 추출
- R열(상품설명): `<IMG src="...">` 또는 `<IMG src='...'>` 태그 형태에서 순수 `http` 이미지 URL만 추출하여 텍스트 링크로 변환한다.
- S열(메인이미지) 및 T열(상세이미지1): 원본 URL을 함께 추출한다.

(digging_7)
디깅 결과 보고 (채팅으로 owner(=osk)에게 알린다)
추출된 각 후보 상품에 대해 아래 서식으로 정리하여 보고한다.
* 상품번호(Text) : A열값
* 상품명 : I열값
* 공급가(B열) : B열값 원
* 배송비(P열) : P열값 원 (3,000원이 아니면 강조 표시)
* 메인이미지(S열) : S열 URL
* 상세설명(R열) : 정제된 R열 URL (owner가 직접 접속하여 이미지 엑박/유령상품 여부 확인)
* 상세이미지1(T열) : T열 URL