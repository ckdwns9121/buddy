---
description: Kill current buddy companion and reroll a completely new one (different species, rarity, stats). Use when you don't like your current buddy.
disable-model-invocation: true
---

# Buddy Kill & Reroll

현재 buddy를 제거하고 완전히 다른 companion을 뽑을 수 있게 합니다.

## 원리

Claude Code의 companion은 `hash(accountUuid + salt)`로 결정됩니다.
`accountUuid`를 랜덤 UUID로 교체하면 seed가 바뀌어 다른 종/희귀도가 나옵니다.
OAuth 인증 토큰은 별도 관리되므로 인증에 영향 없습니다.

## 실행 절차

### 1단계: 현재 buddy 확인

```bash
jq '{name: .companion.name, uuid: .oauthAccount.accountUuid}' ~/.claude.json
```

현재 companion 이름과 UUID를 사용자에게 보여줍니다.
companion이 없으면 "현재 buddy가 없습니다. `/buddy`로 먼저 뽑아주세요." 라고 안내하고 종료합니다.

### 2단계: 사용자 확인

사용자에게 "현재 buddy '{이름}'을(를) 보내고 새로운 companion을 뽑으시겠습니까?" 확인합니다.

### 3단계: 백업 + 리롤 실행

확인받으면 아래 명령어를 순서대로 실행합니다:

```bash
# 백업 생성
cp ~/.claude.json ~/.claude.json.bak

# companion 삭제 + accountUuid를 랜덤 UUID로 교체
jq --arg newuuid "$(uuidgen | tr '[:upper:]' '[:lower:]')" \
  'del(.companion) | del(.companionMuted) | .oauthAccount.accountUuid = $newuuid' \
  ~/.claude.json > /tmp/claude-reroll-tmp.json && mv /tmp/claude-reroll-tmp.json ~/.claude.json
```

### 4단계: 결과 안내

```bash
jq '.oauthAccount.accountUuid' ~/.claude.json
```

새 UUID를 보여주고 다음을 안내합니다:
- "UUID가 변경되었습니다."
- "Claude Code를 재시작한 후 `/buddy`를 실행하면 새로운 companion을 만날 수 있습니다."
- "문제 발생 시 복원: `cp ~/.claude.json.bak ~/.claude.json`"
- "마음에 들 때까지 `/buddy:kill`을 반복할 수 있습니다."
