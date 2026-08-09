# 동의 체크박스 UI 템플릿

> Claude Code가 스캔에서 발견한 실제 수집 항목을 바탕으로 아래 컴포넌트
> 예시 중 프로젝트 스택에 맞는 것을 골라 채워 넣는 용도입니다. 기존 회원가입
> 폼 구조를 깨뜨릴 수 있으니, 실제로 코드에 삽입하기 전에 어디에 넣을지
> 사용자에게 확인하세요.

## 설계 원칙 (자세한 법적 근거는 [../rules/consent-basics.md](../rules/consent-basics.md) 참고)

1. **전체 동의와 개별 동의를 함께 제공한다.** "전체 동의합니다" 체크박스로
   한 번에 몰아 처리하더라도, 각 항목(이용약관, 개인정보 수집·이용,
   마케팅 수신 등)을 개별적으로도 동의/해제할 수 있어야 한다.
2. **필수 동의와 선택 동의를 명확히 구분해서 표시한다.** 선택 동의를
   거부해도 서비스 이용(회원가입)이 가능해야 하며, 이를 코드/UI에서
   강제로 막으면 안 된다.
3. **각 동의 항목은 무엇에 동의하는지 요약 + 자세히 보기 링크를 함께
   제공한다.** 체크박스 라벨만으로 무엇에 동의하는지 알 수 있어야 한다.
4. 마케팅/광고성 정보 수신 동의는 별도의 선택 항목으로 분리한다 (필수
   동의에 묶으면 안 된다).
5. OAuth로 받은 정보를 로그인 목적 외로 쓴다면(마케팅, 분석 등) 그 목적을
   별도 동의 항목으로 분리한다 — [../rules/oauth-social-login.md](../rules/oauth-social-login.md) 참고.

아래 예시의 `{{항목_목록}}`은 스캔에서 실제로 발견된 수집 항목으로 채운다.
가짜 항목을 넣지 말 것.

---

## React (JSX)

```tsx
import { useState } from "react";

type ConsentState = {
  all: boolean;
  terms: boolean; // 필수: 이용약관 동의
  privacy: boolean; // 필수: 개인정보 수집·이용 동의
  marketing: boolean; // 선택: 마케팅 정보 수신 동의
  {{#if MINORS_POSSIBLE}}
  guardian: boolean; // 만 14세 미만: 법정대리인 동의 확인
  {{/if}}
};

const initialState: ConsentState = {
  all: false,
  terms: false,
  privacy: false,
  marketing: false,
  {{#if MINORS_POSSIBLE}}
  guardian: false,
  {{/if}}
};

export function ConsentCheckboxGroup({
  value,
  onChange,
}: {
  value: ConsentState;
  onChange: (next: ConsentState) => void;
}) {
  const requiredKeys: (keyof ConsentState)[] = [
    "terms",
    "privacy",
    {{#if MINORS_POSSIBLE}}"guardian",{{/if}}
  ];
  const canSubmit = requiredKeys.every((k) => value[k]);

  const toggleAll = (checked: boolean) => {
    const next = { ...value, all: checked };
    (Object.keys(next) as (keyof ConsentState)[]).forEach((k) => {
      if (k !== "all") next[k] = checked;
    });
    onChange(next);
  };

  const toggleOne = (key: keyof ConsentState, checked: boolean) => {
    const next = { ...value, [key]: checked };
    const allChecked = requiredKeys
      .concat(["marketing"])
      .every((k) => next[k]);
    next.all = allChecked;
    onChange(next);
  };

  return (
    <fieldset>
      <label>
        <input
          type="checkbox"
          checked={value.all}
          onChange={(e) => toggleAll(e.target.checked)}
        />
        전체 동의합니다
      </label>

      <label>
        <input
          type="checkbox"
          checked={value.terms}
          onChange={(e) => toggleOne("terms", e.target.checked)}
        />
        [필수] 이용약관 동의{" "}
        <a href="{{이용약관_링크}}" target="_blank" rel="noreferrer">
          보기
        </a>
      </label>

      <label>
        <input
          type="checkbox"
          checked={value.privacy}
          onChange={(e) => toggleOne("privacy", e.target.checked)}
        />
        [필수] 개인정보 수집·이용 동의 ({{항목_목록}}){" "}
        <a href="{{처리방침_링크}}" target="_blank" rel="noreferrer">
          자세히 보기
        </a>
      </label>

      <label>
        <input
          type="checkbox"
          checked={value.marketing}
          onChange={(e) => toggleOne("marketing", e.target.checked)}
        />
        [선택] 마케팅 정보 수신 동의
      </label>

      {{#if MINORS_POSSIBLE}}
      <label>
        <input
          type="checkbox"
          checked={value.guardian}
          onChange={(e) => toggleOne("guardian", e.target.checked)}
        />
        [필수] 만 14세 미만 아동입니다 — 법정대리인 동의 절차로 이동합니다
      </label>
      {{/if}}

      <button type="submit" disabled={!canSubmit}>
        가입하기
      </button>
    </fieldset>
  );
}
```

## Vue 3 (Composition API)

```vue
<script setup lang="ts">
import { reactive, computed } from "vue";

const consent = reactive({
  all: false,
  terms: false, // 필수
  privacy: false, // 필수
  marketing: false, // 선택
});

const requiredKeys = ["terms", "privacy"] as const;
const canSubmit = computed(() => requiredKeys.every((k) => consent[k]));

function toggleAll(checked: boolean) {
  consent.all = checked;
  consent.terms = checked;
  consent.privacy = checked;
  consent.marketing = checked;
}

function syncAll() {
  consent.all = requiredKeys.concat(["marketing"] as const).every(
    (k) => consent[k]
  );
}
</script>

<template>
  <fieldset>
    <label>
      <input type="checkbox" v-model="consent.all" @change="toggleAll(consent.all)" />
      전체 동의합니다
    </label>
    <label>
      <input type="checkbox" v-model="consent.terms" @change="syncAll" />
      [필수] 이용약관 동의
      <a href="{{이용약관_링크}}" target="_blank">보기</a>
    </label>
    <label>
      <input type="checkbox" v-model="consent.privacy" @change="syncAll" />
      [필수] 개인정보 수집·이용 동의 ({{항목_목록}})
      <a href="{{처리방침_링크}}" target="_blank">자세히 보기</a>
    </label>
    <label>
      <input type="checkbox" v-model="consent.marketing" @change="syncAll" />
      [선택] 마케팅 정보 수신 동의
    </label>
    <button type="submit" :disabled="!canSubmit">가입하기</button>
  </fieldset>
</template>
```

## Plain HTML + vanilla JS

```html
<fieldset id="consent-group">
  <label>
    <input type="checkbox" id="consent-all" />
    전체 동의합니다
  </label>
  <label>
    <input type="checkbox" class="consent-required" id="consent-terms" />
    [필수] 이용약관 동의
    <a href="{{이용약관_링크}}" target="_blank">보기</a>
  </label>
  <label>
    <input type="checkbox" class="consent-required" id="consent-privacy" />
    [필수] 개인정보 수집·이용 동의 ({{항목_목록}})
    <a href="{{처리방침_링크}}" target="_blank">자세히 보기</a>
  </label>
  <label>
    <input type="checkbox" id="consent-marketing" />
    [선택] 마케팅 정보 수신 동의
  </label>
  <button type="submit" id="submit-btn" disabled>가입하기</button>
</fieldset>

<script>
  const group = document.getElementById("consent-group");
  const all = document.getElementById("consent-all");
  const required = group.querySelectorAll(".consent-required");
  const marketing = document.getElementById("consent-marketing");
  const submitBtn = document.getElementById("submit-btn");
  const every = document.querySelectorAll(
    "#consent-group input[type=checkbox]:not(#consent-all)"
  );

  function updateSubmit() {
    submitBtn.disabled = ![...required].every((c) => c.checked);
  }

  all.addEventListener("change", () => {
    every.forEach((c) => (c.checked = all.checked));
    updateSubmit();
  });

  [...every].forEach((c) =>
    c.addEventListener("change", () => {
      all.checked = [...every].every((x) => x.checked);
      updateSubmit();
    })
  );
</script>
```

---

> ⚠️ 위 코드는 초안 예시이며, 실제 프로젝트의 폼 라이브러리(react-hook-form,
> formik, VeeValidate 등)나 상태 관리 방식에 맞게 조정이 필요할 수 있습니다.
> 법률 전문가의 검토를 권장합니다.
