# 꼬마 부대 대작전 — 작업 규칙

초등학생용 3D 러너 슈팅 게임. `index.html` 파일 하나(Three.js 0.160.0 CDN, 빌드 없음)가 전부다.

## 배포 (사용자의 상시 요청)

게임을 고치면 **묻지 않고 직접 배포까지 끝낸다.**

1. 수정 후 테스트를 통과하면 `main`에 커밋하고 `git push origin main`.
2. Render 정적 사이트 `kkoma-squad`가 push를 받아 자동 배포한다.
   - 사이트: https://kkoma-squad.onrender.com
   - Render 서비스 ID: `srv-dbonkmnf3r2c73botj8g` (workspace `tea-dbo4i967bikc73fur6mg`)
3. 배포가 끝났는지 사이트를 직접 받아 확인하고(새로 바꾼 문구나 코드가 들어갔는지), 사용자에게 링크와 바뀐 점을 알려 준다.
4. 커밋 메시지는 한국어로, 무엇을 바꿨는지 쓴다.

## 반 전체 랭킹 (Supabase)

- `index.html`의 `rankingService` 블록에 `SUPABASE_URL`과 `SUPABASE_ANON_KEY`(publishable 공개 키)가 들어 있다. 이 값은 지우거나 바꾸지 않는다.
- 표 `ranking`은 읽기만 가능하고, 기록은 `submit_score` 함수로만 저장된다(한 이름당 최고 기록). SQL은 `README.md`에 있다.
- **진짜 랭킹에 테스트 기록을 쓰지 않는다.** 저장 흐름은 Playwright `page.route`로 가짜 Supabase를 만들어 테스트하고, 실제 서버에는 읽기 또는 거절되는 요청(예: 9글자 이름)만 보낸다.
- Supabase나 인터넷이 안 되면 자동으로 기기 안(localStorage) 랭킹으로 바뀌어야 한다. 이 동작을 깨지 않는다.

## 테스트

- 클라우드 환경 `kkoma-squad`에서는 Supabase와 Render 주소만 허용되고, cdn.jsdelivr.net과 Google Fonts는 막혀 있다. Three.js는 `npm pack three@0.160.0`으로 받아서 Playwright `page.route`로 CDN 요청 대신 넣어 준다.
- 내부 상태는 주소에 `?debug`를 붙일 때만 `window.__game`으로 열린다.
- 콘솔 오류 0개, PC와 휴대폰 세로 화면 스크린샷을 확인한 뒤 배포한다.

## 지킬 것

- 화면 글자는 전부 한국어로 쓴다. 기존 상용 게임의 캐릭터, 그림, 이름은 쓰지 않는다.
- 과금과 광고 요소는 넣지 않는다.
- 크롬북에서도 가볍게 돌아가야 한다. 반복되는 물체는 InstancedMesh로 그리고, 자동 해상도 조절은 유지한다.
