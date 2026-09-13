# CLAUDE.md — LODESTAR 프로젝트 진입점

이 파일은 **Claude Code가 프로젝트를 열 때 자동으로 읽는 파일**입니다.
새 세션·다른 컴퓨터·아무 상황에서도 이 파일이 첫 진입점이 됩니다.

---

## 이 프로젝트가 뭐예요?

**LODESTAR** — 사용자가 AI(주로 Claude Code)로 만들고 있는 개인 게임 프로젝트.

- **장르**: Vampire Survivors-like 브라우저 게임 (뱀서라이크, bullet heaven)
- **컨셉**: 우주 광부. 사방에서 떠오는 광석(돌→은→금→백금→다이아→미스릴 코어) 채굴
- **스택**: 단일 `index.html` (Canvas 2D + vanilla JS + procedural Web Audio, **의존성 0**)
- **Live**: https://lodestar-lime-nine.vercel.app (옛 solaris 도메인은 v0.9에서 완전 삭제)
- **Repo**: https://github.com/Yun7420/lodestar (2026-09-11 리네임, 옛 solaris URL은 자동 리다이렉트)
- **분석**: Google Analytics 4 (`G-E6FJ175S19`, 속성명 GA4 UI에서 LODESTAR로 리네임 필요)
- **호스팅**: Vercel Hobby (GitHub `main` push 시 자동 재배포)

### 이름 히스토리
초기 명 `pest-hunter` (해충 박멸 컨셉) → `SOLARIS` (우주 방위대) → **`LODESTAR`** (우주 광부). 리브랜드 이유는 컨셉 전환. v0.8 시점 완전 통일.

---

## 세션 시작 시 반드시 할 일

새 세션이거나 다른 컴퓨터라면 **반드시 순서대로 읽으세요**:

1. **`docs/AI_HANDOFF.md`** — 컨셉·유저 취향·코드 지도·결정 이력. 이걸 안 읽으면 이전 세션 결정을 되돌리게 됩니다.
2. **`docs/ROADMAP.md`** — 여태 한 작업, 앞으로 할 작업, 우선순위.
3. 필요 시 `index.html` grep해서 구체 코드 확인.

---

## 절대 지킬 것 (유저 취향 요약)

전체 취향은 `docs/AI_HANDOFF.md §6`에 있음. 핵심만:

- **미니멀**: 롱카피·장식적 명명 금지. "선체 강화" X → "체력" O.
- **세련됨**: 아기같음·이모지 남발 X. 딥 네이비 + 시안/바이올렛/앰버 팔레트 유지.
- **자동 + 액티브 혼합**: 무기 자동 발사가 기본, SPACE/좌클릭은 기본 공격(채굴 빔) 예외.
- **한국어 존댓말**, 짧고 단호하게. 결과·트레이드오프만 간결히.
- **매번 확인 남발 금지**: 방향 명확하면 바로 실행, 결과·스크린샷으로 확인 받기.
- **이모지**: 사용자 요청 없으면 UI/코드/텍스트에 남발 금지.
- **주석**: 이유가 비자명할 때만. WHAT은 쓰지 말고 WHY만.

---

## 작업 완료 후

의미 있는 변경을 했으면 **반드시 `docs/ROADMAP.md` 갱신**:
- 완료된 항목 체크
- 새로 발견한 이슈·아이디어 추가
- 다음 우선순위 조정

큰 변화(버전 릴리스, 아키텍처 변경)는 `docs/AI_HANDOFF.md §11 변경 이력`에도 기록.

---

## 자주 쓰는 경로

이 맥 기준 로컬 경로:
```
/Users/hansang-yun/Documents/한상윤/project/lodestar/
```

다른 컴퓨터에서는:
```bash
git clone https://github.com/Yun7420/lodestar.git
```

---

## 실행

빌드 없이 `index.html` 더블클릭으로 실행. Playwright 등 로컬 서버 필요 시:
```bash
python3 -m http.server 8765
# http://localhost:8765/
```

배포는 GitHub `main`에 push하면 Vercel이 자동으로 반영 (~30초).
