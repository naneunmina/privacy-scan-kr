# privacy-scan-kr

React, Vue, Next.js, Node/Express, Django, Spring 등 스택을 가리지 않고
바이브코딩으로 빠르게 만든 프로젝트의 코드를 읽어서, **한국 개인정보
보호법(PIPA)** 관점에서 동의 절차나 처리방침이 빠져 있는지 **HIGH / MEDIUM**
심각도로 짚어주는 [Claude Code](https://claude.com/claude-code) 스킬입니다.

> ⚠️ **이 스킬은 법률 자문이 아니며 참고용입니다.** 실제 서비스 출시 전에는
> 반드시 법률 전문가의 검토를 받으시길 권장합니다.

## 왜 필요한가요

바이브코딩으로 회원가입·소셜로그인·분석 SDK를 붙이는 건 몇 분이면 끝나지만,
그 순간 발생하는 개인정보 보호법상 의무는 코드를 짜는 사람이 모르고 지나가기
쉽습니다. 특히 "OAuth 로그인 붙였으니 끝"이나 "처리방침 페이지 만들었으니
끝"이라는 착각이 실무에서 가장 흔한 함정입니다 — OAuth 동의창은 제공자와
이용자 사이의 별도 동의일 뿐 내 서비스의 의무를 대체하지 않고, 처리방침은
현황을 공시하는 문서일 뿐 동의를 대신하지 않습니다.

## 무엇을 제공하나요

- 🔍 **스캔 리포트** — `파일:줄` 단위로 문제 지점과 근거 법 조항을 짚어주는
  HIGH/MEDIUM 등급 리스트. 코드는 직접 고치지 않습니다.
- 📄 **개인정보 처리방침 초안** — 스캔에서 실제로 발견된 수집 항목을 근거로
- 📋 **이용약관 초안**
- ✅ **동의 체크박스 UI** — React / Vue / plain HTML 중 프로젝트 스택에 맞는 것
- 📚 **조문 원문 근거** — 판단마다 참조한 개인정보 보호법 조항을
  [rules/](skills/privacy-scan-kr/rules)에서 그대로 확인 가능

스캔 리포트는 항상 먼저 보여드리고, 문서·UI 초안은 "처리방침 만들어줘"처럼
명시적으로 요청할 때만 만들어 드립니다. 스캔 결과만 보고 싶다면 그걸로
끝입니다.

## 설치

### 방법 1: 플러그인으로 설치 (권장)

Claude Code 안에서:

```
/plugin marketplace add naneunmina/privacy-scan-kr
/plugin install privacy-scan-kr@privacy-scan-kr
```

### 방법 2: 스킬 폴더 수동 복사

이 저장소를 클론한 뒤, `skills/privacy-scan-kr/` 폴더를 프로젝트의
`.claude/skills/`(또는 사용자 전역 `~/.claude/skills/`) 아래에 복사하세요.

```bash
git clone https://github.com/naneunmina/privacy-scan-kr.git
```

두 방법 모두 설치 후에는 자연어 요청("개인정보 스캔해줘")이나 슬래시 명령으로
스킬을 호출할 수 있습니다. 플러그인으로 설치했다면 `/privacy-scan-kr:scan`
(다른 명령과 겹치지 않으면 `/scan`도 동작), 스킬 폴더를 수동 복사했다면
`/scan`입니다.

## 사용법

슬래시 명령으로 바로 부르거나, 아래처럼 자연어로 요청해도 자동으로
호출됩니다.

```
/privacy-scan-kr:scan
```

```
개인정보 스캔해줘
```

```
회원가입 코드 짰는데 법적으로 문제 없는지 봐줘
```

스캔 결과를 확인한 뒤, 문서 초안이 필요하면:

```
처리방침 만들어줘
```

## 구조

```
.claude-plugin/
├── plugin.json                  # 플러그인 매니페스트
└── marketplace.json             # 이 저장소 자체를 마켓플레이스로 등록
skills/privacy-scan-kr/
├── SKILL.md                     # Claude Code가 읽는 메인 지침
├── rules/                       # 법 조항 원문 + 판단 기준 (근거 자료)
│   ├── consent-basics.md
│   ├── privacy-policy-items.md
│   ├── oauth-social-login.md
│   └── minors.md
└── templates/                   # 승인 후 문서 생성용 템플릿
    ├── privacy-policy.md
    ├── terms-of-service.md
    └── consent-checkbox.md
```

- 참조 법령 버전은 [VERSION.md](VERSION.md)에서 확인하세요.
- 법 개정에 따른 변경 이력은 [CHANGELOG.md](CHANGELOG.md)에 있습니다.
- 법 개정을 발견하셨다면 [CONTRIBUTING.md](CONTRIBUTING.md)를 참고해서
  이슈를 남겨주세요.

## 한계

- 코드에서 확인 가능한 패턴만 탐지합니다. 오프라인 수집, 제3자와의 계약
  내용, 내부 운영 정책 등은 알 수 없습니다.
- 오탐(과잉 경고)이 있을 수 있습니다. 넓게 잡아내는 것을 목표로 하기
  때문에, MEDIUM 등급은 특히 사람이 직접 판단해야 합니다.
- 법은 계속 개정됩니다. [VERSION.md](VERSION.md)에 명시된 기준일 이후
  개정 사항은 반영되어 있지 않을 수 있습니다.

## 라이선스

[MIT](LICENSE)
