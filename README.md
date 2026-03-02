# Notion CV Helper

Claude Code의 Skill/Agent 체계와 Notion MCP를 활용하여 CV(이력서)를 관리하는 도구.

## 설치

### 1. Notion Integration 생성

1. [Notion Integrations](https://www.notion.so/my-integrations)에서 새 Integration을 생성한다
2. 이름: `CV Helper` (자유롭게 설정)
3. 권한: **Read content**, **Update content**, **Insert content** 체크
4. 생성 후 표시되는 토큰(`ntn_` 또는 `secret_` 접두사)을 복사한다

### 2. CV 페이지에 Integration 연결

1. Notion에서 CV 페이지를 연다
2. 우측 상단 `...` 메뉴 > `Connections` > `CV Helper` Integration을 추가한다

### 3. CV 페이지 ID 확인

1. Notion에서 CV 페이지를 연다
2. URL에서 페이지 ID를 추출한다:
   ```
   https://www.notion.so/Your-CV-Title-{32자리_hex}
                                       ^^^^^^^^^^^^^^^^
                                       이 부분이 페이지 ID
   ```
3. 하이픈 없이 32자리 hex 문자열을 사용한다

### 4. 환경변수 설정

Claude Code에서 `/cv-init` 커맨드로 간편하게 설정한다:

```
/cv-init NOTION_TOKEN=ntn_your_token_here NOTION_CV_PAGE_URL=https://www.notion.so/Your-CV-abc123def456
```

URL에서 Page ID를 자동 추출하여 `.env` 파일을 생성한다.

또는 직접 `.env` 파일을 생성할 수도 있다:

```bash
cp .env.example .env
# .env 파일을 열어 값 입력
```

### 5. 프로젝트 Clone

```bash
git clone https://github.com/youngchansong/notion-cv-helper.git
cd notion-cv-helper
```

## 사용법

`notion-cv-helper` 디렉토리에서 Claude Code를 실행한다.

### 초기 설정

```
/cv-init NOTION_TOKEN=ntn_xxxxx NOTION_CV_PAGE_URL=https://www.notion.so/My-CV-abc123def456
```

Notion 토큰과 CV 페이지 URL을 인자로 전달하면 `.env` 파일을 자동 생성한다.

### CV 조회

```
/cv-read
```

Notion CV 페이지를 읽어 섹션별 구조화된 마크다운으로 출력한다.
각 섹션의 Block ID도 함께 표시하여 업데이트 시 참조할 수 있다.

### CV 업데이트

```
/cv-update Skills 섹션에 Rust 추가
/cv-update Work Experience에 새 회사 추가: Anthropic, 2025.01 - 현재, AI Engineer
/cv-update Summary를 "시니어 백엔드 개발자"로 수정
```

특정 섹션을 대상으로 블록 단위 수정을 수행한다.
기존 레이아웃을 보존하면서 정확한 위치에 내용을 반영한다.

### CV Assistant 에이전트

Claude Code에서 `cv-assistant` 에이전트를 호출하면 대화형으로 CV를 개선할 수 있다.

- 현재 CV를 분석하고 개선점을 제안
- 질문을 통해 경력, 스킬, 프로젝트 정보를 수집
- 정량적 성과와 액션 동사를 활용한 문장으로 재구성
- 사용자 승인 후 Notion에 직접 반영

## CV 페이지 구조

CV 페이지의 섹션 구조에 특별한 제약은 없다.
Skill/Agent는 페이지 내 **모든 Heading 블록(H1/H2/H3)을 동적으로 탐색**하여 섹션으로 인식한다.

- 섹션 이름, 순서, 개수를 자유롭게 구성할 수 있다
- 한국어, 영어 등 어떤 언어의 Heading이든 인식한다
- Heading이 없는 페이지도 전체를 하나의 블록 리스트로 처리한다

## 기술 구조

```
notion-cv-helper/
├── .claude/
│   ├── CLAUDE.md                    # 프로젝트 지시사항
│   ├── skills/
│   │   ├── cv-init/SKILL.md         # 초기 설정 Skill
│   │   ├── cv-read/SKILL.md         # CV 조회 Skill
│   │   └── cv-update/SKILL.md       # CV 업데이트 Skill
│   └── agents/
│       └── cv-assistant.md          # CV 빌딩 에이전트
├── .mcp.json                        # Notion MCP 설정
├── README.md
├── ROADMAP.md
└── LICENSE
```

전통적인 애플리케이션 코드 없이, Claude Code의 Skill/Agent/MCP 체계만으로 동작한다.
