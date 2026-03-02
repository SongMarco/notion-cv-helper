---
name: setup
description: Notion 환경변수 설정을 검증하고 안내한다
argument-hint: [Notion CV 페이지 URL (선택)]
allowed-tools: mcp__notion__*
---

# 환경변수 검증 및 설정 안내

Notion CV 관리에 필요한 환경변수가 올바르게 설정되었는지 검증하고, 미설정 시 설정 방법을 안내한다.

## 워크플로우

1. `$ARGUMENTS`에 Notion URL이 포함되어 있는지 확인한다.
   - URL이 있으면 Page ID를 추출하여 표시한다:
     - URL의 경로 마지막 부분에서 32자 hex를 추출한다
     - 하이픈이 포함된 UUID 형태면 하이픈을 제거한다
     - 쿼리 파라미터(`?v=...` 등)는 무시한다
   - "추출된 Page ID: `<id>` - 이 값을 NOTION_CV_PAGE_ID 환경변수로 설정하세요." 출력

2. 환경변수를 확인한다.
   - `NOTION_TOKEN`: 설정 여부 확인
   - `NOTION_CV_PAGE_ID`: 설정 여부 확인

3. **환경변수가 모두 설정된 경우:**
   - Notion MCP를 통해 CV 페이지 접근을 검증한다
     - `retrieve_block_children`으로 페이지 조회를 시도한다
   - 성공 시: "Notion 연결이 정상입니다. `/ncv:read`로 CV를 조회할 수 있습니다." 출력
   - 실패 시: 에러 원인을 분석하여 안내한다 (토큰 만료, 페이지 권한 미부여 등)

4. **환경변수가 미설정된 경우:**
   아래 설정 안내를 출력한다:

   ```
   ## 환경변수 설정 방법

   shell profile (~/.zshrc 또는 ~/.bashrc)에 다음을 추가하세요:

   export NOTION_TOKEN="ntn_your_token_here"
   export NOTION_CV_PAGE_ID="32자리_hex_page_id"

   설정 후 터미널을 재시작하거나 `source ~/.zshrc`를 실행하세요.
   ```

5. **Notion Integration 미생성 시 안내:**

   ```
   ## Notion Integration 생성 방법

   1. https://www.notion.so/my-integrations 에서 새 Integration을 생성
   2. 권한: Read content, Update content, Insert content 체크
   3. 생성 후 토큰(ntn_ 접두사)을 복사
   4. CV 페이지에서 ... > Connections > 생성한 Integration 추가
   ```

## 주의사항

- 토큰 값을 터미널 출력이나 로그에 절대 노출하지 않는다
- 토큰 존재 여부만 확인하고, 값 자체는 출력하지 않는다
- `.env` 파일을 직접 생성하거나 수정하지 않는다 - 환경변수 설정만 안내한다
