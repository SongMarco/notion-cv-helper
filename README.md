# Notion CV Helper (ncv)

Notion에 작성된 CV(이력서)를 Claude Code에서 관리하는 플러그인.
조회, 수정, 대화형 개선을 지원한다.

## 사전 요구사항

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- [Node.js](https://nodejs.org/) (npx 사용을 위해 필요)
- Notion Integration 토큰 ([생성 방법](#notion-integration-생성))

## 설치

### 마켓플레이스에서 설치

```bash
claude plugin install ncv --marketplace songmarco-plugins
```

### 직접 설치 (개발자용)

```bash
git clone https://github.com/youngchansong/cv-helper.git
claude --plugin-dir ./cv-helper
```

## 환경변수 설정

shell profile (`~/.zshrc` 또는 `~/.bashrc`)에 다음을 추가한다:

```bash
export NOTION_TOKEN="ntn_your_token_here"
export NOTION_CV_PAGE_ID="32자리_hex_page_id"
```

설정 후 터미널을 재시작하거나 `source ~/.zshrc`를 실행한다.

> `/ncv:setup`을 실행하면 환경변수 설정 상태를 검증하고 안내를 받을 수 있다.

### Notion Integration 생성

1. [Notion Integrations](https://www.notion.so/my-integrations)에서 새 Integration을 생성한다
2. 이름: `CV Helper` (자유롭게 설정)
3. 권한: **Read content**, **Update content**, **Insert content** 체크
4. 생성 후 표시되는 토큰(`ntn_` 접두사)을 복사한다

### CV 페이지에 Integration 연결

1. Notion에서 CV 페이지를 연다
2. 우측 상단 `...` 메뉴 > `Connections` > 생성한 Integration을 추가한다

### CV 페이지 ID 확인

Notion에서 CV 페이지 URL을 확인한다:

```
https://www.notion.so/Your-CV-Title-{32자리_hex}
                                     ^^^^^^^^^^^^^^^^
                                     이 부분이 NOTION_CV_PAGE_ID
```

## 사용법

### 환경변수 검증

```
/ncv:setup
```

환경변수가 올바르게 설정되었는지 확인하고, 미설정 시 설정 방법을 안내한다.
Notion URL을 인자로 전달하면 Page ID를 자동 추출하여 표시한다.

### CV 조회

```
/ncv:read
```

Notion CV 페이지를 읽어 섹션별 구조화된 마크다운으로 출력한다.
각 섹션의 Block ID도 함께 표시하여 업데이트 시 참조할 수 있다.

### CV 업데이트

```
/ncv:update Skills 섹션에 Rust 추가
/ncv:update Work Experience에 새 회사 추가: Anthropic, 2025.01 - 현재, AI Engineer
/ncv:update Summary를 "시니어 백엔드 개발자"로 수정
```

특정 섹션을 대상으로 블록 단위 수정을 수행한다.

### CV Assistant 에이전트

`ncv:cv-assistant` 에이전트를 호출하면 대화형으로 CV를 개선할 수 있다.

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

1. **환경변수 이전**: `.env` 파일의 값을 shell profile로 옮긴다
   ```bash
   # ~/.zshrc 또는 ~/.bashrc에 추가
   export NOTION_TOKEN="ntn_your_token_here"
   export NOTION_CV_PAGE_ID="your_page_id_here"
   ```
2. **플러그인 설치**: 위 [설치](#설치) 섹션을 따른다
3. **커맨드 변경**:
   | 이전 | 현재 |
   |------|------|
   | `/cv-init` | `/ncv:setup` |
   | `/cv-read` | `/ncv:read` |
   | `/cv-update` | `/ncv:update` |
   | `cv-assistant` 에이전트 | `ncv:cv-assistant` 에이전트 |
4. **기존 파일 정리**: `.claude/` 디렉토리와 `.env` 파일은 더 이상 필요하지 않다

## 라이선스

MIT
