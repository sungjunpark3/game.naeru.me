# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 구조

- 게임 전체가 `index.html` 한 파일에 들어 있다(인라인 CSS·JS, 빌드 단계 없음). 브라우저로 바로 열어 실행한다.
- `const SPRITES = ...` 한 줄(약 85KB)에 내루미 스프라이트가 base64 WebP로 들어 있다. 파일을 통째로 읽지 말고 `grep -n`으로 위치를 찾아 `offset`/`limit`로 필요한 부분만 읽는다. 이 줄을 Edit의 매칭 대상에 넣지 않는다.
- UI 문구와 코드 주석은 한국어로 쓴다.

## 내루미(Lickitung) 외형 — 반드시 지킬 것

- 내루미는 포켓몬 내루미(Lickitung)다. 외형이 레퍼런스와 달라지면 안 된다.
- 레퍼런스: `~/Documents/refer.png` (정면·측면, 걷기 4프레임, 혀를 수평으로 뻗는 장면).
- 캐릭터는 레퍼런스에서 오려낸 스프라이트로만 그린다. 도형으로 직접 그리거나 새로 그리지 않는다.
  - 걷기 4프레임: 원본에서 좌우 반전해 오른쪽을 보게 함. 서 있을 때는 `IDLE_FRAME`, 점프는 `walk[0]`.
  - 공격 자세: "혀를 수평으로 길게 뻗는 장면"의 몸통. 혀 뿌리(`sx`)까지만 그림이고, 그 뒤 혀는 `drawTongueBody`에서 원본 색(`TONGUE_C`)으로 이어 그린다.
- 혀 공격은 항상 수평이다. 레퍼런스에 맞추려고 대각선 공격을 일부러 없앴다.
- 스프라이트를 다시 만들 때: 배경(저채도·밝은 픽셀)을 가장자리부터 지우고, 기준점은 눈 위치(`ax`)와 발바닥(`ay`)으로 맞춘다. 배율은 `NS = 64 / 207`.

## 게임 규칙 (사용자 요구사항)

- 하트는 3개로 고정이다. 몸에 3번 닿으면 죽는다. 최대 하트 수를 늘리는 능력은 넣지 않는다(회복만 가능).
- 점수 구간을 넘을 때마다 무작위 능력 카드 3장 중 1장을 고른다.
- 적은 진행 거리와 시간에 따라 점점 강해진다(`G.diff`, `hpMul`).

## 테스트

- 테스트 프레임워크는 없다. 헤드리스 확인은 `playwright-core`에 캐시된 Chromium(`~/Library/Caches/ms-playwright/chromium-1140/chrome-mac/Chromium.app/Contents/MacOS/Chromium`)을 `executablePath`로 지정해서 한다.
- 페이지에 `window.__narumi` 테스트 핸들이 있다. `__narumi.sim(초)`는 화면을 그리지 않고 게임을 빠르게 진행한다(카드는 무작위로 자동 선택). `__narumi.keys`로 입력을 흉내 낸다.
