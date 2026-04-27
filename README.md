# 🍪 칸쵸 이름 찾기

롯데 칸쵸 "내 이름을 찾아라" 이벤트의 1탄(504개)·2탄(550개) 이름을 검색할 수 있는 단일 페이지 웹앱.

## 주요 기능
- 이름 검색 → 1탄/2탄 포함 여부 즉시 확인
- 실시간 인기 검색어 TOP 10 (Firebase Realtime Database, 5분 캐시)
- 누적 검색 카운터
- 초성별 전체 이름 목록 (1탄/2탄 탭)
- 카카오톡/트위터/링크 공유
- 비슷한 이름 추천 (같은 첫 글자/초성)

## 배포 전 교체할 placeholder

### 1. Firebase
`index.html` 안 `firebaseConfig` 객체:
```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "kancho-name-search.firebaseapp.com",
  databaseURL: "...",
  ...
};
```
- Firebase 콘솔 → 프로젝트 설정 → 웹 앱에서 복사
- Realtime Database 활성화, 리전: `asia-southeast1`
- 보안 규칙은 프로젝트 사양서의 규칙 적용 (increment만 허용)

### 2. Google Analytics 4
`G-XXXXXXXXXX` 두 곳을 실제 측정 ID로 교체.

### 3. Google AdSense
- `ca-pub-XXXXXXXXXXXXXXXX` 를 승인된 클라이언트 ID로 교체
- 승인 후 각 `.ad-slot` 자리에 실제 `<ins>` 광고 코드 삽입
- 광고 슬롯 4개: 상단 배너 / 검색 결과 하단 / 목록 중간 / 모바일 앵커

### 4. 도메인
`og:url`, `canonical`, `sitemap.xml`, `robots.txt`의 `username.github.io/kancho` 를 실제 배포 URL로 교체.

### 5. 2탄 데이터
`SEASON2_NAMES = []` 배열에 550개 이름 추가.

## 배포

```bash
git init
git add .
git commit -m "칸쵸 이름 찾기 v1.0"
git branch -M main
git remote add origin https://github.com/<user>/kancho.git
git push -u origin main
```
GitHub → Settings → Pages → Source: `main` / `/ (root)`

## 어뷰징 방지
- 같은 이름 같은 세션 내 중복 카운트 차단 (Set 관리)
- 검색 간 최소 1초 간격 + 300ms 디바운스
- Firebase 보안 규칙로 increment만 허용

## 라이선스
비공식 팬 사이트. 롯데웰푸드와 무관.
