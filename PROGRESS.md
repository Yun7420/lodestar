# SOLARIS · 개발 진행 기록

> 이 문서는 **다음 AI 세션이 이 프로젝트를 이어받아 작업할 수 있도록** 만든 인계 문서입니다.
> 새 세션은 이 파일을 먼저 읽고, 필요하면 `index.html` 코드를 확인하세요.
> 위치: `/Users/hansang-yun/Documents/한상윤/project/solaris/PROGRESS.md`

---

## 1. 한 줄 요약

브라우저에서 바로 돌아가는 **Vampire Survivors 스타일 뱀서라이크** 게임. 사용자는 우주 컨셉·미니멀 톤·자동 발사 + 액티브 공격을 원함. 단일 HTML 파일, 빌드/의존성 없음.

- **게임명**: SOLARIS
- **현재 버전**: v0.4
- **파일**: `/Users/hansang-yun/Documents/한상윤/project/solaris/index.html` (단일 파일, 약 1500줄)
- **문서**: `/Users/hansang-yun/Documents/한상윤/project/solaris/PROGRESS.md` (이 파일)
- **저장소**: GitHub `https://github.com/Yun7420/solaris` (아직 초기 push 전 · 다음 세션에서 진행)

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
**SOLARIS** (한 단어, 이전에 "태양계 방위대" 붙였다가 사용자가 촌스럽다고 잘라냄)

### 장르
Vampire Survivors-like (bullet heaven) — **뇌빼고 하는 도파민 게임**

### 핵심 루프
```
이동 → 자동 무기 발사 → 몹 처치 → XP/골드 획득 → 레벨업(무기 강화) → 반복
        ↓
     사망 시
        ↓
사령부 금고에 골드 저장 → 영구 강화 구매 → 다음 판에서 더 강해짐
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

### 몹 (6종) - `ENEMY_DEFS` 객체
`chunk` (소행성조각) / `asteroid` (소행성) / `scout` (정찰드론, 붉은 눈 펄스) / `satellite` (반란위성, 태양전지판) / `fighter` (외계 전투기, 다트형) / `boss` (반란 행성, 대기광·링)

**모든 몹은 캔버스 벡터로 직접 그림** — 이모지 사용 금지. `drawEnemy()` → 타입별 draw 함수 라우팅.

### 스폰
- 첫 60초: chunk/asteroid만
- 30s부터 scout, 90s부터 asteroid 증가, 150s satellite, 210s fighter…
- HP 스케일링: `1 + time/90`
- 스폰 속도: `1.2 + time/25` 마리/초
- **3분(180s)마다 보스** 출현 → "▲ BOSS" 텍스트 + 화면 흔들림

### 영구 강화 (SHOP_ITEMS)
체력(+20HP) / 공격(+5%DMG) / 속도(+3%SPD) / 골드(+10%) / 자석(+25%픽업) / 쿨다운(-3%) / 부활(+1) / 장비(+1 시작 무기)

### 개인 기록 (v0.4 신규)
`META.best = { time, kills, gold }` — 사망 시 갱신, 메뉴 상단에 표시. 갱신 시 게임오버 화면에 "▲ NEW BEST" 배지.

---

## 5. 코드 지도 (index.html 내부)

파일 하나에 전부 인라인 (HTML + CSS + JS). 섹션 구분자 주석 참고.

```
Line ~1-190       HTML: overlays (menu, levelup, gameover), HUD, canvas, styles
Line ~200-260     Audio: procedural Web Audio SFX (beep + noiseBurst 조합)
Line ~270-320     Meta state: DEFAULT_META, loadMeta/saveMeta, metaMul() 배율 헬퍼
Line ~325-370     WEAPONS / PASSIVES 정의
Line ~372-386     ENEMY_DEFS 정의
Line ~388-450     Run state: state 객체 스키마, starfield/nebula 초기화, newRunState()
Line ~455-490     Input: keys, mouse, event handlers
Line ~495-560     Helpers: dist/randRange/spawnFloatText/spawnSparks/spawnDebris/shake/combo
Line ~565-620     Spawning: spawnEnemyAt/spawnAtEdge/currentSpawnMix/updateSpawner
Line ~625-700     Weapon stats: weaponStats(key) — level별 스탯 계산
Line ~702-760     Basic attack: fireBasicAttack()  ← v0.4 신규
Line ~762-830     Fire weapons: fireWeapon(key, stats)
Line ~835-980     Update loop: update(dt) — 이동, 몹 AI, 발사체, 이펙트, 픽업, 레벨업 체크
Line ~985-1020    Damage/Kill: damageEnemy() / killEnemy()
Line ~1025-1090   Level up choices: triggerLevelUp() / pickLevelUpChoices() / applyChoice()
Line ~1095-1250   Drawing primitives: drawOrb (player), drawAsteroid, drawScout,
                  drawSatelliteEnemy, drawFighter, drawBossPlanet, drawEnemy 라우터
Line ~1255-1350   Projectile draws: drawProjSatellite/Plasma/Missile/Drone + 라우터
Line ~1355-1420   Icons for menu: drawIcon() — SVG-스러운 심볼
Line ~1425-1550   Render: render() — 배경, 성운, 별밭, 픽업, 몹, 발사체, 플레이어, 이펙트, 비네트
Line ~1555-1620   HUD/Shop/Menu/Death: updateHUD/onDeath/backToMenu/renderShop/buyUpgrade/resetAll
Line ~1625-1680   Start/Loop/Fit: startGame() / loop() / fit()
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

### 유저의 정성적 취향 (반드시 지킬 것)
- **미니멀**: 롱카피/장식적 명명 금지. "선체 강화" X → "체력" O.
- **세련됨**: 아기같음/이모지 남발 X. 미니멀 UI, 딥한 컬러.
- **자동화**: 조작 최소화. 무기 자동. 다만 액티브 여지는 남김(SPACE/클릭).
- **성장 가시성**: 숫자가 커지는 즐거움. 콤보, 데미지 넘버, 크레딧 축적.
- **확장 여지**: 나중에 랭킹/커뮤니티 붙일 수 있는 방향으로 데이터 저장.

---

## 7. 현재 알려진 이슈 · 미비점

- **아직 안 함**: 밸런스 튜닝(실제 30분 플레이 안 해봄, 몹 HP·업그레이드 코스트 미조정)
- **아직 안 함**: 모바일 조작(터치 조이스틱)
- **아직 안 함**: 스테이지 시스템(현재 단일 무한 웨이브)
- **아직 안 함**: 서버 리더보드 (로컬 best만 있음)
- **아직 안 함**: 배포(itch.io / 스팀 / 웹뷰 모바일)
- **작은 이슈**: `triggerLevelUp` 내부 카드 렌더 코드가 지저분함 (icon wrap 이중 처리, 작동은 함). 정리해도 좋음.
- **작은 이슈**: 마우스 aim 인디케이터 없음 (조준선/십자선 추가하면 클릭 공격 피드백 향상)
- **작은 이슈**: 기본 공격에 관성이나 반동 이펙트 없음 (액션감 강화 여지)

---

## 8. 로드맵 (우선순위 순)

### 즉시 (다음 세션 시작하면 이거부터 물어봐도 됨)
1. **밸런스 튜닝 · 30분 플레이 후 조정** — 몹 HP 스케일링, 업그레이드 코스트, 무기 데미지 비례
2. **마우스 조준 리티클 추가** — 클릭 공격 피드백
3. **콤보 이펙트 강화** — 콤보 10/25/50 마일스톤 시 화면 이펙트

### 단기 (1~3일)
4. **모바일 대응** — 세로 화면, 터치 조이스틱, 자동 사격 (SPACE 대체)
5. **스테이지 시스템** — 배경 팔레트 스와핑, 각 스테이지 고유 몹 세트 (성운/우주/블랙홀/성단 등)
6. **음악 추가** — Web Audio로 짧은 앰비언트 루프 (또는 배경 사운드스케이프)
7. **엔딩 조건** — 15분 생존 → 승리 화면 + 특별 보상

### 중기 (1~2주)
8. **에셋 업그레이드** — 파티클/이펙트 셰이더, 필요 시 스프라이트 도입 (AI 이미지 생성)
9. **더 많은 무기/패시브** — 8~10개 무기, 조합 시너지 (뱀서의 evolution 시스템)
10. **일일 챌린지 / 세팅 프리셋** — 유저 리텐션 훅

### 장기 (수익화·성장)
11. **배포**: itch.io + CrazyGames 퍼블리싱 신청
12. **서버 리더보드**: 익명 닉네임 + 일간/주간 랭킹 (간단히 Supabase or Firebase)
13. **광고 붙이기**: 웹 광고(CrazyGames), 모바일은 AdMob 웹뷰
14. **커뮤니티 기능**: 리플레이 공유, 스크린샷 자랑 (SNS 훅)

---

## 9. 실행 방법

### 로컬 실행
1. 파인더에서 `/Users/hansang-yun/Documents/한상윤/project/solaris/index.html` 더블클릭
2. 또는 터미널: `open "/Users/hansang-yun/Documents/한상윤/project/solaris/index.html"`
3. 사운드가 안 나오면 브라우저 정책 → 아무 키/클릭 한 번이면 켜짐

### 로컬 서버로 실행 (Playwright 테스트용)
```bash
cd "/Users/hansang-yun/Documents/한상윤/project/solaris"
python3 -m http.server 8765
# 브라우저에서 http://localhost:8765/
```

### AI 세션에서 테스트하기
- Playwright MCP 사용. `file://` 프로토콜은 차단되므로 반드시 로컬 서버 경유.
- 예:
  ```
  Bash: python3 -m http.server 8765 (run_in_background: true)
  mcp__playwright__browser_navigate: http://localhost:8765/
  mcp__playwright__browser_click: button[onclick='startGame()']
  mcp__playwright__browser_press_key: d (이동)
  mcp__playwright__browser_take_screenshot: play.png
  Read: /Users/hansang-yun/play.png
  ```
- 끝나면 `pkill -f "http.server 8765"`로 정리.

---

## 10. AI 세션 인계 안내

**다음 세션에서 이 프로젝트 이어받을 때**:

1. **이 파일(`PROGRESS.md`)을 먼저 읽으세요**. 사용자가 SOLARIS/solaris 프로젝트 언급하면 자동으로 열어보세요.
2. **위치**: `/Users/hansang-yun/Documents/한상윤/project/solaris/` (2026-09-10 리네임·이동, 이전 `pest-hunter/` 경로는 사용 안 함).
3. **사용자 취향은 §6에 시간 순으로 정리**. 반복 실수 금지.
4. **코드 위치는 §5의 라인 지도** 참고. 정확한 라인은 파일이 바뀌면 어긋날 수 있으니 grep 확인.
5. **작업 후 이 문서를 업데이트하세요**. §7(이슈), §8(로드맵), §11(변경 이력)에 반영.
6. **큰 방향 전환 시(테마 변경, 프레임워크 이동 등)** 반드시 사용자에게 `AskUserQuestion`으로 확인.
7. **사용자는 완벽하게 완성된 결과보다 반복 개선**을 선호. 매번 물어보지 말고 일단 만들고, 스크린샷/설명으로 확인 받기.

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

### v0.4 (2026-09-10) — 미니멀 폴리시 (현재)
- 게임명 축소: "SOLARIS · 태양계 방위대" → **SOLARIS**
- 스토리 롱카피 제거 → 한 줄 룰
- 강화 이름 단순화: 선체 강화/주무기 증폭/… → **체력/공격/속도/골드/자석/쿨다운/부활/장비**
- 무기 이름 단순화: 궤도 위성/플라즈마 캐논/… → **오빗/플라즈마/펄스/미사일/드론**
- 플레이어: 삼각 우주선 → **순수 빛의 오브** (breathing glow, 궤도 스파크, 방향 힌트 아크)
- **기본 공격 추가** — SPACE 또는 좌클릭 → 마우스 방향 플라즈마 발사 (쿨다운 0.24s)
- **개인 기록(Personal Best)** — 사망 시 저장, 메뉴 상단 표시, NEW BEST 배지
- UI 텍스트 대문자·짧게: START / SOUND ON / RESET / END / MENU 등
- 이 문서(`PROGRESS.md`) 최초 작성

---

### v0.5 (2026-09-10) — 위치 정리 · GitHub 준비
- 프로젝트 위치 이동: `~/pest-hunter/` → `~/Documents/한상윤/project/solaris/`
- 디렉토리 리네임: `pest-hunter` → `solaris` (게임명과 일치)
- GitHub 리포 생성 + 초기 push 완료: `https://github.com/Yun7420/solaris` (Public, `main` 브랜치)
- `.gitignore` + `README.md` 작성
- 이 문서·오토메모리(`solaris_game_project.md`)의 경로 참조 모두 업데이트

---

_마지막 업데이트: 2026-09-10 · 다음 세션은 이 문서를 먼저 읽고 이어받으세요._
