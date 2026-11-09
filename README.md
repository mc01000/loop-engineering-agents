# Loop Engineering Agents - 9-Agent Virtual Team

이 레포지토리는 Antigravity(AGY) 등 코딩 AI 도구들을 위한 사용자 지정 에이전트(`custom-agents`) 플러그인, 5단계 오케스트레이션 스킬(`loop-engineering`), 그리고 과업별 모델 자동 전환 라우팅 규칙(`agent-team-routing.md`)의 배포 및 공유를 위해 작성되었습니다.

구성된 전문 AI 서브 에이전트들은 `gstack`의 가상 엔지니어링 팀 개념에서 영감을 받아, Antigravity 고유의 자연어 라우팅과 서브 에이전트 모델 동적 지정(`pro` / `flash`)을 통해 호출되도록 설계되었습니다.

---

## 🧠 과업별 자동 지정 모델 (Automatic Model Routing)

에이전트가 서브 에이전트를 호출(`invoke_subagent`)할 때, 과업의 성격과 난이도에 따라 자동으로 최적의 모델 등급(`pro` = Gemini 4 Argon 계열 / `flash` = Gemini 3.8 Flash 계열)을 지정하여 추론 품질과 속도·쿼터 효율을 동시에 극대화합니다.

| 담당 서브 에이전트 | 과업 내용 | 자동 지정 모델 (`Model`) | 이유 |
| :--- | :--- | :--- | :--- |
| **`lead_architect`** | 요구사항 분석 및 아키텍처 설계 | **`pro`** | 초기 설계 품질이 전체 성패를 좌우하므로 최고 추론 모델 사용 |
| **`architect_reviewer`** | 아키텍처 비판적 검토 | **`pro`** | 확장성·복잡도·엣지케이스의 정밀한 검증 필요 |
| **`security_reviewer`** | OWASP 및 시맨틱 보안 감사 | **`pro`** | 숨겨진 취약점 및 권한 우회 시나리오 추론 필요 |
| **`software_engineer`** | TDD 코드 구현 및 버그 수정 | **`pro`** (핵심 로직) / **`flash`** (일반 구현) | 복잡한 비즈니스 로직은 `pro`, 일반 CRUD/보일러플레이트는 `flash` |
| **`project_leader`** | 기획 및 백로그/이슈 분해 | **`flash`** | 빠른 작업 분해 및 이슈 생성에 최적화 |
| **`code_reviewer`** | 테스트 커버리지 및 코드 리뷰 | **`flash`** | 테스트 실행 확인 및 코드 컨벤션 점검 |
| **`qa_engineer`** | 브라우저 E2E 및 UI/UX 테스트 | **`flash`** | 빠른 화면 조작 및 시나리오 반복 검증 |
| **`doc_engineer`** | 기술 문서 및 가이드 작성 | **`flash`** | 코드 기반 문서 요약 및 작성 속도가 우수 |
| **`doc_reviewer`** | 문서-코드 일치 여부 대조 | **`flash`** | 정형화된 스펙/경로/매개변수 1:1 교차 대조 |
| **`release_engineer`** | 테스트 구동 확인 및 PR/커밋 생성 | **`flash`** | 정해진 Conventional Commits 포맷팅 및 명령 실행 |

---

## 🚀 워크플로우 및 구성 에이전트

전체 개발 주기를 담당하는 전문 가상 팀원과 통합 루프 스킬이 포함되어 있습니다. 특정 키워드(예: "보안 점검해줘", "배포 준비해줘", "가상 팀 루프로 구현해줘")를 말하면, `GEMINI.md`에 설정된 글로벌 규칙에 의해 자동으로 담당 에이전트와 지정 모델이 호출됩니다.

### 🔄 통합 스킬: `loop-engineering`
- **역할**: 기획(`project_leader`) ➔ 설계 루프(`lead_architect` ⇄ `architect_reviewer`) ➔ TDD 구현 및 3중 검증 루프(`software_engineer` ⇄ `code_reviewer` & `security_reviewer` & `qa_engineer`) ➔ 문서화 루프(`doc_engineer` ⇄ `doc_reviewer`) ➔ 배포/PR(`release_engineer`)의 5단계 파이프라인을 끝까지 완수합니다.
- **라우팅 발화**: `/loop-engineering`, `/loop`, "loop-engineering 스킬로 시작해줘", "가상 팀 루프로 구현해줘", "엔지니어링 루프로 끝까지 완료해줘"

### 0. `project_leader` (프로젝트 총괄 / PM) — `Model: flash`
- **역할**: 오케스트레이션의 시발점(Entry point)으로서 사용자의 요구사항을 분석해 백로그를 작성하고, 각 전문 에이전트에게 업무를 병렬 또는 순차적으로 위임합니다.
- **라우팅 발화**: "프로젝트 시작해줘", "이 기능 전체 총괄해줘", "작업 쪼개서 분배해줘"

### 1. `lead_architect` (수석 아키텍트 / 기술 리더) — `Model: pro`
- **역할**: 사용자의 요구사항을 분석하고, 시스템을 설계하며, 하위 서브 에이전트들이 이해할 수 있는 명확한 프롬프트를 작성합니다. (강력한 Superpower 스킬 활용)
- **라우팅 발화**: "새로운 기능 설계", "시스템 설계", "요구사항 분석"

### 2. `software_engineer` (소프트웨어 엔지니어) — `Model: pro (핵심 로직) / flash (일반 구현)`
- **역할**: 아키텍트의 설계 문서를 바탕으로 실제 코드를 구현하고, TDD 기반 개발을 진행하며, 리뷰어들의 피드백을 반영하여 버그를 수정합니다.
- **라우팅 발화**: "기능 구현해줘", "코드 작성해줘", "버그 수정해줘", "피드백 반영해서 고쳐줘"

### 3. `architect_reviewer` (아키텍처 리뷰어) — `Model: pro`
- **역할**: 설계 및 아키텍처에 대한 비판적 리뷰를 수행하고 보완점을 제시합니다.
- **라우팅 발화**: "아키텍트 리뷰해줘", "설계 검토해줘"

### 4. `security_reviewer` (보안 책임자) — `Model: pro`
- **역할**: 변경된 코드 및 Diff를 기반으로 인젝션, 권한 문제 등 의미론적(Semantic) 보안 취약점을 깊이 있게 분석합니다.
- **라우팅 발화**: "보안점검해줘", "보안 리뷰해줘"

### 5. `code_reviewer` (코드 리뷰어 & 테스트 검증) — `Model: flash`
- **역할**: 순수 논리 오류, 런타임 버그, Race Condition 등을 잡고 코드 품질을 리뷰합니다. 또한 **테스트 코드 커버리지를 엄격하게 검사**하여 테스트 누락을 방지합니다.
- **라우팅 발화**: "코드 리뷰해줘", "버그 찾아줘", "테스트 커버리지 확인해줘"

### 6. `qa_engineer` (QA 엔지니어) — `Model: flash`
- **역할**: 스테이징 및 프로덕션 환경(브라우저)에서 사용자의 관점으로 UI/UX 결함과 기능적 버그를 찾아냅니다.
- **라우팅 발화**: "QA 진행해줘", "브라우저에서 테스트해줘", "화면 기능 확인해줘"

### 7. `release_engineer` (릴리스 엔지니어) — `Model: flash`
- **역할**: 테스트 스위트 구동, 지저분한 커밋 묶기(Squash), 배포 및 PR(Pull Request) 생성을 담당합니다.
- **특징**: 커밋 및 PR 생성 시 반드시 Conventional Commits 규칙을 따르며 본문에 **[Why(배경/이유), What(변경 내역), How to Test(테스트 방법), Related Issues(관련 이슈)]**를 강제적으로 포함합니다.
- **라우팅 발화**: "배포 준비해줘", "PR 만들어줘", "커밋 정리하고 올려줘"

### 8. `doc_engineer` (테크니컬 라이터) — `Model: flash`
- **역할**: 프로젝트의 기술 문서, API 스펙 및 사용자/개발자 가이드를 작성합니다.
- **라우팅 발화**: "문서 작성해줘", "가이드 작성해줘", "이 프로젝트 특징 정리해줘"

### 9. `doc_reviewer` (문서 리뷰어) — `Model: flash`
- **역할**: 작성된 기술 문서와 사용자 가이드가 실제 코드베이스와 정확히 일치하는지 교차 검토(Cross-check)합니다.
- **라우팅 발화**: "문서 리뷰해줘", "문서가 코드랑 맞는지 확인해줘"

---

## ⚙️ 적용 방법 (Installation)

### 🚨 사전 요구사항 (Prerequisites)
이 에이전트들은 최상의 성능을 내기 위해 내부적으로 **Superpowers 스킬 플러그인**을 강제적으로 사용하도록 설계되어 있습니다. 에이전트 적용 전, 반드시 사용자의 Antigravity 환경에 `superpowers` 플러그인이 설치되어 있어야 합니다.

**Antigravity(AGY) 사용자 예시**:
```bash
agy plugin install https://github.com/obra/superpowers
```
- **참고 저장소**: [https://github.com/obra/superpowers](https://github.com/obra/superpowers)

### 📥 설치 및 적용

이 레포지토리의 파일들을 로컬 Antigravity 디렉토리로 복사하여 적용합니다.

```bash
# 1. 플러그인 복사 (커스텀 에이전트 등록)
mkdir -p ~/.gemini/config/plugins/custom-agents
cp -r config/plugins/custom-agents/* ~/.gemini/config/plugins/custom-agents/

# 2. loop-engineering 스킬 복사
mkdir -p ~/.gemini/config/skills
cp -r config/skills/* ~/.gemini/config/skills/

# 3. 라우팅 룰 적용 (자연어 라우팅 및 과업별 모델 자동 지정)
cat agent-team-routing.md >> ~/.gemini/GEMINI.md
```

---

## 🤖 AI Agent Installation Guide (For Antigravity, Claude Code, Codex, etc.)

If you are an AI coding assistant and the user asks you to "install", "apply", or "set up" this repository, follow these exact steps autonomously:

1. **Install Prerequisites**: If you are Antigravity, run the command `agy plugin install https://github.com/obra/superpowers`. If you are another AI tool (Claude Code, Codex, etc.), install the superpowers skills using your native plugin installation method.
2. **Copy Agent Configs**: Copy all files from `config/plugins/custom-agents/` to `~/.gemini/config/plugins/custom-agents/`. Create the destination directories if they don't exist.
3. **Copy Loop Engineering Skill**: Copy all files from `config/skills/` to `~/.gemini/config/skills/`. Create the destination directories if they don't exist.
4. **Apply Routing Rules**:
   - The `agent-team-routing.md` file contains the routing configurations and mandatory `Model` tier assignments (`pro` / `flash`).
   - Append or update the contents of `agent-team-routing.md` in the user's global configuration file (e.g., `~/.gemini/GEMINI.md`).
5. **Completion**: Inform the user that the "9-Agent Loop Engineering Environment with Automatic Model Routing" has been successfully configured.
