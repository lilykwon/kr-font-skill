---
name: "font-research"
description: "AI 단골 폰트를 피해 브리프에 맞는 상업용 무료 한글/영문 폰트를 찾아 보드로 고르고 라이선스와 함께 받는다. Finds commercially free Korean/English fonts beyond AI defaults."
---

# Font Research

이 스킬의 목적은 "AI가 골랐다는 티가 나지 않는, 브리프에 맞는 감각적인 폰트"를 찾는 것이다. 본문용 기본 폰트는 그대로 쓰고, 개성을 담당하는 디스플레이/캐릭터 폰트를 제대로 고르는 데 집중한다. 모든 후보는 상업적 무료 사용이 가능해야 한다.

## 0. 브리프

아래를 파악한다. 말하지 않은 항목은 기본값으로 진행하고 어떤 기본값을 썼는지 밝힌다.

- 무엇에 쓰는지: 브랜드/로고, 웹사이트, 앱, 패키지, 포스터, 영상/SNS (기본값: 웹 + 앱)
- 무드 키워드 3개 이내, 레퍼런스(사이트, 이미지, 브랜드)가 있으면 받는다
- 언어: 한영 페어링(기본값), 한글만, 영문만. "한글만"이면 한글 소스만 탐색한다
- 샘플 문구: 실제로 쓸 헤드라인이나 브랜드명. 없으면 브리프에 맞게 지어낸다
- 저장 위치 (기본값: `./fonts/`)

## 1. 폰트 3단계 분류

### 1단계: 본문용 기본 폰트 (허용, 본문/UI/fallback 전용)
본문, UI, 숫자, fallback으로는 자유롭게 쓴다. 헤드라인이나 브랜드의 얼굴로는 쓰지 않는다. 디스플레이 폰트와 짝지을 본문 폰트로 이 중에서 고른다.
- 한글: Pretendard, SUIT, Wanted Sans, Noto Sans KR, Noto Serif KR, Spoqa Han Sans Neo, IBM Plex Sans KR, Nanum Gothic
- 영문: Inter, Roboto, Open Sans, Lato, IBM Plex Sans, IBM Plex Serif, Source Serif

### 2단계: AI 시그니처 폰트 (금지)
사용자가 이름을 콕 집어 요청하지 않는 한 후보에 넣지 않는다.
- 한글: Jua, Do Hyeon, Black Han Sans, Gmarket Sans, Gaegu, Nanum Pen Script, Nanum Brush Script, Gowun Dodum, Gowun Batang, Sunflower, Gothic A1, Gamja Flower, Hi Melody, Poor Story, Single Day, Cute Font, East Sea Dokdo, Yeon Sung, Song Myung, Dongle, Nanum Myeongjo
- 영문: Space Grotesk, Instrument Serif, Geist, Syne, Fraunces, Bricolage Grotesque, Playfair Display, Cormorant Garamond, Jost, DM Sans, DM Serif Display, Poppins, Montserrat, Raleway, Plus Jakarta Sans, Manrope, Outfit, Work Sans, Nunito, Bebas Neue, Oswald, Satoshi, Clash Display, General Sans, Cabinet Grotesk, JetBrains Mono (디스플레이 용도)
- 금지 패턴: 본문 폰트 히어로 안에 세리프 이탤릭 한 단어 강조, 숫자만 모노스페이스로 처리, 한 폰트로 헤드라인부터 본문까지 전부 처리

### 3단계: 반복 방지 (history)
- `~/.claude/font-research/history.md` 에 이 스킬이 후보로 올린 폰트와 사용자가 최종 선택한 폰트를 날짜와 함께 기록한다. 파일이 없으면 만든다.
- 새 리서치에서 최근 기록에 있는 폰트는 다시 추천하지 않는다. 사용자가 이전에 선택한 폰트는 "이전 선택"으로 표시만 하고 후보 수에 넣지 않는다.
- 같은 폰트가 여러 번 후보에 오르면 그건 새로운 단골이 생기는 신호다. 2단계 금지 목록에 추가할지 사용자에게 한 줄로 묻는다.

## 2. 탐색: 기억이 아니라 카탈로그에서 찾는다

모델의 기억에서 꺼낸 폰트는 결국 비슷해진다. 그래서 후보의 절반 이상은 반드시 아래 사이트를 실제로 둘러보고(WebFetch 또는 브라우저) 찾은 것이어야 한다. 최신순, 신규, 카테고리 목록을 우선 본다.

### 한글
- 눈누 (noonnu.cc): 상업용 무료 한글 폰트 집계. 용도별 라이선스 표(인쇄, 웹, 포장, 영상, 임베딩, BI/CI, OFL)가 있다. 신규 폰트 목록과 카테고리별 목록을 본다
- 공유마당 안심글꼴 (gongu.copyright.or.kr)
- 네이버 한글한글 아름답게 (hangeul.naver.com)
- 산돌구름 무료 폰트 (다운로드에 계정 필요, 링크 전달 대상)
- GitHub의 독립 타입 프로젝트, 지자체/기업 전용서체(라이선스 원문 필수 확인)

### 영문
- Fontshare (fontshare.com): ITF Free Font License. 단, 2단계에 있는 폰트는 제외
- Velvetyne (velvetyne.fr), Collletttivo (collletttivo.it), The League of Moveable Type: 대부분 OFL
- Google Fonts: "Newest" 정렬 위주로 본다. 2단계 폰트 제외
- uncut.wtf, Open Foundry: 라이선스가 제각각이라 개별 확인 필수

### 제외
- "Free for personal use" 폰트, 출처 불명 재업로드 사이트

### 감각적으로 고르는 방법
- 브리프를 서로 다른 방향 2~3개로 해석한다. 예: "따뜻한 레트로" → (a) 70년대 간판 레터링, (b) 손맛 있는 붓 세리프, (c) 둥근 모듈형 디스플레이
- 방향마다 2~4개 후보, 전체 8~15개로 맞춘다
- 각 후보마다 브리프와 연결되는 이유를 한 줄로 쓴다. "깔끔함", "모던함" 같은 말만으로는 이유가 안 된다. 형태적 특징을 말한다 (획 대비, 굽은 터미널, 넓은 자폭, 기하학적 원형 등)
- 한영 페어링은 "디스플레이 1개 + 1단계 본문 폰트 1개" 구조로 제안한다. 한글 디스플레이와 영문 디스플레이를 짝지을 때는 획 두께와 자폭의 인상이 맞는지 본다

## 3. 라이선스 확인

- 허용: SIL OFL 1.1, Apache 2.0, ITF Free Font License, 원문에 상업적 사용이 명시된 자체 라이선스
- 패키지 안의 라이선스 파일(OFL.txt, LICENSE)을 직접 읽는다. 없으면 배포처 라이선스 페이지를 확인한다. 눈누 표만 보고 확정하지 않는다
- 용도별 판정: 인쇄, 웹 임베딩, 앱 임베딩, 영상, 포장/제품, BI/CI(로고), 수정. 각각 O / X / 확인 필요
- 기업 무료 폰트는 BI/CI 금지, 폰트 자체 판매 금지, 출처 표기 의무가 흔하다. 브리프 용도에서 X인 폰트는 보드에 올리되 빨간 배지로 표시한다
- 판단이 안 되면 추측하지 말고 "확인 필요"와 이유를 적는다. 전달 시 법률 자문이 아니라는 점을 한 줄로 알린다

## 4. 비주얼 보드 (pick and choose)

후보가 모이면 다운로드 전에 보드부터 보여준다.

- 파일: `fonts/board.html` 한 개 (CSS, JS 인라인). 브라우저로 열 수 있게 경로를 알려주고 가능하면 `open` 으로 연다. claude.ai처럼 인라인 위젯 도구가 있는 환경이면 위젯으로 보여줘도 된다
- 카드 구성: 폰트명, 제작사, 방향(a/b/c), 샘플 문구를 해당 폰트로 크게, 짝지을 본문 폰트로 쓴 짧은 문단, 용도별 라이선스 배지, 브리프와 연결되는 이유 한 줄, 공식 페이지 링크
- 상단에 샘플 문구 입력창: 바꾸면 모든 카드가 실시간으로 바뀐다
- 카드 클릭으로 선택/해제, 하단 "선택 복사" 버튼이 `선택: 폰트A, 폰트B` 형태의 텍스트를 클립보드에 복사한다. 사용자가 그걸 채팅에 붙여넣으면 다음 단계로 간다

### 미리보기 폰트 불러오는 법 (우선순위)
1. 웹폰트 CSS를 직접 로드: Google Fonts css2 URL, Fontshare `https://api.fontshare.com/v2/css?f[]=<slug>@400`, jsDelivr의 GitHub 배포본
2. 눈누 폰트: 눈누 상세 페이지의 "웹폰트로 사용" @font-face 코드에 나온 URL(대개 jsDelivr의 projectnoonnu 저장소)을 그대로 쓴다
3. 위 방법이 안 되면 스크린샷: `npx playwright screenshot --full-page <폰트 페이지 URL> fonts/previews/<slug>.png` 로 배포처 페이지를 캡처해 카드에 이미지로 넣는다. 카드에 "스크린샷 미리보기"라고 표시한다 (Playwright가 없으면 `npx playwright install chromium` 이 필요하다고 알리고 사용자 확인 후 설치)

## 5. 확보

사용자가 선택한 폰트만 처리한다.

- 폰트마다 별도의 빈 폴더 `fonts/<slug>/` 에 받는다. 압축은 그 폴더 안에서만 풀고, 받은 파일 안의 스크립트는 실행하지 않는다
- GitHub 릴리스: `gh release download -R <owner>/<repo> --pattern '*.zip' -D fonts/<slug>`
- Google Fonts: `npm pack @fontsource/<slug>` 또는 `google/fonts` 저장소의 `ofl/<family>/`
- Fontshare: `curl -L -o fonts/<slug>/<slug>.zip https://api.fontshare.com/v2/fonts/download/<slug>` (응답이 이상하면 사이트의 다운로드 링크 확인)
- 로그인, 약관 동의, 폼이 필요한 곳: 직접 진행하지 않고 공식 다운로드 링크를 정리해서 전달한다
- 검증: fontTools로 패밀리명, 웨이트 목록, 한글 지원 범위(U+AC00~U+D7A3 전체 11,172자인지 KS X 1001 2,350자인지) 확인. 웹용은 woff2 우선, 한글은 서브셋(`pyftsubset`) 또는 배포처의 dynamic subset을 안내한다

## 6. 산출물

1. `fonts/board.html`: 선택 결과 표시를 남긴 최종 보드
2. `fonts/LICENSES.md`: 폰트, 제작사, 라이선스, 공식 URL, 라이선스 원문 URL, 받은 날짜, 용도별 O/X/확인 필요, 주의사항. 라이선스 원문 파일도 각 폴더에 보관
3. CSS 스니펫: @font-face 선언과 역할별 변수 (`--font-display`, `--font-body`). 한영 혼용 시 font-family 순서는 페어링 의도에 맞게 정한다
4. 다운로드 못 한 폰트의 링크 목록
5. `~/.claude/font-research/history.md` 업데이트

## 7. 마지막 점검

- 2단계 금지 폰트와 금지 패턴이 후보나 페어링에 들어가지 않았는가
- 후보 절반 이상이 이번에 실제로 둘러본 카탈로그에서 나왔는가
- 최근 history의 폰트를 다시 올리지 않았는가
- 모든 후보의 라이선스 원문을 확인했고, 브리프 용도에서 X 또는 확인 필요인 폰트를 분명히 표시했는가
- 보드를 실제로 열어 폰트가 제대로 로드되는지 확인했는가