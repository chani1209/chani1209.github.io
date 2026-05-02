# 프로젝트 흐름 정리

## 한 줄 요약

칸쵸 "내 이름을 찾아라" 이벤트의 1탄/2탄 이름 목록을 빠르게 검색하고, 이벤트 안내/FAQ/통계/정책 페이지로 SEO와 사용자 신뢰를 보강하는 정적 GitHub Pages 사이트입니다.

## 파일 역할

- `index.html`: 메인 검색 앱. 이름 검색, 결과 표시, 인기 검색어, 전체 이름 목록, 이벤트 안내, 구매/광고 영역을 모두 담당합니다.
- `event-guide.html`: 이벤트 참여 방법, 경품, 실수 방지 팁을 정리한 콘텐츠 페이지입니다.
- `faq.html`: 이벤트/검색/개인정보 관련 FAQ 페이지입니다.
- `name-stats.html`: 1탄/2탄 이름 데이터를 통계 관점으로 풀어낸 콘텐츠 페이지입니다.
- `about.html`: 사이트 목적, 비공식 운영 고지, 데이터 출처, 광고 정책을 설명합니다.
- `privacy.html`: 개인정보 처리방침 페이지입니다.
- `ads.txt`: AdSense 게시자 확인 파일입니다.
- `robots.txt`, `sitemap.xml`: 검색엔진 크롤링과 색인 안내 파일입니다.
- `README.md`: 초기 배포/설정 메모입니다. 현재 일부 문자가 깨져 보여 추후 UTF-8 기준으로 재작성하는 편이 좋습니다.

## 메인 검색 흐름

1. 사용자가 `index.html`의 검색 폼에 이름을 입력합니다.
2. `searchName(raw)`가 입력값을 정리하고 `NAME_REGEX`로 한글 1~4글자만 허용합니다.
3. 입력값이 3~4글자이면 성을 제외한 뒤 2글자 이름으로도 자동 폴백 검색합니다.
4. `SEASON1_NAMES`, `SEASON2_NAMES` 배열과 `SET1`, `SET2`를 기준으로 1탄/2탄 포함 여부를 계산합니다.
5. `renderResult(r)`가 결과 카드, 비슷한 이름 추천, 공유 버튼, 검색 횟수 배지를 화면에 렌더링합니다.
6. 검색이 유효하면 `incrementSearchCount(name)`가 Firebase Realtime Database의 `search_counts/{name}`을 트랜잭션으로 증가시킵니다.
7. 어뷰징 방지를 위해 `localStorage` 기반으로 같은 이름 24시간 쿨다운과 브라우저당 일일 50회 카운트 제한을 둡니다.

## 데이터와 상태

- 이름 데이터는 서버 API 없이 `index.html` 안의 `SEASON1_NAMES`, `SEASON2_NAMES` 정적 배열에 들어 있습니다.
- 전체 이름 목록은 `renderList(season)`가 초성별로 그룹화해 렌더링합니다.
- 인기 검색어는 Firebase `search_counts`를 `orderByValue() + limitToLast(10)`으로 읽고, 5분 동안 `localStorage`에 캐시합니다.
- 총 검색 수는 `search_counts` 전체 합산으로 계산하며, 별도 `total_searches` 노드는 사용하지 않습니다.

## 광고/수익화 흐름

- 모든 페이지는 `AD_CONFIG`를 통해 AdSense 우선, AdFit 폴백 구조를 공유합니다.
- 현재 `AD_CONFIG.adsense.enabled`는 `false`라 AdFit이 우선 렌더링됩니다. AdSense 승인 후 `true`로 바꾸고 슬롯 ID를 채우면 됩니다.
- 메인 페이지 구매 버튼은 쿠팡 파트너스 링크(`https://link.coupang.com/a/eA8l43`)로 연결됩니다.
- 구매 안내 영역에는 쿠팡 파트너스 필수 고지 문구와 제공된 iframe 광고가 함께 들어 있습니다.

## 배포 흐름

- 별도 빌드 과정이 없는 정적 사이트입니다.
- GitHub Pages에서 `main` 브랜치의 루트(`/`)를 배포 소스로 사용하면 됩니다.
- 배포 후 확인할 핵심 URL은 `/`, `/event-guide.html`, `/faq.html`, `/name-stats.html`, `/about.html`, `/privacy.html`입니다.

## 주의할 점

- 개인정보성 연락처는 HTML 본문에 직접 넣지 않는 방향으로 유지합니다.
- 새 연락 채널이 필요하면 개인 이메일 대신 별도 공개용 계정, 폼, 또는 GitHub Issues처럼 분리된 채널을 쓰는 편이 안전합니다.
- Firebase 설정값과 광고 ID는 공개 클라이언트 코드에 노출됩니다. 보안은 Firebase Realtime Database Rules와 광고 콘솔 설정으로 제한해야 합니다.
- 히스토리에서 개인정보를 제거하려면 일반 커밋 추가가 아니라 amend/filter 후 force push가 필요합니다.
