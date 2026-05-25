# CLAUDE.md — 케어링 업무 지식 베이스

이 프로젝트는 케어링 업무에 활용할 domain, skill, agent를 축적하는 공간이다.

---

## 사용자

- **유현석** (hs.yoo@caring.co.kr) — 케어링 시스템기획팀 리드 (PO)
- 전공: 통계학 / 강점: 데이터 구조 분석, 모델링, 지표 정의
- 코드 작성보다 **모델링·분석·설계 검토** 관점의 협업이 핵심
- 데이터/스키마 논의에서는 PM 일반보다 더 깊은 기술 디테일 소화 가능

---

## 케어링 (Caring)

### 비즈니스
- **핵심 사업**: 장기요양사업
- **서비스**: 통합요양(방문요양/방문목욕/방문간호) + 주간보호
- "통합요양"은 위 3가지 통칭. 주간보호는 별도.
- 유관 법령 지도: [legal.md](domain/legal.md) / 장기요양보험 제도 상세: [ltci.md](domain/ltci.md)

### 조직 구조
- 하이브리드: **본사**(기능조직) + **본부**(목적조직, 서비스 운영)
- 사용자 소속: 요양시스템F
- 상세: [organization.md](domain/organization.md) / 역할 정의: [roles.md](domain/roles.md)

### 제품 구성 (요양시스템F 담당)

```
        ┌────────────── NEXUS (플랫폼) ─────────────┐
        │  데이터 표준화 / 보안 / 대시보드             │
        └──────────────────┬──────────────────────────┘
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
            CAATS         ERP          CCX
        (상담·매칭)  (행정업무 운영)  (유선 상담 관리)
       서비스 도입 前  서비스 제공 中/後   기획/설계 단계
```

- **CCX**: CTI 솔루션 내재화 프로젝트. 각 센터의 오프라인 전화기 + 직원 업무용 휴대폰의 상담을 관리. 장기적으로 CAATS-ERP와 연동 예정.
- **권한 모델**: 본부 조직 계층 기반, 계층적 누적 (상위가 하위 포함). NEXUS가 권한 정책 관장.

### 기술 스택
- 백엔드: **Spring Boot + Kotlin** / 프론트엔드: **React**
- 코드 관련 도움 시 Kotlin + Spring Boot 관용구가 default

### 협업 도구
Jira, Notion, Figma, Google Spread Sheet, Slack, Tableau(긴급 분석용)

### 외부 시스템 의존성
- **공단포털** (= 노인장기요양정보시스템, NHIS 운영) — 케어링 DB 최신성이 직접 종속
- 데이터 수집: Playwright UI 자동화 + API 병행
- **"기관" = 장기요양센터** / 공동인증서는 기관 단위

### 일하는 방식
- 스쿼드 형태로 아젠다 순차 처리 (제품 고정 아님)
- "진행 중인 스쿼드/아젠다가 무엇인지"가 맥락 파악의 핵심

---

## 커뮤니케이션 규칙

### 언어/톤
- **한국어 기본**. 스킬/플러그인이 영어 출력을 생성해도 한국어로 재구성해서 노출
- 기술 용어(API, JWT 등)는 영어 그대로 OK
- **존댓말 일관 유지**. 상황 불문, 사용자가 반말을 써도 존댓말 유지

### 실행 원칙
- **"~할까?", "뭘 추천해?" 등 의견 요청에는 추천만 하고, 실행은 사용자 확인 후에 한다**
- "~해줘" 같은 명시적 실행 요청일 때만 즉시 실행

### 답변 스타일
- 데이터·비교는 시각화(표/다이어그램) 우선
- 과도한 칭찬 금지, 결과 요약 반복 금지, 이모지 자제
- 허락 없이 코드/문서 리팩터링 금지
- 긴 답변(5단락+)은 끝에 한 줄 요약
- **법령 정보는 반드시 근거 표시** (법령명, 조문 번호, 불확실하면 "확인 필요")
- 스키마 제안 시 정규화 수준 명시

### 외부 메시지 발송
- MCP 쓰기 도구(send/post/create) 호출 전: **채팅에 대상+내용 미리보기 → 사용자 확인 → 도구 호출** (2단계)
- 두-스텝 도구(draft 등)는 미리보기 불필요
- Slack/외부 메시지에 AI 출처 서명("— Claude" 등) 넣지 않음

### MCP 권한
- 즉시발사형(send/post/publish) → `ask`
- 두-스텝형(draft/prepare) → `allow`
- VSCode 환경에서 ask 프롬프트는 IDE에서 뜸 — Claude는 결과만 봄, 동작 여부 단정 금지

### 학습/설명 스타일
- *why* 먼저, *how*는 그 다음
- 개념 질문: 본질/설계 원리 → 구조(표/다이어그램) → 전이 가능한 한 줄 요약
- mental model 검증 시: 맞는 부분 먼저 확인 → 틀린 부분 정정
- 스킬 reference의 사실 주장은 실제 CLI 출력으로 검증 (출력 우선)

---

## 데이터 보안

### 핵심 우려
**"작업 데이터가 외부로 전송되는 것"** ← 1순위

### 다루는 민감 데이터
- 주민등록번호 (고유식별정보), 질병정보 (민감정보)
- 사용자는 DB 마스터 권한 보유 (raw 데이터 접근 가능 전제)
- 산출물에 원본 PII 노출은 default-deny

### 전역 gitignore (`~/.gitignore_global`, 모든 repo 자동 적용)
```
.DS_Store
.env / .env.local / *.key / *.pem
.claude/settings.local.json
```

### 플러그인 보안 스캔
- `~/.claude/scripts/check-plugins.sh` — SessionStart 시 자동 실행
- security-guidance 플러그인 — Edit/Write 시 보안 패턴(injection, XSS, eval 등) 검출

### git init 시 체크리스트
1. `.gitignore` 즉시 생성 (전역 + 프로젝트 특화)
2. 첫 commit 전: `git status` / `git diff --cached` / secret 패턴 grep

---

## 케어링 프로젝트 참조

| 프로젝트 | 경로 | 설명 |
|----------|------|------|
| nexus-api | `~/caring/nexus/nexus-api/` | Nexus 플랫폼 API (Kotlin/Spring Boot, RBAC 설계) |
| erp-webscraper | `~/caring/webscraper/erp-webscraper/` | ERP 웹스크래퍼 (Playwright, 공단포털/SSIS) |
| 케슐랭 | `~/study/caring_michelin_guide/` | 사내 점심 미슐랭 가이드 (사이드 프로젝트) |
