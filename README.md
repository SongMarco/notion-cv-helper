# Notion CV Helper

Notion에 작성된 CV(이력서)를 Claude Code에서 조회, 수정, 대화형 개선하는 플러그인.

## Quick Start

### 1. 마켓플레이스 등록

```
/plugin marketplace add https://github.com/SongMarco/notion-cv-helper
```

### 2. 플러그인 설치

```
/plugin install notion-cv-helper
```

### 3. 초기 설정

```
/notion-cv-setup ntn_your_token_here https://www.notion.so/Your-CV-abc123def456
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
/notion-cv-read
```

Notion CV 페이지를 읽어 섹션별 구조화된 마크다운으로 출력한다.

### CV 업데이트

```
/notion-cv-update Skills 섹션에 Rust 추가
```

마크다운이나 비정형 텍스트를 그대로 붙여넣어도 CV에 맞게 변환한다:

```
/notion-cv-update 경력에 추가:

### Anthropic (2025.01 - 현재)
- AI Engineer
- Claude Code 플러그인 시스템 설계 및 구현
- 플러그인 마켓플레이스 활성 사용자 300% 증가
```

### CV Assistant 에이전트

`notion-cv-assistant` 에이전트를 호출하면 대화형으로 CV를 개선할 수 있다.

- 현재 CV를 분석하고 개선점을 제안
- 질문을 통해 경력, 스킬, 프로젝트 정보를 수집
- 정량적 성과와 액션 동사를 활용한 문장으로 재구성
- 사용자 승인 후 Notion에 직접 반영

## Notion Integration 생성

1. [Notion Integrations](https://www.notion.so/profile/integrations/internal)에서 새 Integration을 생성한다
2. 이름: `CV Helper` (자유롭게 설정)
3. 권한: **Read content**, **Update content**, **Insert content** 체크
4. 생성 후 표시되는 토큰(`ntn_` 접두사)을 복사한다
5. Notion에서 CV 페이지를 열고 우측 상단 `...` > `Connections` > 생성한 Integration을 추가한다

## 라이선스

MIT
