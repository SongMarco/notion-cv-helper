# Notion CV Helper

Notion에 작성된 CV(이력서)를 Claude Code에서 조회, 수정, 대화형 개선하는 플러그인.

## Quick Start

### 1. 마켓플레이스 등록

```
/plugin marketplace add SongMarco/notion-cv-helper
```

### 2. 플러그인 설치

```
/plugin install notion-cv-helper
```

### 3. 초기 설정

```
/notion-cv-helper:setup ntn_your_token_here https://www.notion.so/Your-CV-abc123def456
```

Notion MCP 서버를 등록하고, `claude --continue`로 세션을 재시작하면 바로 사용할 수 있다.

> Notion Integration 토큰이 없다면 [Notion Integration 생성](#notion-integration-생성) 섹션을 참고한다.

## 사전 요구사항

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- [Node.js](https://nodejs.org/) (npx 사용을 위해 필요)
- Notion Integration 토큰 ([생성 방법](#notion-integration-생성))

## 사용법

### CV 조회

```
/notion-cv-helper:read
```

Notion CV 페이지를 읽어 섹션별 구조화된 마크다운으로 출력한다.

### CV 업데이트

```
/notion-cv-helper:update Skills 섹션에 Rust 추가
/notion-cv-helper:update Work Experience에 새 회사 추가: Anthropic, 2025.01 - 현재, AI Engineer
```

특정 섹션을 대상으로 블록 단위 수정을 수행한다.

### CV Assistant 에이전트

`notion-cv-helper:cv-assistant` 에이전트를 호출하면 대화형으로 CV를 개선할 수 있다.

- 현재 CV를 분석하고 개선점을 제안
- 질문을 통해 경력, 스킬, 프로젝트 정보를 수집
- 정량적 성과와 액션 동사를 활용한 문장으로 재구성
- 사용자 승인 후 Notion에 직접 반영

## Notion Integration 생성

1. [Notion Integrations](https://www.notion.so/my-integrations)에서 새 Integration을 생성한다
2. 이름: `CV Helper` (자유롭게 설정)
3. 권한: **Read content**, **Update content**, **Insert content** 체크
4. 생성 후 표시되는 토큰(`ntn_` 접두사)을 복사한다
5. Notion에서 CV 페이지를 열고 우측 상단 `...` > `Connections` > 생성한 Integration을 추가한다

## CV 페이지 구조

CV 페이지의 섹션 구조에 제약은 없다.
플러그인은 페이지 내 **모든 Heading 블록(H1/H2/H3)을 동적으로 탐색**하여 섹션으로 인식한다.

- 섹션 이름, 순서, 개수를 자유롭게 구성할 수 있다
- 한국어, 영어 등 어떤 언어의 Heading이든 인식한다
- 업데이트 시 페이지 전체를 덮어쓰지 않고, 개별 블록 단위로 조작한다

## 직접 설치 (개발자용)

```bash
git clone https://github.com/SongMarco/notion-cv-helper.git
claude --plugin-dir ./notion-cv-helper
```

## 라이선스

MIT
