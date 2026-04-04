---
name: buddy:kill
description: Kill current buddy and reroll a new one by refreshing the buddy seed UUID.
disable-model-invocation: true
---

# Buddy Kill & Reroll

현재 buddy를 정리하고, 다음 `/buddy`에서 새로운 buddy가 나오도록 리롤합니다.

## 원리

buddy는 `companionUserId + salt` 기반 seed로 결정됩니다.
이 스킬은 seed UUID를 새 값으로 갱신해서 다음 `/buddy` 결과가 바뀌도록 합니다.

## 실행 절차

### 1) 현재 상태 확인

```bash
jq '{name: .companion.name, accountUuid: .oauthAccount.accountUuid}' ~/.claude.json
```

`companion`이 없으면:
- "현재 buddy가 없습니다. `/buddy`로 먼저 뽑아주세요." 안내 후 종료

### 2) 사용자 확인

- "현재 buddy '{이름}'을(를) 정리하고, 새 buddy로 리롤할까요?"

### 3) 백업 + 실행

```bash
cp ~/.claude.json ~/.claude.json.bak

jq --arg newuuid "$(uuidgen | tr '[:upper:]' '[:lower:]')" '
  del(.companion)
  | del(.companionMuted)
  | .oauthAccount.accountUuid = $newuuid
' ~/.claude.json > /tmp/claude-reroll-tmp.json && mv /tmp/claude-reroll-tmp.json ~/.claude.json
```

### 4) 결과 안내

```bash
jq '{accountUuid: .oauthAccount.accountUuid, hasCompanion: (.companion != null)}' ~/.claude.json
```

안내:
- "리롤 준비 완료: seed UUID가 갱신되었습니다."
- "Claude Code 재시작 후 `/buddy`를 실행하면 새 buddy가 생성됩니다."
- "문제 시 복원: `cp ~/.claude.json.bak ~/.claude.json`"
