---
name: update
description: 사용자가 전달한 내용으로 Notion CV의 특정 섹션을 업데이트한다
argument-hint: <업데이트할 내용 또는 섹션명>
allowed-tools: mcp__notion__*, Read
---

# CV 업데이트

사용자의 요청에 따라 Notion CV 페이지의 특정 섹션을 수정하거나 새 항목을 추가한다.

## 워크플로우

1. `$ARGUMENTS`에서 업데이트 요청을 파싱한다.
   - 대상 섹션 식별 (예: "경력에 새 회사 추가", "스킬에 Go 추가")
   - 업데이트 내용 추출

2. `.env` 파일에서 `NOTION_CV_PAGE_ID` 값을 읽는다.
   - `.env` 파일이 없거나 `NOTION_CV_PAGE_ID`가 없는 경우: "`.env` 파일에 `NOTION_CV_PAGE_ID`가 설정되지 않았습니다. `/notion-cv:setup`을 실행하여 초기 설정을 완료하세요." 출력 후 중단한다.

3. Notion MCP를 통해 현재 CV 페이지의 블록을 조회한다.
   - `retrieve_block_children`로 전체 블록 목록을 가져온다.
   - 모든 Heading 블록(`heading_1`, `heading_2`, `heading_3`)을 섹션 구분자로 인식한다.
   - 고정된 섹션 목록에 의존하지 않는다 - 페이지에 존재하는 Heading을 그대로 사용한다.
   - 각 섹션의 Block ID를 파악한다.

4. 대상 섹션을 찾고 업데이트 방식을 결정한다.

   **기존 블록 수정 시:**
   - 해당 블록의 Block ID로 `update_block`을 호출한다.
   - 블록의 type에 맞는 형식으로 내용을 구성한다.

   **새 항목 추가 시:**
   - 해당 섹션의 마지막 블록 뒤에 `append_block_children`으로 삽입한다.
   - 섹션의 기존 형식(글머리 기호, 번호 목록 등)을 따른다.

   **블록 삭제 시:**
   - `delete_block`으로 해당 블록을 제거한다.
   - 삭제 전 사용자에게 확인을 요청한다.

5. 변경 결과를 요약하여 출력한다.
   - 어떤 섹션이 변경되었는지
   - 추가/수정/삭제된 내용 요약
   - 변경 후 해당 섹션의 현재 상태

## 사용 예시

```
/notion-cv:update Skills 섹션에 Rust 추가
/notion-cv:update Work Experience에 새 회사 추가: Anthropic, 2025.01 - 현재, AI Engineer
/notion-cv:update Summary를 "시니어 백엔드 개발자"로 수정
```

## Block 조작 원칙

1. **절대 페이지 전체를 덮어쓰지 않는다** - 개별 블록 단위로만 조작
2. 수정 대상 블록의 Block ID를 정확히 식별한 후 작업한다
3. 섹션 Heading 블록은 기본적으로 수정하지 않는다 (앵커 역할). 사용자가 명시적으로 요청한 경우에만 수정한다.
4. 기존 포맷팅(볼드, 이탤릭, 링크 등)을 보존한다
5. 변경 전 현재 상태를 확인하고, 변경 후 결과를 검증한다

## 에러 처리

- 섹션을 찾을 수 없음: 현재 페이지에서 발견된 Heading 목록을 표시하여 사용자가 정확한 섹션명을 선택하도록 안내
- MCP 연결 실패 / Notion API 호출 불가: "`claude mcp list`로 Notion MCP 서버가 등록되어 있는지 확인하세요. 등록되지 않았다면 `/notion-cv:setup`을 실행하고, `claude --continue`로 세션을 재시작하세요." 출력
- Block ID 조회 실패: `/notion-cv:read`를 먼저 실행하도록 안내
- API 오류: 에러 메시지를 사용자에게 전달하고, 부분 변경이 있었다면 현재 상태를 출력
