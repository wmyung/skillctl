# skillctl — Hermes Context Saver

Hermes Agent 컨텍스트 절약을 위한 스킬 매니저.
사용하지 않는 스킬을 archive로 이동시켜 available_skills 목록에서 제거하고, 필요시 즉시 재설치한다.

```bash
skillctl init        # 전체 스킬 스캔 → SQLite 인덱싱
skillctl search q    # 키워드 검색
skillctl install n   # archive → skills/ (컨텍스트 복원)
skillctl remove n    # skills/ → archive (컨텍스트 제거)
skillctl status      # 활성/보관 현황
skillctl list        # 활성 목록
skillctl list --all  # 전체 목록
```
