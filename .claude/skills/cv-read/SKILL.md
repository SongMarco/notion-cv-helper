---
name: cv-read
description: Notion에서 CV를 조회하여 섹션별 구조화된 마크다운으로 출력한다
allowed-tools: mcp__notion__*
---

# CV 조회

Notion CV 페이지를 읽어 구조화된 형태로 표시한다.

## 워크플로우

1. 환경변수 `NOTION_CV_PAGE_ID`에서 CV 페이지 ID를 확인한다.
   - 설정되지 않은 경우: 사용자에게 설정 방법을 안내하고 중단한다.

2. Notion MCP를 통해 CV 페이지의 블록 목록을 조회한다.
   - `retrieve_block_children` API로 페이지의 모든 블록을 가져온다.
   - 중첩된 블록이 있으면 재귀적으로 하위 블록도 조회한다.

3. 블록을 섹션별로 파싱한다.
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

- `NOTION_CV_PAGE_ID` 미설정: "환경변수 NOTION_CV_PAGE_ID를 설정해 주세요." 출력
- 페이지 접근 불가: "Notion Integration에 CV 페이지 접근 권한을 부여해 주세요." 출력
- API 오류: 에러 메시지를 사용자에게 전달
