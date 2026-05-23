# skillctl — Hermes Agent Skill Manager

SQLite 기반 스킬 활성/보관 전환기. Hermes 컨텍스트 절약을 위해 사용하지 않는 스킬을 archive로 이동시키고, 필요시 즉시 재설치.

```bash
skillctl init        # 전체 스킬 인덱싱
skillctl search q    # 검색
skillctl install n   # archive → active
skillctl remove n    # active → archive
skillctl status      # 현황
skillctl list        # 활성 목록
```
