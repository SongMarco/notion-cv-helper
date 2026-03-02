# Notion CV Helper (notion-cv)

Notion에 작성된 CV(이력서)를 Claude Code에서 관리하는 플러그인.
조회, 수정, 대화형 개선을 지원한다.

## 사전 요구사항

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- [Node.js](https://nodejs.org/) (npx 사용을 위해 필요)
- Notion Integration 토큰 ([생성 방법](#notion-integration-생성))

## 설치

### 마켓플레이스에서 설치

```bash
# 1. 마켓플레이스 등록
/plugin marketplace add SongMarco/notion-cv-helper

# 2. 플러그인 설치
/plugin install notion-cv@songmarco-plugins
```

### 직접 설치 (개발자용)

```bash
git clone https://github.com/SongMarco/notion-cv-helper.git
claude --plugin-dir ./notion-cv-helper
```

## 초기 설정

플러그인 설치 후 다음 순서로 설정한다:

1. `/notion-cv:setup`으로 토큰과 CV 페이지를 설정한다:
   ```
   /notion-cv:setup ntn_your_token_here https://www.notion.so/Your-CV-abc123def456
   ```
   Notion MCP 서버를 `claude mcp add`로 등록하고, Page ID를 `.env`에 저장한다.

2. `claude --continue`로 세션을 재시작한다 (Notion MCP 서버 활성화).

3. `/notion-cv:read`로 CV를 조회하여 동작을 확인한다.

### Notion Integration 생성

1. [Notion Integrations](https://www.notion.so/my-integrations)에서 새 Integration을 생성한다
2. 이름: `CV Helper` (자유롭게 설정)
3. 권한: **Read content**, **Update content**, **Insert content** 체크
4. 생성 후 표시되는 토큰(`ntn_` 접두사)을 복사한다

### CV 페이지에 Integration 연결

1. Notion에서 CV 페이지를 연다
2. 우측 상단 `...` 메뉴 > `Connections` > 생성한 Integration을 추가한다

## 사용법

### CV 조회

```
/notion-cv:read
```

Notion CV 페이지를 읽어 섹션별 구조화된 마크다운으로 출력한다.
각 섹션의 Block ID도 함께 표시하여 업데이트 시 참조할 수 있다.

### CV 업데이트

```
/notion-cv:update Skills 섹션에 Rust 추가
/notion-cv:update Work Experience에 새 회사 추가: Anthropic, 2025.01 - 현재, AI Engineer
/notion-cv:update Summary를 "시니어 백엔드 개발자"로 수정
```

특정 섹션을 대상으로 블록 단위 수정을 수행한다.

### CV Assistant 에이전트

`notion-cv:cv-assistant` 에이전트를 호출하면 대화형으로 CV를 개선할 수 있다.

- 현재 CV를 분석하고 개선점을 제안
- 질문을 통해 경력, 스킬, 프로젝트 정보를 수집
- 정량적 성과와 액션 동사를 활용한 문장으로 재구성
- 사용자 승인 후 Notion에 직접 반영

## CV 페이지 구조

CV 페이지의 섹션 구조에 제약은 없다.
플러그인은 페이지 내 **모든 Heading 블록(H1/H2/H3)을 동적으로 탐색**하여 섹션으로 인식한다.

- 섹션 이름, 순서, 개수를 자유롭게 구성할 수 있다
- 한국어, 영어 등 어떤 언어의 Heading이든 인식한다
- 업데이트 시 페이지 전체를 덮어쓰지 않고, 개별 블록 단위로 조작한다

## 기존 사용자 마이그레이션

이전 버전(`.claude/` 기반)에서 플러그인으로 전환하는 경우:

1. **플러그인 설치**: 위 [설치](#설치) 섹션을 따른다
2. **초기 설정 재실행**: `/notion-cv:setup`으로 토큰과 페이지 URL을 다시 설정한다
3. **커맨드 변경**:
   | 이전 | 현재 |
   |------|------|
   | `/cv-init` | `/notion-cv:setup` |
   | `/cv-read` | `/notion-cv:read` |
   | `/cv-update` | `/notion-cv:update` |
   | `cv-assistant` 에이전트 | `notion-cv:cv-assistant` 에이전트 |
4. **기존 파일 정리**: `.claude/` 디렉토리는 더 이상 필요하지 않다

## 라이선스

MIT
