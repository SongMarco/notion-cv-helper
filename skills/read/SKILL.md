---
name: read
description: Notion에서 CV를 조회하여 섹션별 구조화된 마크다운으로 출력한다
allowed-tools: mcp__notion__*, Read
---

# CV 조회

Notion CV 페이지를 읽어 구조화된 형태로 표시한다.

## 워크플로우

1. `.env` 파일에서 `NOTION_CV_PAGE_ID` 값을 읽는다.
   - `.env` 파일이 없거나 `NOTION_CV_PAGE_ID`가 없는 경우: "`.env` 파일에 `NOTION_CV_PAGE_ID`가 설정되지 않았습니다. `/notion-cv:setup`을 실행하여 초기 설정을 완료하세요." 출력 후 중단한다.

2. Notion MCP를 통해 CV 페이지의 블록 목록을 조회한다.
   - `retrieve_block_children` API를 `page_size: 30`으로 호출한다.
   - `has_more`가 `true`이면 `next_cursor`로 다음 페이지를 조회한다.
   - 중첩된 블록(`has_children: true`)이 있으면 재귀적으로 하위 블록도 조회한다.

3. **MCP 응답을 즉시 파싱하여 마크다운으로 변환한다** (토큰 절약 필수).
   - 각 블록에서 **type, id, rich_text의 plain_text**만 추출한다.
   - parent, created_time, last_edited_time 등 불필요한 필드는 무시한다.
   - **원본 JSON을 그대로 반복하거나 출력하지 않는다.**
   - 모든 Heading 블록(`heading_1`, `heading_2`, `heading_3`)을 섹션 구분자로 인식한다.
   - 고정된 섹션 목록에 의존하지 않는다 - 페이지에 존재하는 Heading을 그대로 섹션으로 취급한다.
   - 각 섹션의 **Heading 레벨, 제목, Block ID를 함께 기록**한다.

4. 결과를 구조화된 마크다운으로 출력한다.
   - 각 섹션 제목 옆에 Block ID를 주석으로 표기한다 (디버깅/업데이트 참조용).
   - 빈 섹션은 "(내용 없음)"으로 표시한다.

## 출력 형식 예시

```markdown
## 자기소개

<!-- block_id: abc123 -->

풀스택 개발자로서 5년간 ...

## 경력

<!-- block_id: def456 -->

### 회사명 (2022.03 - 현재)

- 역할: Senior Developer
- 주요 성과: ...

### 이전 회사 (2019.01 - 2022.02)

<!-- block_id: uvw012 -->

- ...

## 기술 스택

<!-- block_id: ghi789 -->

- **Languages**: TypeScript, Python, Go
```

Heading의 이름, 레벨, 개수는 실제 페이지 구조에 따라 달라진다.
위는 예시일 뿐이며, 실제 출력은 페이지의 Heading을 그대로 반영한다.

## 에러 처리

- `.env` 파일 없음 또는 `NOTION_CV_PAGE_ID` 미설정: "`/notion-cv:setup`을 실행하여 초기 설정을 완료해 주세요." 출력
- MCP 연결 실패 / Notion API 호출 불가: "`claude mcp list`로 Notion MCP 서버가 등록되어 있는지 확인하세요. 등록되지 않았다면 `/notion-cv:setup`을 실행하고, `claude --continue`로 세션을 재시작하세요." 출력
- 페이지 접근 불가: "Notion Integration에 CV 페이지 접근 권한을 부여해 주세요. `/notion-cv:setup` 실행 시 안내를 확인할 수 있습니다." 출력
- API 오류: 에러 메시지를 사용자에게 전달
