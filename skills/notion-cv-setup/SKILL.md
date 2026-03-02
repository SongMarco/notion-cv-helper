---
name: notion-cv-setup
description: Notion 토큰과 CV 페이지 URL을 인자로 받아 MCP 서버를 등록하고 .env 파일을 생성한다
argument-hint: <토큰> <CV 페이지 URL>
allowed-tools: mcp__notion__*, Write, Read, Bash
---

# 초기 설정

인자로 전달받은 Notion 토큰과 CV 페이지 URL로 MCP 서버를 등록하고 `.env` 파일을 생성한다.

## 사용법

```
/notion-cv-setup ntn_xxxxx https://www.notion.so/My-CV-abc123def456
```

## 워크플로우

1. `$ARGUMENTS`에서 토큰과 URL을 파싱한다.
   - `ntn_` 또는 `secret_`로 시작하는 값을 토큰으로 인식한다.
   - `https://`로 시작하는 값 또는 32자 hex 문자열을 페이지 URL/ID로 인식한다.
   - `NOTION_TOKEN=`, `NOTION_CV_PAGE_URL=` 접두사가 붙어 있으면 제거 후 값만 사용한다 (하위 호환).
   - 둘 중 하나라도 누락되면 사용법을 안내하고 중단한다.

2. 입력값을 검증한다.
   - Token: `ntn_` 또는 `secret_` 접두사 확인
     - 검증 실패 시: "토큰은 ntn_ 또는 secret_ 으로 시작해야 합니다." 출력 후 중단
   - Page URL에서 Page ID를 추출한다:
     - URL의 경로 마지막 부분에서 32자 hex를 추출한다
     - 패턴: `https://www.notion.so/...` 에서 마지막 `-` 뒤의 hex 또는 경로 끝의 32자
     - 하이픈이 포함된 UUID 형태면 하이픈을 제거한다
     - 쿼리 파라미터(`?v=...` 등)는 무시한다
     - 32자 hex ID가 직접 입력된 경우도 허용한다
   - 추출 실패 시: "URL에서 Page ID를 추출할 수 없습니다." 출력 후 중단

3. 기존 `.env` 파일이 있는지 확인한다.
   - 이미 존재하면 "기존 .env 파일을 덮어씁니다." 라고 안내한다.

4. `.env` 파일을 생성한다.
   ```
   # Notion CV Page ID
   NOTION_CV_PAGE_ID=abc123def456...
   ```
   - 토큰은 `.env`에 저장하지 않는다 (MCP 설정에서 관리).

5. `claude mcp add` 명령어로 Notion MCP 서버를 등록한다.
   - Bash 도구로 다음 명령어를 실행한다:
     ```bash
     claude mcp add -e NOTION_TOKEN=<토큰값> notion -- npx -y @notionhq/notion-mcp-server
     ```
   - 이미 `notion` MCP 서버가 등록되어 있으면 먼저 제거 후 재등록한다:
     ```bash
     claude mcp remove notion 2>/dev/null; claude mcp add -e NOTION_TOKEN=<토큰값> notion -- npx -y @notionhq/notion-mcp-server
     ```
   - 등록 후 `claude mcp list`로 정상 등록 여부를 확인한다.

6. 설정 완료 메시지를 출력한다.
   - "Notion MCP 서버가 등록되고 .env 파일이 생성되었습니다."
   - "Notion에서 CV 페이지에 Integration 연결을 확인해 주세요."
     - 페이지 우측 상단 "..." > "Connections" > 생성한 Integration 추가
   - "`claude --continue`로 세션을 이어가면 Notion MCP 서버가 활성화됩니다."
   - "`/notion-cv-read`로 CV를 조회할 수 있습니다."

## Notion Integration 미생성 시 안내

```
## Notion Integration 생성 방법

1. https://www.notion.so/profile/integrations/internal 에서 새 Integration을 생성
2. 권한: Read content, Update content, Insert content 체크
3. 생성 후 토큰(ntn_ 접두사)을 복사
4. CV 페이지에서 ... > Connections > 생성한 Integration 추가
```

## 주의사항

- 토큰 값을 터미널 출력이나 로그에 절대 노출하지 않는다
- `.env` 파일의 토큰 값을 출력할 때 마스킹한다 (예: `ntn_****`)
