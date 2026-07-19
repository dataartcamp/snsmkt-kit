# AGENTS.md

브랜드보이스 기준 문서와 SNS 채널 카피를 만드는 스킬 2개(`expert-voice-builder`, `channel-copywriter`)를 담은 저장소. 실행 코드·빌드·테스트가 없고 편집 대상은 항상 프롬프트 텍스트다.

**정본 지침은 `CLAUDE.md`다.** 배경·편집 규칙 전체는 그 파일을 읽을 것. 아래는 그것만 지켜도 최악의 실패를 피할 수 있는 최소 규칙 발췌다.

---

<!-- 아래 블록은 CLAUDE.md 발췌 — 고칠 일이 생기면 CLAUDE.md도 함께 고칠 것 -->

**2중 미러링** — 같은 스킬이 하네스별 스캔 경로 2곳(`.claude/skills/` = Claude Code, `.agents/skills/` = Antigravity·Codex)에 바이트 단위로 동일하게 복제되어 있다. 한 곳만 고치면 하네스마다 다른 스킬이 도는 분기가 조용히 생긴다. 어떤 수정이든 2개 파일 전부에 반영하고, 해시로 동일성을 확인한 뒤 끝낸다.

```bash
for s in channel-copywriter expert-voice-builder; do
  md5sum .claude/skills/$s/SKILL.md .agents/skills/$s/SKILL.md
done
```

**Codex web 주의** — 위 경로는 Codex CLI 기준이다. Codex web(cloud)에서 스킬이 자동 로드되는지는 공식문서에 명시돼 있지 않다(cloud가 명시적으로 읽는 것은 `AGENTS.md`다). 근거와 판별법은 `CLAUDE.md`의 "하네스 스캔 경로 — 근거와 불확실성" 절에 있다.

**제목 계약** — `channel-copywriter`는 `expert-voice.md`의 섹션을 제목 이름으로 찾아 쓴다(`전문 분야`, `내 제안·행동유도(CTA)`, `내 말투 (보이스)`의 금지어). `expert-voice-builder`의 출력 템플릿 제목은 임의로 바꿀 수 없는 인터페이스이며, 한쪽만 바꾸면 런타임 에러 없이 카피 생성이 조용히 어긋난다.

<!-- 발췌 끝 -->
