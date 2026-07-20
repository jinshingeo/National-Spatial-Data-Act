
# Google Maps Platform 조사 — 지침 대응 버전 (2026.7.20)

> 출처: `국외_지도플랫폼_API_조사지침_필수.docx` (교수님 지침, 2026.7.20 수령)
> 원칙: 아래 4개 질문에 답하는 데 필요한 것만 기록. 상세 요금·쿼터·세부기능 카탈로그·캐싱조건 등은 여기 넣지 않음.
> (해당 상세자료는 `Google_API_조사_작업틀_20260720.md`에 백업으로 남아있음 — 필요시 참조용으로만 사용)

---

## 1. API 제공 목록

> 목적: "각 플랫폼은 어떠한 API를 제공하는가?" — 리스트만. 기능 설명·요금 불필요.

`[정리 완료, 출처: mapsplatform.google.com/intl/ko_kr/maps-products/, 2026.7.20]`

- **지도(Maps)**: 3D Maps, Aerial View, 동적 지도, 동적 스트리트 뷰, 고도, 지도 타일, Maps Embed, 포토리얼리스틱 3D 타일, 정적 지도, 정적 스트리트 뷰, 스트리트 뷰 타일, Maps SDK, Immersive Maps
- **경로(Routes)**: Compute Routes, Compute Routes Matrix, 내비게이션 커넥트, Navigation SDK, Roads, Route Optimization
- **장소(Places)**: Address Validation, 자동 완성, 지오코딩, 위치정보, Isochrones API, 주변 지역 검색, 장소 세부정보, 장소 사진, Places Aggregate, 텍스트 검색, Time Zone, Grounding with Google Maps, Maps Grounding Lite, 장소 UI 키트
- **환경(Environment)**: 공기질, 태양광, 꽃가루, 날씨
- **분석·도구**: 데이터 세트 및 모델(Places Insights, Street View Insights, 항공·위성 영상분석/모델, 인구 동태 인사이트, 도로 관리 정보), 도구(Google 어스, Earth Engine)

---

## 2. 제공 방식 — 원본 배포 vs API 호출 (4장·5장 제1안 연결)

> 목적: 데이터를 다운로드·반출해서 재사용 가능한가, 아니면 호출로 기능만 쓰는 구조인가.
> **약관의 "원본 스크래핑·대량 다운로드·재배포 금지" 조항을 원문 발췌 + URL + 확인일자로 기록.**

`[조사 완료]` 출처: Google Maps Platform Terms of Service, `cloud.google.com/maps-platform/terms`, 2026.7.20 확인 (Section 3.2 License Requirements and Restrictions)

**결론: Google은 지도 콘텐츠를 원본 반출·재사용 가능한 형태로 배포하지 않으며, API 호출을 통한 서비스 내 사용만 허용한다.** "1:5,000 수치지도 반출 없이는 서비스가 불가능하다"는 주장과 반대로, Google 스스로 원본 데이터의 추출·반출·재배포를 약관상 명시적으로 금지하고 있다 — 즉 Google Maps Platform은 태생적으로 "API 호출형" 모델이며, 반출된 원본 데이터를 유통하는 모델이 아니다.

**(a) No Scraping — 3.2.3(a)**
> "Customer will not export, extract, or otherwise scrape Google Maps Content for use outside the Services. For example, Customer will not: (i) pre-fetch, index, store, reshare, or rehost Google Maps Content outside the services; (ii) bulk download Google Maps tiles, Street View images, geocodes, directions, distance matrix results, roads information, places information, elevation values, and time zone details; (iii) copy and save business names, addresses, or user reviews; or (iv) use Google Maps Content with text-to-speech services."

**(b) No Caching — 3.2.3(b)**
> "Customer will not cache Google Maps Content except as expressly permitted under the Maps Service Specific Terms."

**(c) No Creating Content From Google Maps Content — 3.2.3(c)**
> "Customer will not create content based on Google Maps Content." (도로·건물 외곽선 트레이싱, 45도 이미지로 3D 건물모델 생성, Elevation API로 지형모델 구축, Street View로 가로수 위치 인덱스 구축, **"use Google Maps Content to improve machine learning and artificial intelligence models, including to train, test, validate or fine-tune the models"**(AI 모델 학습용 사용도 금지) 등을 명시적 예시로 금지)

**(d) No Re-Creating Google Products or Features — 3.2.3(d)**
> "Customer will not use the Services to create a product or service with features that are substantially similar to or that re-create the features of another Google product or service." (구글 지도 서비스를 재배포하거나 대체재를 만드는 것, 자체 실시간 내비게이션 기능을 구축하는 것 등을 금지 예시로 명시)

![그림1. Google Maps Platform 서비스 약관 3.2.3 원문 캡처](evidence_ToS_3.2.3_원문캡처.png)

그림1. Google Maps Platform 서비스 약관 Section 3.2.3 원문 캡처. 출처: cloud.google.com/maps-platform/terms, 2026.7.20 확인.

**(참고) 일반 배포 제한 — 3.2.1(b) General Restrictions**
> "Customer will not: ... (b) sell, resell, sublicense, transfer, or distribute the Services"

| 항목 | 내용 |
|---|---|
| 원본 다운로드 허용 여부 | 불허 — 명시적으로 "export, extract, scrape, bulk download" 금지 |
| 캐싱 허용 범위 | Maps Service Specific Terms에서 명시적으로 허용한 경우만 예외적으로 가능 (별도 확인 필요 — 통상 30일 제한으로 알려짐, 원문 재확인 권장) |
| 재배포·재판매 | 금지 (3.2.1(b), 3.2.3(d)) |
| 조항 번호·출처 URL | Section 3.2.1, 3.2.3 / cloud.google.com/maps-platform/terms |
| 확인일자 | 2026.7.20 |
| 캡처 증빙자료 | `Google Maps Platform Terms Of Service _ Google Cloud.pdf` (본 폴더 저장, 2026.7.20 15:56 KST 캡처 — PDF 39페이지, 인용 부분은 6~8페이지) |
| 결론(원본 vs API only) | **API 호출형(API-only)** — 원본 데이터 반출·재사용은 약관상 전면 금지, 서비스 내 실시간 호출 결과만 사용 가능한 구조 |

`[남은 확인]` "Maps Service Specific Terms"에서 캐싱이 예외적으로 허용되는 구체 기간(며칠/개월)을 원문으로 확인하면 논증이 더 탄탄해짐 — 우선순위는 낮음, 여유 있으면 추가.

---

## 3. 한국 vs 해외 제공 격차 (4장 핵심 — 한국만 선별 저하 논증)

### ㉠ 개발자 API 레이어 — 완료

`[정리 완료, 출처: developers.google.com/maps/coverage, 확인 필요 — 이전 조사는 2026.5.12 기준이라 재확인 권장]`

| 기능 | 한국(KR) 상태 |
|---|---|
| 지도 타일 2D/3D | 정상 제공 |
| 지오코딩 | 정상 제공 |
| Maps JavaScript 3D | 미제공 |
| 교통정보 레이어 | 미제공 |
| 운전 경로/내비게이션 | 미제공 |
| 자전거 경로 | 미제공 |
| 도보 경로 | 미제공 |
| 속도 제한 데이터 | 미제공 |

→ `[재확인 필요]` developers.google.com/maps/coverage 접속 후 국가 필터를 대한민국으로 설정해 2026.7.20 기준으로 동일한지 재캡처.
→ 비교 기준으로 미국(US) 또는 일본(JP) 등 정상 서비스 국가의 커버리지 표도 함께 캡처하면 "한국만 저하"라는 대조가 더 명확해짐.

### ㉡ 소비자 앱 레이어 — 미착수

`[신규조사 필요]`

VPN으로 한국 IP와 해외 IP(예: 미국·일본)를 각각 연결해 Google Maps 소비자 앱(또는 웹)에서 동일 기능의 제공 여부를 대조 캡처.

| 기능 | 한국 IP | 해외 IP(국가: ) | 비고 |
|---|---|---|---|
| 자동차 턴바이턴 내비게이션 | `[캡처 필요]` | `[캡처 필요]` | |
| 실시간 교통정보 표시 | `[캡처 필요]` | `[캡처 필요]` | |
| 자전거/도보 경로 안내 | `[캡처 필요]` | `[캡처 필요]` | |
| 3D 지도/실감 뷰 | `[캡처 필요]` | `[캡처 필요]` | |
| 대중교통 경로 | `[캡처 필요]` | `[캡처 필요]` | |

---

## 4. 온쇼어 강제 요건 (5장 연결 — 법으로 온쇼어 강제한 나라 대조 사례)

> 목적: Google API를 쓰려면 승인이 필요한지, 승인받기 위해 어떤 정보를 제공해야 하는지.
> AMAP(중국)은 개발자 실명·현지법인 자격·데이터 중국 내 저장 강제·GCJ-02 좌표계 사용이 강제된다는 것과 대조하기 위함.

`[신규조사 필요]`

- [ ] Google Cloud/Maps Platform 계정(API 키) 발급 절차 확인 — 승인제인지 즉시발급인지
- [ ] 요구되는 정보: 사업자 등록/법인 정보 요구 여부, 결제수단(신용카드)만으로 충분한지, 별도 심사 있는지
- [ ] 데이터 저장 위치 강제 조항 여부 (한국 내 저장 요구가 있는지 — 없다면 그 자체가 대조점)
- [ ] 좌표계 강제 여부 (한국은 WGS84 사용 여부, 중국처럼 별도 좌표계 강제가 있는지)

| 항목 | Google | (참고) AMAP |
|---|---|---|
| 승인 필요 여부 | `[조사 필요]` | 승인제로 알려짐 |
| 요구 정보 | `[조사 필요]` | 개발자 실명, 현지법인 자격 |
| 데이터 저장 위치 강제 | `[조사 필요]` | 중국 내 저장 강제(알리바바 클라우드) |
| 좌표계 강제 | `[조사 필요]` | GCJ-02 강제 |

---

## 부록: Apple·AMAP도 Google 커버리지 표 같은 공식 국가별 표가 있는가?

`[신규조사 필요, 채린/민우 담당 파트일 수 있음 — 확인 후 담당 조율]`

- [ ] Apple Maps 개발자 문서에 국가별 기능 제공 공식 표 존재 여부
- [ ] AMAP(高德地图) 오픈플랫폼에 국가별 기능 제공 공식 표 존재 여부
- 없다면 "구글만 국가별 커버리지를 투명하게 공개하고 있다"는 점 자체가 서술 포인트가 될 수 있음.
