# CV Helper

Notion에 작성된 CV(이력서)를 Claude Code의 Skill/Agent를 통해 관리하는 도구.

## 환경변수

| 변수명              | 필수 | 설명                                                   |
| ------------------- | ---- | ------------------------------------------------------ |
| `NOTION_TOKEN`      | Y    | Notion Integration 토큰 (`ntn_` 또는 `secret_` 접두사) |
| `NOTION_CV_PAGE_ID` | Y    | CV 페이지 ID (32자 hex, 하이픈 없이)                   |

환경변수는 시스템 환경변수 또는 프로젝트 루트의 `.env` 파일로 설정한다.

## 슬래시 커맨드

| 커맨드              | 설명                                                   |
| ------------------- | ------------------------------------------------------ |
| `/cv-read`          | Notion CV를 조회하여 섹션별 구조화된 마크다운으로 출력 |
| `/cv-update <내용>` | CV의 특정 섹션을 업데이트                              |

## 에이전트

| 에이전트       | 설명                                       |
| -------------- | ------------------------------------------ |
| `cv-assistant` | 대화형으로 CV를 작성하고 개선하는 에이전트 |

## Notion CV 페이지 구조 규칙

CV 페이지는 **Heading 2 블록을 섹션 앵커**로 사용한다.
AI가 정확한 위치에 내용을 추가/수정하려면 다음 헤딩 제목을 유지해야 한다:

```
## Summary
## Work Experience
## Projects
## Skills
## Education
## Certifications
## Languages
```

### Block ID 기반 업데이트 원칙

1. `/cv-read` 실행 시 각 섹션의 Block ID를 함께 파싱한다
2. `/cv-update` 실행 시 해당 섹션의 Block ID를 기준으로 블록 단위 수정을 수행한다
3. 페이지 전체를 덮어쓰지 않는다 - 항상 개별 블록을 조작한다
4. 새 항목 추가 시 `append_block_children`을 사용하여 해당 섹션 하위에 삽입한다

## 보안

- `NOTION_TOKEN`을 코드나 커밋에 절대 포함하지 않는다
- `.env` 파일은 `.gitignore`에 등록되어 있다
- 토큰이 노출된 경우 즉시 Notion Integration 페이지에서 재생성한다
