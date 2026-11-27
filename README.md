# kr-font-skill

**한글이 먼저인 폰트 스킬.** AI 티 나지 않는 상업용 무료 한글/영문 폰트를 찾고, 고르고, 안전하게 쓰게 해 주는 AI 스킬 묶음입니다.

AI가 매번 고르는 주아체, 도현체, Space Grotesk 같은 폰트 대신, 브리프에 맞는 **상업용 무료** 폰트를 찾고, 라이선스를 원문으로 확인하고, 비주얼 보드에서 직접 고르게 해 줍니다.

## 빠른 설치

**어떤 AI 코딩 도구든 한 줄** (Claude Code, Codex, Cursor 등)
```
npx skills add lilykwon/kr-font-skill
```

**Claude Code 공식 디렉터리** (심사 통과 후)
```
/plugin install kr-font-skill@claude-plugins-official
```

**ChatGPT, Codex 플러그인 디렉터리** (심사 통과 후)
플러그인 목록에서 `kr-font-skill` 을 검색해 설치하세요.

아래는 도구별 자세한 방법이에요.

## 사용법

스킬은 `SKILL.md` 하나로 된 폴더라서 Claude와 GPT(Codex) 어느 쪽에서도 쓸 수 있어요. 쓰는 곳에 맞는 방법을 고르세요.

### Claude Code (추천)

다운로드, 스크린샷, 보드 파일 열기까지 전부 됩니다.

```
/plugin marketplace add lilykwon/kr-font-skill
/plugin install kr-font-skill@kr-font-skill
```

플러그인 없이 쓰려면 스킬 폴더를 `~/.claude/skills/` 에 복사해도 됩니다.

### claude.ai (웹, 데스크톱, 모바일)

1. **설정 > 기능** 에서 코드 실행과 파일 만들기를 켭니다.
2. **맞춤 설정 > 스킬** 에서 `+` > `Upload a skill` 을 누릅니다.
3. 이 저장소의 `dist/` 폴더에 있는 zip을 스킬마다 하나씩 올립니다 (`font-research.zip` 등).
4. 대화에서 "레트로한 카페 브랜드 폰트 찾아줘"처럼 요청하면 됩니다. 보드는 대화 안에 바로 보여요.

참고: claude.ai의 실행 환경은 인터넷 접근이 제한될 수 있어서, 일부 폰트는 다운로드 대신 공식 링크로 안내됩니다.

### Codex (CLI, IDE 확장, ChatGPT 데스크톱 앱의 Codex)

스킬 폴더를 Codex 스킬 위치에 복사합니다.

```
# 내 계정 전체에서 쓰기
mkdir -p ~/.codex/skills
cp -r plugins/kr-font-skill/skills/* ~/.codex/skills/

# 특정 저장소에서만 쓰기
mkdir -p .agents/skills
cp -r plugins/kr-font-skill/skills/* .agents/skills/
```

Codex를 다시 시작한 뒤 `/skills` 로 목록을 확인하고, `$font-research` 처럼 이름을 불러 쓰거나 그냥 요청하면 됩니다.

- 보드는 `fonts/board.html` 파일로 만들어지니 브라우저로 열어서 고르세요.
- 반복 방지 기록은 `~/.claude/font-research/history.md` 에 저장됩니다 (Codex에서도 같은 경로).

### ChatGPT 채팅 (웹, 모바일)

ChatGPT 데스크톱 앱은 위의 Codex 방식으로 스킬을 쓸 수 있어요. 웹이나 모바일 채팅에서는 플러그인으로 묶인 스킬만 바로 쓸 수 있어서, 디렉터리 심사가 끝나기 전까지는 이렇게 쓰세요.

1. **프로젝트** 를 하나 만들고, 쓰려는 스킬의 `SKILL.md` 를 파일로 올립니다.
2. 프로젝트 지침에 "폰트 요청이 오면 첨부한 SKILL.md 지침을 그대로 따를 것"이라고 적습니다.
3. 웹 검색을 켜고 요청합니다. 보드는 HTML 파일로 받아서 열어 보세요.

### 어디서나 지켜지는 것

어떤 모델로 쓰든 금지 목록, 라이선스 원문 확인, "법률 자문 아님" 규칙은 똑같이 적용됩니다. 다만 웹 검색이나 파일 쓰기가 안 되는 환경에서는 카탈로그 탐색과 다운로드 단계가 제한돼요.

## 들어 있는 스킬

| 스킬 | 하는 일 | 예시 요청 |
|---|---|---|
| `font-research` | 브리프에 맞는 폰트 후보를 카탈로그(눈누, 산돌구름 무료, Google Fonts, Fontshare 등)에서 찾아 보드로 보여주고, 고른 폰트를 라이선스 기록과 함께 받기 | "레트로하지만 고급스러운 카페 브랜드 폰트, 한글만" |
| `font-alternatives` | 유료 폰트 이름, 이미지 속 글씨, 사이트 URL을 받아 비슷한 무료 폰트를 원본과 나란히 비교 | "이 사진의 로고 글씨랑 비슷한 무료 폰트" |
| `font-license-audit` | 프로젝트나 사이트에서 쓰인 폰트를 찾아 용도별 라이선스 위험을 점검하고 `FONT-AUDIT.md` 작성 (파일은 건드리지 않음) | "이 프로젝트 폰트 라이선스 점검해줘" |
| `korean-webfont-optimize` | 라이선스 확인 후 한글 웹폰트 서브셋, woff2 변환, unicode-range 분할, preload 설정 | "사이트 한글 폰트가 너무 무거워" |

## 원칙

- **본문용 기본 폰트는 허용**: Pretendard, SUIT, Noto Sans KR, Inter, IBM Plex 등은 본문, UI, 대체 폰트로 씁니다.
- **AI 시그니처 폰트는 금지**: 주아체, 도현체, G마켓 산스, Space Grotesk, Instrument Serif, Fraunces 등은 요청하지 않는 한 후보에 넣지 않습니다. 전체 목록은 `font-research/SKILL.md`.
- **기억이 아니라 카탈로그에서**: 후보의 절반 이상은 실행할 때 실제로 둘러본 사이트에서 찾습니다.
- **라이선스는 원문으로**: 집계 사이트 표만 보고 확정하지 않고, 용도별(인쇄, 웹, 앱, 포장, 로고)로 O / X / 확인 필요를 매깁니다.
- **반복 방지**: `~/.claude/font-research/history.md` 에 추천과 선택을 기록해 같은 폰트만 계속 나오지 않게 합니다.

## 주의

이 스킬은 법률 자문이 아닙니다. 로고나 상표 등록처럼 되돌리기 어려운 사용 전에는 폰트 저작권자의 최신 약관을 직접 확인하세요. 폰트 파일 자체는 이 저장소에 들어 있지 않으며, 각 폰트는 배포처의 라이선스를 따릅니다.

## 라이선스

스킬 텍스트: MIT
