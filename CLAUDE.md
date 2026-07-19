# CLAUDE.md

## 이 저장소의 구성

Agent Skills 표준(`SKILL.md`)으로 만든 SNS 카피 파이프라인 2단계다.

- **`expert-voice-builder`** — 사용자를 인터뷰해 브랜드보이스 기준 문서 `expert-voice.md`를 만든다.
- **`channel-copywriter`** — 그 기준 문서로 Threads·LinkedIn 카피 파일을 만든다.

의존은 단방향이다. `channel-copywriter`는 `expert-voice.md` 없이도 동작하며, 파일이 없다는 이유로 스킬을 건너뛰지 않는다.

## 3중 미러링 — 이 저장소의 핵심 제약

같은 스킬이 하네스별 스캔 경로 3곳에 **바이트 단위로 동일하게** 복제되어 있다.

| 경로 | 하네스 |
|---|---|
| `.claude/skills/<skill>/SKILL.md` | Claude Code |
| `.agent/skills/<skill>/SKILL.md` | Antigravity |
| `.codex/skills/<skill>/SKILL.md` | Codex CLI |

한 곳만 고치면 하네스마다 다른 스킬이 도는 분기가 조용히 생긴다. **어떤 수정이든 3개 파일 전부에 반영하고, 해시로 동일성을 확인한 뒤 끝낸다.**

```powershell
foreach ($s in @('channel-copywriter','expert-voice-builder')) {
  Get-FileHash ".claude\skills\$s\SKILL.md", ".agent\skills\$s\SKILL.md", ".codex\skills\$s\SKILL.md" `
    -Algorithm MD5 | Format-Table Hash, Path
}
```

```bash
for s in channel-copywriter expert-voice-builder; do
  md5sum .{claude,agent,codex}/skills/$s/SKILL.md
done
```

각 스킬의 3줄이 같은 해시면 통과. 이것이 이 저장소의 검증 절차다.

수정은 한 파일에서 하고 **파일 복사로 나머지 두 곳에 전파한다.** 세 파일을 각각 에디터로 열어 저장하면 줄바꿈(CRLF/LF)이 달라져 내용이 같아도 해시가 어긋난다.

체크아웃 쪽 줄바꿈은 `.gitattributes`(`*.md text eol=lf`)가 막아준다. 이 PC는 `core.autocrlf=true`라 그것이 없으면 클론할 때마다 파일이 CRLF로 바뀌어, 위 해시가 기기마다 달라지고 검증이 재현되지 않는다. **`.gitattributes`를 지우면 이 저장소의 검증 절차가 무력해진다.**

각 SKILL.md 맨 끝 "참고" 절이 자기 폴더의 세 경로를 문자열로 적어둔다. 스킬을 추가·개명하면 그 문장도 함께 고친다.

## 두 스킬을 잇는 계약 — 제목 문자열

`channel-copywriter`는 `expert-voice.md`의 섹션을 **제목 이름으로 찾아 쓴다** — `전문 분야`(해시태그에 반영), `내 제안·행동유도(CTA)`, `내 말투 (보이스)` 안의 금지어.

따라서 `expert-voice-builder`의 출력 템플릿 제목은 임의로 바꿀 수 없는 인터페이스다. 한쪽만 바꾸면 런타임 에러 없이 카피 생성이 조용히 어긋난다. 제목을 손댈 일이 생기면 양쪽 스킬을 함께 고친다.

## 프롬프트 편집 시 지키는 것

| 대상 | 규칙 |
|---|---|
| frontmatter `description` | 트리거 사양이다. 한국어 발화 예시 목록이 곧 라우팅 규칙이고, 두 스킬의 description은 서로를 지목해 오발동을 막는다. 예시를 지우면 트리거 정확도가 떨어진다 |
| 사실 날조 금지 조항 | 사용자가 말하지 않은 경력·숫자·성과를 지어내지 말라는 지시가 양쪽 스킬에 있다. 축약·정리 대상이 아니다 |
| 채널 규격 수치 | Threads 500자, LinkedIn 1,300~1,900자 등은 플랫폼 규격이다. 근거 없이 조정하지 않는다 |
| SKILL.md의 자체 완결성 | 이 저장소의 폴더 구조·관리 규칙을 SKILL.md 본문에 넣지 않는다. 스킬은 어디에 복사되든 그 자체로 동작해야 한다 |

## 문서 구조

| 파일 | 독자 | 성격 |
|---|---|---|
| `CLAUDE.md` | Claude Code | **정본** |
| `AGENTS.md` | Codex CLI | 포인터 + 최소 규칙 발췌 |
| `README.md` | 사람 | 사용 안내 |

`AGENTS.md`를 전체 복제하지 않는 이유: 스킬 미러는 해시로 기계 검증되지만 산문 지침에는 그런 수단이 없다. 복제 범위를 눈으로 대조 가능한 몇 줄로 묶어두기 위해 발췌만 둔다. 부실해 보인다고 전체 복제로 되돌리지 말 것.

## Git

원격은 `https://github.com/dataartcamp/snsmkt-kit.git`, 기본 브랜치는 `main`이다. **공개 저장소**이며 수강생은 README 안내대로 `Code → Download ZIP`으로 받는다.

`SKILL.md`를 커밋하기 전에 위 해시 검증을 통과시킨다. 미러가 어긋난 상태의 커밋은 만들지 않는다.

스킬 실행 산출물(`expert-voice.md`, `threads-copy.md`, `linkedin-copy.md`)은 저장소 루트에 생성되며 `.gitignore`로 제외되어 있다. 배포본에 테스트 산출물이 섞이지 않게 하려는 것이므로 이 항목을 지우지 않는다.

### 커밋 신원 — 반드시 프로젝트 레벨로

이 저장소의 계정은 `dataartcamp <dataartcamp@gmail.com>`이며 `--local`로 설정되어 있다.

작업 PC는 **전역 git 신원이 비어 있고 `user.useConfigOnly=true`가 켜져 있다.** 프로젝트마다 계정이 달라, 전역 폴백이 남아 있으면 다른 계정으로 조용히 커밋되기 때문이다. 그 결과:

- 이 저장소를 **새로 클론하면 첫 커밋이 `Author identity unknown`으로 중단된다.** 정상 동작이다.
- 해결은 `--local` 설정이며, **전역 설정으로 우회하지 않는다.**

```bash
git config --local user.name "dataartcamp"
git config --local user.email "dataartcamp@gmail.com"
```

커밋 후 `git log --pretty='%an <%ae>' -1`로 실제 귀속을 확인한다. 커밋 신원은 푸시 인증(Windows 자격 증명 관리자)과 별개다.
