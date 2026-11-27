---
name: "korean-webfont-optimize"
description: "무겁거나 느린 한글 웹폰트를 라이선스 확인 후 서브셋, woff2, unicode-range 분할, preload로 최적화한다. Optimizes heavy Korean webfonts."
---

# Korean Webfont Optimize

한글 폰트는 글리프가 수천에서 1만 자 이상이라 한 웨이트에 수 MB가 흔하다. 이 스킬은 사이트에서 쓰는 한글 웹폰트를 가능한 한 가볍고 빠르게 만든다. 원칙은 하나다: **폰트 파일을 손대기 전에 라이선스가 허용하는지 확인한다.**

## 0. 현황 파악

- 프로젝트에서 폰트 로딩 경로를 모두 찾는다: `@font-face`, `@import`, `<link>` (Google Fonts, jsDelivr, 눈누 CDN), `next/font`, Tailwind `fontFamily`, CSS-in-JS, 번들된 폰트 파일(`*.ttf *.otf *.woff *.woff2`)
- 폰트별로 기록: 패밀리명, 웨이트 수, 포맷, 파일 크기, 자체 호스팅인지 CDN인지, 어느 요소에 쓰이는지(본문, 제목, 로고 텍스트 등)
- 배포된 사이트가 있으면 실제 전송량도 본다: 폰트 요청 수, 총 바이트, 렌더 차단 여부. 가능하면 Lighthouse(`npx lighthouse <url> --only-categories=performance`)로 기준선을 남긴다

## 1. 라이선스 게이트 (건너뛰지 않는다)

서브셋, woff2 변환, 분할은 모두 폰트 파일의 수정 또는 포맷 변환이다. 폰트마다 라이선스 원문(패키지의 OFL.txt, LICENSE, 배포처 약관)을 읽고 아래로 분류한다.

- **변환 가능**: SIL OFL 1.1, Apache 2.0 등 수정을 허용하는 라이선스
  - OFL에 Reserved Font Name(RFN)이 있으면, 수정본은 원래 이름을 쓸 수 없다. 파일 내부 이름(name 테이블)과 `font-family` 이름을 바꾼다 (예: `MyFont` → `MyFontWeb`). RFN 여부는 OFL.txt 첫줄의 Copyright 문장에 "with Reserved Font Name"이 있는지로 확인한다
- **변환 금지**: "수정 금지", "배포되는 형태 그대로 사용", "다른 형식으로 변환 금지" 같은 문구가 있는 자체 라이선스. 한국 기업/기관 무료 폰트에 흔하다
  - 파일을 건드리지 않는다. 대신 배포처가 공식 제공하는 웹폰트(woff/woff2, 공식 CDN, 눈누에 등록된 웹폰트 코드)를 쓰고, 2단계의 로딩 최적화(5단계)만 적용한다
- **웹 임베딩 자체가 금지/조건부**: 여기서 멈추고 사용자에게 알린다. 대안으로 (a) 텍스트를 이미지/SVG로 만들기, (b) 비슷한 무료 폰트로 교체(font-alternatives 스킬)를 제안한다
- 판단이 안 되면 추측하지 않고 "확인 필요"로 두고 파일을 건드리지 않는다. 법률 자문이 아니라는 점을 한 줄로 알린다

## 2. 전략 고르기

폰트의 역할에 따라 다르게 처리한다.

| 역할 | 전략 |
|---|---|
| 로고, 고정된 헤드라인 (글자가 거의 안 바뀜) | 쓰이는 글자만 남기는 **텍스트 서브셋**. 수십 KB 이하로 줄어든다 |
| 디스플레이 (제목, 배너, CMS에서 바뀜) | **KS X 1001 2,350자 + 라틴 + 기호** 서브셋 또는 unicode-range 분할 |
| 본문 (사용자 입력, 댓글 등 어떤 글자든 나옴) | 전체 11,172자를 유지하되 **unicode-range 분할(dynamic subset)**. 필요한 조각만 내려받는다 |

- 본문 폰트는 직접 분할하기 전에 배포처가 이미 dynamic subset을 주는지 확인한다 (예: Pretendard의 `dynamic-subset` CSS, Google Fonts의 한국어 폰트는 자동 분할). 있으면 그걸 쓰는 게 가장 안전하다
- 2,350자 서브셋은 "뛰", "훽" 같은 글자가 빠진다. 사용자 입력이 들어가는 자리에는 쓰지 않는다. fallback으로 빠진 글자가 다른 폰트로 보일 수 있다는 점을 알린다
- 웨이트 수를 줄이는 것도 최적화다. 실제로 쓰는 웨이트만 남기거나, 가변 폰트(variable)가 있으면 웨이트 3개 이상일 때 가변 폰트 하나가 더 가벼운지 비교한다

## 3. 변환 작업 (변환 가능한 폰트만)

원본은 절대 덮어쓰지 않는다. 결과물은 `fonts/optimized/` 같은 새 폴더에 만든다.

도구 준비: `pip install fonttools brotli` (woff2에 brotli 필요)

**텍스트 서브셋** (로고, 고정 문구)
```
pyftsubset in.ttf --text-file=used-chars.txt --flavor=woff2 \
  --layout-features='*' --output-file=fonts/optimized/Logo-subset.woff2
```
- `used-chars.txt` 는 소스에서 그 폰트가 적용되는 문구를 모아 만든다. 대문자/소문자, 숫자, 공백, 구두점을 포함한다

**KS X 1001 서브셋** (디스플레이)
- 2,350자 목록은 Python으로 생성한다: EUC-KR의 한글 영역(0xB0A1~0xC8FE)을 디코딩하면 정확히 2,350자가 나온다. 거기에 U+0020~U+007E, 한글 자모(U+3131~U+318E), 자주 쓰는 기호(· “” ‘’ … → ₩ 등)를 더한다
- `pyftsubset in.ttf --text-file=ksx1001.txt --flavor=woff2 --layout-features='*' --output-file=...`

**unicode-range 분할** (본문)
- 도구 후보: `cn-font-split` (npm, CJK 분할용), 또는 fontTools로 직접 분할. 어느 쪽이든 설치 전에 사용자에게 알리고, 실행은 다운로드한 폰트 폴더 밖의 스크립트로 한다
- 직접 분할할 때: 사용 빈도 높은 글자(KS X 1001 범위)를 앞 조각에, 나머지 현대 한글(U+AC00~U+D7A3 중 남은 글자)를 뒤 조각들에 담는다. 조각당 20~60KB 수준이 적당하다. 각 조각마다 `@font-face`에 `unicode-range`를 정확히 적는다
- 라틴은 별도 조각으로 분리한다 (영문만 있는 페이지에서 한글 조각을 받지 않게)

**변환 후 검증**
- fontTools로 결과 파일의 cmap을 읽어 필요한 글자가 다 들어갔는지 확인한다 (텍스트 서브셋은 문구의 글자를 하나씩 대조)
- RFN이 있는 OFL 폰트는 name 테이블의 패밀리명을 바꿨는지 확인한다
- 각 결과 폴더에 원본 라이선스 파일을 함께 둔다 (OFL은 수정본에도 라이선스 동봉이 필요)

## 4. CSS 작성

```css
@font-face {
  font-family: 'BrandSerifWeb';
  src: url('/fonts/optimized/BrandSerif-ko-001.woff2') format('woff2');
  font-weight: 700;
  font-display: swap;
  unicode-range: U+AC00-AD7F;
}
```
- `font-display`: 본문은 `swap`, 로고처럼 다른 폰트로 보이면 안 되는 곳은 `block`을 짧게 쓰거나 이미지로 대체한다. 장식적 폰트에서 깜빡임이 싫으면 `optional`을 제안한다
- fallback에는 레이아웃 흔들림(CLS)을 줄이는 메트릭 보정을 제안한다: `size-adjust`, `ascent-override`, `descent-override` 를 단 별도 `@font-face`(local 시스템 폰트 기반). 값은 두 폰트의 메트릭을 fontTools로 읽어 계산하고 추측하지 않는다
- font-family 스택 예: `'BrandSerifWeb', 'BrandSerifFallback', 'Apple SD Gothic Neo', 'Malgun Gothic', sans-serif`

## 5. 로딩 최적화 (모든 폰트에 적용 가능)

- 첫 화면에 보이는 폰트 파일 1~2개만 `<link rel="preload" as="font" type="font/woff2" crossorigin>`. 분할 조각 전체를 preload하지 않는다
- 외부 CDN을 쓰면 `preconnect`를 추가하고, 눈누 CDN(jsDelivr 기반)을 운영 서비스에 그대로 쓰는 것과 자체 호스팅의 장단점을 알려준다 (가용성, 캐시, 라이선스상 자체 호스팅 허용 여부)
- `@import`로 불러오는 CSS는 `<link>`로 바꿔 요청 체인을 줄인다
- Next.js는 `next/font/local` 사용을 제안한다 (자동 자체 호스팅, preload)
- 캐시 헤더: 파일명에 해시나 버전을 넣고 `Cache-Control: public, max-age=31536000, immutable`

## 6. 결과 보고

- 폰트별 표: 원래 크기, 최적화 후 크기(또는 첫 화면 기준 전송량), 적용한 전략, 라이선스 판정, 파일을 수정했는지 여부
- 변경한 파일 목록과 diff. 코드 변경은 사용자가 검토할 수 있게 보여주고, 커밋은 사용자에게 물어본 뒤에만 한다
- 가능하면 로컬 빌드를 띄워 페이지를 열고 한글이 빠진 글자 없이 제 폰트로 나오는지 확인한다. 텍스트 서브셋을 쓴 곳은 "이 문구가 바뀌면 서브셋을 다시 만들어야 한다"고 README나 주석에 남긴다

## 마지막 점검

- 변환한 모든 폰트가 수정/변환을 허용하는 라이선스인가
- RFN 폰트의 이름을 바꿨고, 라이선스 파일을 동봉했는가
- 원본 파일을 덮어쓰지 않았는가
- 사용자 입력이 들어가는 자리에 글자가 빠지는 서브셋을 쓰지 않았는가
- 전후 수치를 실제로 측정해서 보고했는가