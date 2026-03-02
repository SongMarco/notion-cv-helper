---
name: setup
description: Notion 토큰과 CV 페이지 URL을 인자로 받아 .env 및 .mcp.json 파일을 생성한다
argument-hint: NOTION_TOKEN=<토큰> NOTION_CV_PAGE_URL=<URL>
allowed-tools: mcp__notion__*, Write, Read
---

# 초기 설정

인자로 전달받은 Notion 토큰과 CV 페이지 URL로 `.env`와 `.mcp.json` 파일을 생성한다.

## 사용법

```
/notion-cv:setup NOTION_TOKEN=ntn_xxxxx NOTION_CV_PAGE_URL=https://www.notion.so/My-CV-abc123def456
```

## 워크플로우

1. `$ARGUMENTS`에서 `NOTION_TOKEN`과 `NOTION_CV_PAGE_URL` 값을 파싱한다.
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
   # Notion Integration Token
   NOTION_TOKEN=ntn_xxxxx

   # Notion CV Page ID
   NOTION_CV_PAGE_ID=abc123def456...
   ```

5. `.mcp.json` 파일을 생성하거나 업데이트한다.
   - 기존 `.mcp.json`이 있으면 Read로 읽어서 `mcpServers`에 `notion` 항목을 추가(병합)한다.
   - 기존 `.mcp.json`이 없으면 새로 생성한다.
   - 기존에 `notion` 서버가 이미 설정되어 있으면 덮어쓴다.
   ```json
   {
     "mcpServers": {
       "notion": {
         "command": "sh",
         "args": ["-c", "set -a && [ -f \"$PWD/.env\" ] && . \"$PWD/.env\" && set +a && exec npx -y @notionhq/notion-mcp-server"],
         "env": {}
       }
     }
   }
   ```

6. 설정 완료 메시지를 출력한다.
   - ".env 파일과 .mcp.json 파일이 생성되었습니다."
   - "Notion에서 CV 페이지에 Integration 연결을 확인해 주세요."
     - 페이지 우측 상단 "..." > "Connections" > 생성한 Integration 추가
   - "`claude --continue`로 세션을 이어가면 Notion 연결이 활성화됩니다."
   - "`/notion-cv:read`로 CV를 조회할 수 있습니다."

## Notion Integration 미생성 시 안내

```
## Notion Integration 생성 방법

1. https://www.notion.so/my-integrations 에서 새 Integration을 생성
2. 권한: Read content, Update content, Insert content 체크
3. 생성 후 토큰(ntn_ 접두사)을 복사
4. CV 페이지에서 ... > Connections > 생성한 Integration 추가
```

## 주의사항

- 토큰 값을 터미널 출력이나 로그에 절대 노출하지 않는다
- `.env` 파일의 토큰 값을 출력할 때 마스킹한다 (예: `ntn_****`)
