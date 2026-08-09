# CHANGELOG

이 파일은 스킬 자체의 변경 이력과, 법 개정에 대응해 rules/templates를 갱신한 이력을
함께 기록합니다. 날짜는 실제 작업을 수행한 날짜입니다.

## [Unreleased]

- 2026-09-11 시행 예정인 개인정보 보호법 개정안 반영 필요 (자세한 내용은
  [VERSION.md](VERSION.md) 참고). 시행 후 rules/*.md 전체 재검토 예정.

## 2026-08-09

- 최초 릴리스.
- 참조 법령: 개인정보 보호법 (법률 제20897호, 2025.4.1. 일부개정, 2025.10.2. 시행)
- rules/consent-basics.md, rules/privacy-policy-items.md, rules/oauth-social-login.md,
  rules/minors.md 작성.
- templates/privacy-policy.md, templates/terms-of-service.md,
  templates/consent-checkbox.* 작성.
- SKILL.md 스캔 로직(자동 스택 판단, 심각도 구분, 2단계 워크플로우) 작성.
