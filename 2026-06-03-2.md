# 2026-06-03 — 컨텍스트 정리 #2

## 작업

### retweet 포트폴리오 모달 상세화 (아이디어만, 미적용)
- myWebsite의 ProjectModal(project2=Sleact)이 rich content 구조: description/motivation/demoGif/techStack/designPoints(problem·solution·result·video)/features/troubleshooting/links
- 현재 retweet(project3) 모달은 description/techStack/features/links만 → 빈약
- git log + 코드(client.ts, use-toggle-post-like.ts, comment 엔티티, RefreshTokenStore 등)에서 채울 재료 정리
- 최종 방향: "기술 나열" 대신 **엔지니어링 판단 5역량**으로 재구성 (BE/FE 5:5 균형)
  - ① E2E 인증(Redis 토큰스토어 + FE 무중단 재발급/인터셉터 큐잉) ② 상태 정합성(낙관적 업데이트+정규화 캐시) ③ 도메인 모델링(댓글 rootComment/parentComment 2-depth FK) ④ 성능(가상화·번들·CLS) ⑤ 운영·배포(CI/CD·OOM 대응)
  - troubleshooting 3개(삭제 FK 순서, OAuth Profile NPE, 삭제글 접근), features 그룹화
- 현재 상태: 모달 코드 미수정. 재료·구조만 확정

### 디자인시스템 프로젝트 신규 착수 ✅ 실제 작업
- 위치: `/Users/soo/Documents/design-system`
- 스택: Vite + React + TS + Tailwind v4(@tailwindcss/vite) + pnpm (node 22 / pnpm 10)
- 전략: 모노레포 통합 X (retweet·myWebsite 이미 별도 배포 repo → 위험). 독립 프로젝트 먼저 만들고 Storybook 산출물을 포트폴리오로, 나중에 기존 프로젝트 연결
- **1단계**: `pnpm create vite@latest design-system --template react-ts` + install ✅
- **2단계**: Tailwind v4 + 디자인 토큰 + 다크모드 ✅ (`pnpm build` 통과, built in 100ms)
  - vite.config.ts에 `tailwindcss()` 플러그인 추가
  - index.css: `@import "tailwindcss";` + `@custom-variant dark (&:where(.dark, .dark *));` + `@theme {}`에 토큰(`--color-primary/accent/surface/muted/border`, `--radius-card`, `--shadow-pop`)
  - `.dark {}`에서 토큰 값만 교체
  - App.tsx: 토큰 박스 4개 + 다크모드 토글 데모로 교체
- 현재 상태: 2단계 완료. 3단계(Storybook)부터 내일(2026-06-04) 이어감
- 메모리 저장: project_design_system.md + MEMORY.md 인덱스 갱신

## Q&A / 배운 것

### Tailwind v4 디자인 토큰 동작 원리
- `@theme {}` 안 변수 → Tailwind가 자동으로 유틸리티 클래스 생성 (`--color-primary` → `bg-primary`/`text-primary`, `--radius-card` → `rounded-card`)
- 다크모드: `.dark {}`에서 **토큰 값만** 교체 → 컴포넌트는 `bg-surface`만 계속 씀, 코드 불변으로 전환
- `@custom-variant dark (&:where(.dark, .dark *))` = `.dark` 클래스 기준으로 다크 판단 (없으면 OS 설정만 따라감)

### "확장 가능한 시스템 / Design System·UI Library 개발"이 무슨 영역?
- 프론트엔드 영역 (디자인=그래픽 아님). JD가 "UI Framework/Library 개발"이면 코드로 컴포넌트 라이브러리 만드는 일
- 확장 가능한 시스템(FE 맥락) = 서버 트래픽 아니라 기능·팀 늘어도 안 무너지는 코드 구조 (store 분리, 쿼리키 체계, 관심사 분리 등 — 이미 보유)
- 본인은 적용·설계 증거 충분, "라이브러리 직접 개발"만 빔 → Storybook + 토큰 + 공통 UI 패키지 추출로 보강

### 데이터 시각화(차트/대시보드/리포트) 컴포넌트 구축이란
- 차트=그래프 하나(막대/선/산점도), 대시보드=차트 여러 개 모은 현황판, 리포트=기간/조건 데이터 정리 화면
- "컴포넌트 구축"=재사용 가능한 차트 컴포넌트로 (`<BarChart data={}/>`)
- 대량 데이터 렌더링 성능이 진짜 난제 (Canvas/WebGL). 라이브러리: Recharts/Chart.js/visx/D3/ECharts
- 본인 없음 → myWebsite 어드민 통계 대시보드로 보강 시 "시각화+운영어드민+0부터설계" 동시 해결

### JD 12개 분석 (프론트/백엔드/풀스택)
- 분류: 프론트(집지켜·위시스트·포스타입·온라인사업가) / 백엔드(위버스BE·여기어때·B2B WMS) / 풀스택(위버스·에듀테크·채널톡·스탠드오일·의료)
- 강점: 실시간/WebSocket/Redis 심화, 풀스택+테스트+성능, AI Agent 실사용(채용 자동화), 트러블슈팅 깊이
- 반복 갭(우선순위): ①모니터링(Sentry/Amplitude) ②커머스/결제 도메인 ③Kafka/이벤트스트리밍 ④협업·코드리뷰·페어 ⑤MCP·Kotlin·K8s·RN

### FE 채용담당자 시각 — 탈락 사유
1. 백엔드 색채 과함(sleact 하이라이트 다 서버) 2. 클론 코딩 2개(0부터 설계 약함) 3. 실사용자·운영흔적 0 4. 혼자만 함(협업 0) 5. CSS·디자인·접근성 의심(포폴 사이트 이모지 기반) 6. 프론트 진짜 난제 회피(시각화·에디터·대량렌더) 7. 기술 나열 多→얕아보임 8. AI자동화=양날(바이브코딩 의심)

### FE 난제 → 기존 프로젝트에 심기 (새 클론 X)
- 데이터 시각화 → myWebsite 어드민 대시보드 (★★★)
- 위지윅 에디터 → myWebsite 블로그/retweet (★★★)
- 대량 렌더링(어려운 버전) → sleact 채팅 가상화 (★★)
- 복잡 폼/접근성 → retweet 폼 (★)

## 미해결
- [ ] 디자인시스템 3단계 Storybook 설치 (내일)
- [ ] 4단계 Button 컴포넌트 (CVA variant)
- [ ] 5단계 컴포넌트 확장 + 기존 프로젝트 연결
- [ ] retweet 모달 5역량 구조로 실제 코드 적용
- [ ] (갭) Sentry/Amplitude 모니터링 적용 — 가장 싸게 메울 갭
- [ ] (갭) 커머스/결제 토이연동, Kafka 전환 실험 등 도메인 보강
