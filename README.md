# skillctl — Hermes Context Saver 🧠⚡

**Hermes Agent의 available_skills 목록에서 안 쓰는 스킬을 제거 → 컨텍스트 확보 → 필요한 순간 즉시 복원.**

Hermes Agent는 세션 시작 시 모든 SKILL.md의 name/description을 시스템 프롬프트에 포함합니다. 스킬이 300+개면 수십 KB가 컨텍스트를 차지합니다. skillctl은 **쓰는 스킬만 활성화**하고 나머지는 archive로 빼서, 모델이 진짜 필요한 정보에 집중하게 합니다.

## Features

- **SQLite 기반 인덱싱** — name, description, tags 전체 검색
- **즉시 설치/제거** — archive → `skills/` (파일 복사, 컨텍스트 복원)
- **0 dependencies** — Python 3 stdlib만 사용
- **Enhancer 연동 가능** — `memory_enhancer_search()`와 함께 사용

## Quickstart

```bash
skillctl init                    # 처음 한 번: 전체 스킬 인덱싱
skillctl status                  # 활성 257 / 보관 125
skillctl search "postgwas"       # 검색
skillctl remove daiso-search     # archive로 이동 (컨텍스트에서 제거)
skillctl install daiso-search    # 다시 활성화 (컨텍스트 복원)
skillctl list --all              # 전체 목록
```

## Why

| 상황 | before | after |
|---|---|---|
| 전체 스킬 | 382 | 382 (총량 동일) |
| 시스템 프롬프트 노출 | 382개 설명 포함 | **활성 스킬만** |
| 사용자 컨텍스트 점유 | ~50KB | **~25KB** |
| 필요할 때 접근 | 항상 떠있음 | `skillctl search` → `install` 5초 |

## Install

```bash
curl -O https://raw.githubusercontent.com/wmyung/skillctl/main/skillctl
chmod +x skillctl
./skillctl init
```

Requires: Python 3.8+, `~/.hermes/skills/` 디렉토리 (Hermes Agent)

## Keywords

`hermes-agent` `skill-manager` `context-optimization` `prompt-compression` `available-skills` `sqlite` `agent-tooling` `컨텍스트절약` `스킬관리`
