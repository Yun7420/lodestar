# LODESTAR · AI 인계 문서

> 이 문서는 **다음 AI 세션이 이 프로젝트를 이어받아 작업할 수 있도록** 만든 인계 문서입니다.
> 진입점은 프로젝트 루트의 `CLAUDE.md`. 이 문서는 그 다음으로 읽는 상세본입니다.
> 로드맵·작업 목록은 `ROADMAP.md` 참조.

---

## 1. 한 줄 요약

브라우저에서 바로 돌아가는 **우주 광부 컨셉 뱀서라이크** 게임. 몰려오는 광석(돌→은→금→백금→다이아→미스릴 코어)을 자동 무기로 채굴, 크레딧으로 영구 강화. 미니멀·세련 톤, 단일 HTML 파일, 빌드/의존성 없음.

- **게임명**: LODESTAR
- **현재 버전**: v0.8
- **Live**: Vercel 리네임 대기 (구 URL `https://solaris-lime-nine.vercel.app` — 사용자가 Vercel 대시보드에서 `lodestar`로 리네임하면 새 URL 발급)
- **Repo**: https://github.com/Yun7420/lodestar (2026-09-11 리네임, 옛 solaris URL은 GitHub이 자동 리다이렉트)
- **GA4**: `G-E6FJ175S19` (속성명은 GA4 UI에서 `LODESTAR`로 리네임 필요, 측정 ID와 데이터는 유지)

---

## 2. 유저 프로필 & 커뮤니케이션 규칙

- **한국어** 사용. 존댓말 톤. 짧고 단호하게.
- 프론트엔드 개발자. 코드 이해도 있음, 하지만 상세한 로직 설명은 원하지 않음. **결과물과 트레이드오프만** 간결히.
- 결정 요구 시 `AskUserQuestion`으로 3~4개 옵션 (첫 옵션에 "(추천)").
- 사용자가 방향을 명확히 지시하면 확인 없이 실행. 확인 남발 금지.
- 이모지 사용 최소화. 사용자 요청 없으면 UI에도 이모지 넣지 말 것.
- 사용자 이메일: `sy.han@rtmc-kr.com` (RTMC 재직, 이건 개인 프로젝트).

---

## 3. 프로젝트 컨셉 (현재 확정)

### 게임 제목
**LODESTAR** (한 단어). "lode" = 광맥, "star" = 별. 두 뜻 이중 은유. 이전 이름 `pest-hunter` → `SOLARIS` → **`LODESTAR`**. 컨셉 전환 때마다 리네임.

### 장르
Vampire Survivors-like (bullet heaven) + **우주 광부 서사** — 뇌빼고 하는 도파민 게임. 이동만 하면 자동 무기가 광석을 채굴, 크레딧 축적.

### 핵심 루프
```
이동 → 자동 무기가 광석 채굴 → 크레딧·XP 획득 → 레벨업(무기 강화) → 반복
        ↓
     사망 시
        ↓
금고에 크레딧 저장 → 영구 강화 구매 → 다음 판에서 더 강해짐
```

### 미학
- **미니멀 · 사이파이 · 세련됨** (촌스럽거나 아기같은 스타일 금지)
- 팔레트: 딥 네이비(#04060d) + 시안(#4de1ff) + 바이올렛(#a06bff) + 앰버(#f0b448, currency만)
- 폰트: 시스템 산세리프 + "SF Mono" 모노스페이스 (숫자)
- 캐릭터: **삼각 우주선이 아니라 순수 빛의 오브**로 표현 (breathing glow + 궤도 스파크 2개)
- UI: 라운드 최소화(4~6px), 대문자·자간 넓힘·모노스페이스 숫자

---

## 4. 게임 시스템 상세

### 조작
- **WASD / 화살표**: 이동 (8방향)
- **SPACE**: 기본 공격 (마지막 이동 방향으로 발사)
- **좌클릭**: 기본 공격 (마우스 방향으로 발사)
- 기본 공격 쿨다운: 0.24s
- 자동 무기는 별개로 계속 발사됨

### 무기 (5종, 각 6레벨 만렙) - `WEAPONS` 객체
| key | 이름 | 동작 | baseDmg | baseCd |
|-----|------|------|---------|--------|
| `orbital` | 오빗 | 플레이어 주변 회전 근접 | 12 | 0.9s |
| `plasma` | 플라즈마 | 이동 방향 전방 직선 발사 | 10 | 0.55s |
| `emp` | 펄스 | 주변 광역 링(주기적 폭발) | 20 | 2.2s |
| `missile` | 미사일 | 가까운 적 유도 아크 폭발 | 40 | 2.6s |
| `drone` | 드론 | 궤도 자동 요격 | 8 | 0.7s |

시작 시 항상 `orbital` 지급. `startWeap` 메타 업그레이드로 추가 무기 시작 가능(최대 +2).

### 패시브 (6종, 각 5레벨) - `PASSIVES` 객체
부스트(속도) / 실드(HP+15) / 자석(픽업+25%) / 럭(골드+15%) / 파워(공격+10%) / 헤이스트(쿨다운-8%)

### 광석 티어 (6단계) - `ENEMY_DEFS` 객체
낮은 티어는 흔하고 약함, 높은 티어는 희귀하고 튼튼·가치 높음. 모두 플레이어에게 접근하며 부딪히면 데미지(뱀서라이크 유지).

| key | 이름 | 크기 | HP | 속도 | 데미지 | 크레딧 | XP | 색상 |
|-----|------|-----|-----|------|-------|-------|-----|------|
| `pebble`   | 돌         | 16 | 8   | 70  | 5  | 1   | 1  | 갈색 |
| `silver`   | 은 원석    | 22 | 20  | 60  | 8  | 3   | 2  | 실버 |
| `gold`     | 금 원석    | 24 | 36  | 55  | 12 | 8   | 3  | 딥골드 |
| `platinum` | 백금 원석  | 26 | 60  | 75  | 14 | 16  | 4  | 푸른흰 |
| `diamond`  | 다이아 원석 | 28 | 90  | 85  | 18 | 28  | 6  | 청백 |
| `mithril`  | 미스릴 코어(보스) | 68 | 800 | 45 | 30 | 200 | 50 | 발광 청록 |

**모든 광석은 캔버스 벡터로 직접 그림** — 이모지 사용 금지. `drawEnemy()` → 타입별 draw 함수 라우팅.
- `drawPebble`: 갈색 다각형 (결정 없음)
- `drawSilverOre` / `drawGoldOre` / `drawPlatinumOre`: 회색 암반 + `drawCrystals` 헬퍼(3~4개 결정)
- `drawDiamondOre`: 중앙 큰 결정 + 스파클 애니메이션
- `drawMithrilCore`: 발광 결정 클러스터 (중앙 큰 결정 + 6개 주변)

### 스폰
- 첫 60초: `pebble` 위주 + `silver` 소량
- 30s부터 `silver` 증가, 90s부터 `gold` 등장, 150s `gold` 안정, 210s `platinum`, 300s+ `diamond`
- HP 스케일링: `1 + time/90`
- 스폰 속도: `1.2 + time/25` 마리/초
- **3분(180s)마다 미스릴 코어 등장** → "▲ MITHRIL CORE" 텍스트 + 화면 흔들림
- 처치 시 "CORE MINED" + 크레딧 폭포 + 슬로우모

### 영구 강화 (SHOP_ITEMS)
체력(+20HP) / 공격(+5%DMG) / 속도(+3%SPD) / 골드(+10%) / 자석(+25%픽업) / 쿨다운(-3%) / 부활(+1) / 장비(+1 시작 무기)

### 개인 기록
`META.best = { time, kills, gold }` — 사망 시 갱신, 메뉴 상단에 표시. 갱신 시 게임오버 화면에 "▲ NEW BEST" 배지.

---

## 5. 코드 지도 (index.html 내부)

파일 하나에 전부 인라인 (HTML + CSS + JS). 섹션 구분자 주석 참고. 정확한 라인은 파일이 바뀌면 어긋날 수 있으니 grep 확인 우선.

```
Line ~1-200       HTML: overlays (menu, levelup, gameover), HUD, canvas, styles
                  GA4 gtag 스크립트도 <head> 안에 있음
Line ~210-270     Audio: procedural Web Audio SFX (beep + noiseBurst 조합)
Line ~275-330     Meta state: DEFAULT_META, loadMeta/saveMeta, metaMul()
Line ~335-380     WEAPONS / PASSIVES 정의
Line ~382-396     ENEMY_DEFS 정의
Line ~400-460     Run state: state 스키마, starfield/nebula 초기화, newRunState()
Line ~465-510     Input: keys, mouse, event handlers
Line ~515-570     Helpers: dist/randRange/spawnFloatText/spawnSparks/spawnDebris/shake/combo
Line ~575-630     Spawning: spawnEnemyAt/spawnAtEdge/currentSpawnMix/updateSpawner
Line ~635-710     Weapon stats: weaponStats(key) — level별 스탯 계산
Line ~712-770     Basic attack: fireBasicAttack() (v0.4 신규 - SPACE/click)
Line ~772-840     Fire weapons: fireWeapon(key, stats)
Line ~845-990     Update loop: update(dt) — 이동, 몹 AI, 발사체, 이펙트, 픽업, 레벨업
Line ~995-1030    Damage/Kill: damageEnemy() / killEnemy()
Line ~1035-1100   Level up: triggerLevelUp() / pickLevelUpChoices() / applyChoice()
Line ~1105-1260   Drawing primitives: drawOrb (player), drawRockyBody+drawCrystals 헬퍼,
                  drawPebble/drawSilverOre/drawGoldOre/drawPlatinumOre/drawDiamondOre/drawMithrilCore,
                  drawEnemy 라우터
Line ~1265-1360   Projectile draws: drawProjSatellite/Plasma/Missile/Drone + 라우터
Line ~1365-1430   Icons for menu: drawIcon()
Line ~1435-1560   Render: render() — 배경, 성운, 별밭, 픽업, 몹, 발사체, 플레이어, 이펙트, 비네트
Line ~1565-1630   HUD/Shop/Menu/Death: updateHUD/onDeath/backToMenu/renderShop/buyUpgrade
Line ~1635-1690   Start/Loop/Fit: startGame() / loop() / fit()
```

**주요 좌표계**: 게임 로직은 무한 월드 좌표. 렌더 시 `state.camera` 뺀 값으로 변환. `cx = -(cam.x + shake)`, `cy = -(cam.y + shake)` 후 `ctx.translate` 활용.

---

## 6. 유저 피드백 & 결정 이력 (시간 순)

이 순서대로 **사용자 취향을 학습**한 결과입니다. 앞으로 결정 내릴 때 참고하세요.

1. **"뭔가를 사냥하는 느낌, 뱀서처럼"** → 방향 확정 (돈 벌면서 사냥)
2. **"유니크하고 SNS 바이럴"** → 초기엔 "해충 박멸" 컨셉으로 시작
3. **"뇌빼고 중독적"** → VS-like 코어 루프 유지, 조작 이동만
4. **"최소 2.5D/3D"** → 잠깐 Three.js로 갈아엎었다가
5. **"뇌빼고엔 2D가 낫나?"** → 유저가 재확인. **2D 유지 + 이펙트 강화 채택**
6. **"우주 배경, 행성이 오는 거"** → 해충→우주로 컨셉 전환 (v0.3)
7. **"중앙 캐릭터가 별로"** → 이모지 삼각선→ 삼각 우주선→ 순수 빛 오브로 진화 (v0.4)
8. **"너무 촌스럽다, 이름들이 (태양계 방위대 등)"** → 미니멀 리네이밍 (v0.4)
9. **"공격/방어/스피드 이런 느낌으로 간단하게"** → 강화 이름 한 단어씩 (체력/공격/속도/…)
10. **"우주선 너무 아기같아, 차라리 빛으로만"** → drawOrb 도입 (v0.4)
11. **"SPACE or 좌클릭 기본 공격"** → 액티브 공격 추가 (v0.4)
12. **"유저가 즐거워지고 많아지면 랭킹까지"** → 로컬 personal best 저장, 서버 확장 여지 남김
13. **"어릴 때 우산 게임 (비 맞으면 돈)"** → 게임 디자인 철학 공감. "단순한 행동 → 자연스러운 보상 → 숫자 성장"이 핵심이라는 상호 이해.
14. **"React가 아니라 HTML? Unity로 갈 예정 아니었나?"** → HTML5 Canvas가 웹게임 정답이고 Unity는 스팀 갈 때 결정. 이식 아닌 재사용 가능한 구조. (설명 완료)
15. **"베타인데 티스토리/Vercel 어떤 방향?"** → Vercel + GA4 먼저, 개발일지는 데이터 쌓인 뒤. Velog가 개발자 유입에 유리.
16. **"컴퓨터 회사꺼라 이식성 필요"** → 이 문서 구조 도입 (v0.7)
17. **"우주 광석 채굴 컨셉으로 (돌→은→금)"** → 방향성 자체 전환. 뱀서 코어(공격/방어) 유지하고 몹만 광석 티어로 재정의 결정. (v0.8)
18. **"우주 광석 채굴은 너무 없어보이고 / 다른 게임과 겹치지 않을 독특한 이름"** → 조어·희소단어 위주로 LODESTAR 채택 (실제 영단어 lode+star, 게임명 거의 안 쓰임). (v0.8)
19. **"인프라도 다 리네임"** → GitHub 리포·Vercel·GA4·로컬 폴더·localStorage 키까지 SOLARIS→LODESTAR 통일. (v0.8)

### 유저의 정성적 취향 (반드시 지킬 것)
- **미니멀**: 롱카피/장식적 명명 금지. "선체 강화" X → "체력" O.
- **세련됨**: 아기같음/이모지 남발 X. 미니멀 UI, 딥한 컬러.
- **자동화**: 조작 최소화. 무기 자동. 다만 액티브 여지는 남김(SPACE/클릭).
- **성장 가시성**: 숫자가 커지는 즐거움. 콤보, 데미지 넘버, 크레딧 축적.
- **확장 여지**: 나중에 랭킹/커뮤니티 붙일 수 있는 방향으로 데이터 저장.
- **작업 흐름**: 완벽한 결과보다 반복 개선 선호. 매번 확인받지 말고 만들고 스크린샷 공유.

---

## 7. 현재 알려진 이슈 · 미비점

- **작은 이슈**: `triggerLevelUp` 내부 카드 렌더 코드가 지저분함 (icon wrap 이중 처리, 작동은 함). 정리해도 좋음.
- **작은 이슈**: 마우스 aim 인디케이터 없음 (조준선/십자선 추가하면 클릭 공격 피드백 향상)
- **작은 이슈**: 기본 공격에 관성이나 반동 이펙트 없음 (액션감 강화 여지)
- **작은 이슈**: favicon 없음 (콘솔에 404 계속 뜸, 시각적 영향은 없음)
- **밸런스**: 30분 정식 플레이 안 해봄. 몹 HP·업그레이드 코스트 아직 튜닝 안 됨.
- **SEO**: `<meta description>`, OG image 등 소셜 카드 없음. 공유 시 미리보기 없음.

---

## 8. 실행 방법

### 로컬 실행
1. 파인더에서 `index.html` 더블클릭
2. 또는 터미널: `open "/Users/hansang-yun/Documents/한상윤/project/lodestar/index.html"`
3. 사운드가 안 나오면 브라우저 정책 → 아무 키/클릭 한 번이면 켜짐

### 로컬 서버로 실행 (Playwright 테스트용)
```bash
cd "/Users/hansang-yun/Documents/한상윤/project/lodestar"
python3 -m http.server 8765
# 브라우저에서 http://localhost:8765/
```

### 다른 컴퓨터에서 이어받을 때
```bash
git clone https://github.com/Yun7420/lodestar.git
cd lodestar
# CLAUDE.md 자동 로드됨. docs/AI_HANDOFF.md 읽고 이어감.
```

### AI 세션에서 테스트하기
- Playwright MCP 사용. `file://` 프로토콜은 차단되므로 반드시 로컬 서버 경유.
- 끝나면 `pkill -f "http.server 8765"`로 정리.

### 배포
- **자동**: `git push` (main 브랜치) → Vercel이 30초 안에 재배포
- 수동 배포 불필요

---

## 9. 서비스 계정 정보

이건 로그인 방법이지 인증 정보가 아님. 인증은 사용자님이 직접.

| 서비스 | 계정 | 용도 | 접근 |
|--------|------|------|------|
| GitHub | Yun7420 | 소스 저장, 자동 배포 트리거 | https://github.com/Yun7420/lodestar |
| Vercel | GitHub OAuth (yun7420) | 자동 배포·호스팅 | https://vercel.com (프로젝트명 리네임 대기) |
| Google Analytics | (사용자님 Google 계정) | 유저 계측 | https://analytics.google.com — 속성명 리네임 대기 |

**측정 ID**: `G-E6FJ175S19` (index.html에 하드코딩됨, 리브랜드 후에도 유지)

**복구 코드 저장 위치** (이 맥 기준):
- `~/Documents/한상윤/깃허브/Yun7420-github-recovery-codes.txt`
- `~/Documents/한상윤/깃허브/vercel-recovery-codes.txt`

---

## 10. AI 세션 인계 안내

**다음 세션에서 이 프로젝트 이어받을 때**:

1. **`CLAUDE.md`가 자동 로드**됩니다 (Claude Code 관례).
2. 이 파일(`docs/AI_HANDOFF.md`)과 `docs/ROADMAP.md`를 순서대로 읽으세요.
3. **디렉토리명은 `lodestar`, 게임명도 `LODESTAR`**입니다. (`pest-hunter` → `solaris` → `lodestar` 순으로 리네임 됨, 이제 잔재 없음)
4. **큰 방향 전환**(테마, 프레임워크 이동 등)은 반드시 사용자에게 `AskUserQuestion` 확인.
5. **작업 후** `ROADMAP.md`의 완료 항목 체크·이슈 갱신. 큰 변경은 §11에도 기록.
6. **사용자는 완벽한 결과보다 반복 개선**을 선호. 매번 확인받지 말고 만들고 스크린샷/설명으로 확인 받기.

---

## 11. 변경 이력

### v0.1 (2026-09-10) — 초기 MVP (해충 박멸)
- 컨셉: 방역업체 사장, 해충 웨이브 박멸
- 5개 무기(슬리퍼/스프레이/전기채/폭탄/드론) + 6종 몹 + 보스
- localStorage 영구 저장 + 8종 영구 업그레이드 상점
- 이모지 스프라이트, 격자 배경

### v0.2 (2026-09-10) — Juice 강화 (같은 컨셉)
- 프로시저럴 사운드(Web Audio) 추가
- 파티클 시스템, 콤보 카운터, 화면 셰이크/플래시, 슬로우모, 무기 트레일

### v0.3 (2026-09-10) — 우주 리스킨
- 컨셉 전환: 해충 → 태양계 방위대 (반란 진압)
- 모든 몹/발사체를 **캔버스 벡터**로 직접 드로잉 (이모지 제거)
- 팔레트 리마스터 (딥 네이비 + 시안/바이올렛/앰버)
- UI 전면 리파인 (사이파이 톤, 모노스페이스)
- 플레이어: 이모지 → 삼각 우주선 (엔진 글로우, 조종석)
- 3중 패럴랙스 별밭 + 성운 배경

### v0.4 (2026-09-10) — 미니멀 폴리시
- 게임명 축소: "SOLARIS · 태양계 방위대" → **SOLARIS**
- 스토리 롱카피 제거 → 한 줄 룰
- 강화 이름 단순화: 선체 강화/주무기 증폭/… → **체력/공격/속도/골드/자석/쿨다운/부활/장비**
- 무기 이름 단순화: 궤도 위성/플라즈마 캐논/… → **오빗/플라즈마/펄스/미사일/드론**
- 플레이어: 삼각 우주선 → **순수 빛의 오브** (breathing glow, 궤도 스파크, 방향 힌트 아크)
- **기본 공격 추가** — SPACE 또는 좌클릭 → 마우스 방향 플라즈마 발사 (쿨다운 0.24s)
- **개인 기록(Personal Best)** — 사망 시 저장, 메뉴 상단 표시, NEW BEST 배지

### v0.5 (2026-09-10) — 위치 정리 · GitHub
- 프로젝트 위치 이동: `~/pest-hunter/` → `~/Documents/한상윤/project/solaris/`
- 디렉토리 리네임: `pest-hunter` → `solaris`
- GitHub 리포 생성 + 초기 push: `https://github.com/Yun7420/solaris` (Public)
- `.gitignore` + `README.md` 작성

### v0.6 (2026-09-10) — 라이브 배포 · 계측 시작
- **Vercel 배포 완료**: https://solaris-lime-nine.vercel.app (Hobby 플랜, 자동 재배포)
- **Google Analytics 4 심음** — `G-E6FJ175S19` (SOLARIS 속성, 대한민국, KRW, 향상된 측정 ON)
- 자동 계측: 페이지뷰, 스크롤, 외부 링크 클릭, 이탈 클릭, 세션 지속시간
- GitHub About의 Website 필드에 Vercel URL 노출

### v0.7 (2026-09-10) — 문서 이식성
- `CLAUDE.md` 진입점 파일 추가 (Claude Code 자동 로드)
- `docs/AI_HANDOFF.md` 생성 (이 파일, 기존 PROGRESS.md 리팩터)
- `docs/ROADMAP.md` 생성 (완료·계획 작업 관리)
- 루트 `PROGRESS.md` 삭제 (docs/ 하위로 이관)
- 이유: 이 맥은 회사 컴퓨터라 다른 곳에서도 작업할 수 있어야 함. 오토메모리에 의존 X, 프로젝트 자체에 문서 내장.

### v0.8 (2026-09-11) — 채굴 리브랜드 (SOLARIS → LODESTAR)
- **컨셉 전환**: 우주 방위대 → **우주 광부**. 몹이 반란군에서 광석으로 재정의.
- **몹 6종 완전 재작성**:
  - 코드명: `chunk/asteroid/scout/satellite/fighter/boss` → `pebble/silver/gold/platinum/diamond/mithril`
  - 시각적: 기계·행성 → **암반 다각형 + 티어별 결정 embed** (drawRockyBody + drawCrystals 헬퍼)
  - 미스릴 코어는 발광 결정 클러스터로 재작성 (`drawMithrilCore`)
- **인프라 전면 리네임** (SOLARIS → LODESTAR):
  - GitHub 리포: `Yun7420/solaris` → `Yun7420/lodestar` (`gh repo rename`, 옛 URL 자동 리다이렉트)
  - 로컬 폴더: `project/solaris/` → `project/lodestar/`
  - git remote URL 업데이트
  - localStorage 키: `solaris_meta_v1` → `lodestar_meta_v1` (기존 로컬 저장 데이터 리셋됨)
  - 모든 문서 (CLAUDE.md, README.md, docs/) SOLARIS 언급 전부 교체
- **UI 텍스트 조정**:
  - `<title>` / h1: SOLARIS → LODESTAR
  - 서브타이틀: "사방에서 몰려오는 적을 격파" → "사방에서 떠오는 우주 광석을 채굴. 돌 → 은 → 금 → 백금 → 다이아 → 미스릴"
  - HUD 라벨: KILL → MINED
  - 보스 등장: "▲ BOSS" → "▲ MITHRIL CORE"
  - 보스 처치: "BOSS DOWN" → "CORE MINED"
- **사용자 수동 작업 대기** (Vercel/GA4는 API 없음):
  - Vercel Project 리네임 (URL도 `lodestar-xxxx.vercel.app`으로)
  - GA4 속성명 SOLARIS → LODESTAR
  - GitHub About Website 필드 새 Vercel URL로 교체
- 이유: 컨셉이 완전 바뀌었으니 이름·인프라 모두 통일. 지금이 유저 트래픽 아직 없어서 리브랜드 손실 제로.
