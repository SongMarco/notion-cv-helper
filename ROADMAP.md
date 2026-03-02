# Roadmap

## 완료

### 플러그인 전환 (v1.0.0)

- [x] Claude Code 플러그인 포맷으로 전환
- [x] `/notion-cv-helper:setup`, `/notion-cv-helper:read`, `/notion-cv-helper:update` 스킬 구현
- [x] `notion-cv-helper:cv-assistant` 에이전트 구현
- [x] 환경변수 직접 참조 방식으로 MCP 설정 변경 (`.env` 의존 제거)
- [x] Self-hosted 마켓플레이스 배포 구조 준비

## 현재 제한사항

- **Notion API 블록 제한**: 한 번의 API 호출로 최대 100개 블록만 조회 가능. 페이지가 길면 페이지네이션 필요.
- **Rich Text 제한**: Notion API의 Rich Text는 일부 복잡한 서식(예: 테이블 내 중첩 블록)을 완전히 지원하지 않을 수 있음.
- **동시 편집 충돌**: 사용자가 Notion에서 직접 편집하는 동안 API로 수정하면 충돌 가능성 있음.
- **이미지/파일 첨부**: 현재 텍스트 기반 블록만 지원. 이미지 업로드는 Notion API 제한으로 미지원.

## 향후 기능

### Phase 1: 내보내기

- [ ] 마크다운 형식 내보내기 (`/notion-cv-helper:export md`)
- [ ] PDF 내보내기 (Puppeteer 또는 외부 서비스 활용)
- [ ] HTML 내보내기 (커스텀 템플릿 적용)

### Phase 2: JD 기반 CV 최적화

- [ ] JD(Job Description) 텍스트 또는 URL을 입력받아 분석
- [ ] JD와 현재 CV의 키워드 매칭 분석
- [ ] JD에 맞춰 CV 강조점 재구성 제안
- [ ] 지원 회사별 CV 버전 관리

### Phase 3: 다국어 지원

- [ ] 한국어/영어 CV 동시 관리
- [ ] 번역 스킬 추가 (`/notion-cv-helper:translate`)
- [ ] 언어별 CV 페이지 자동 동기화

### Phase 4: 템플릿 시스템

- [ ] 산업/직군별 CV 템플릿 제공
- [ ] 빈 Notion 페이지에 템플릿 자동 생성
- [ ] 커스텀 섹션 추가 지원

### Phase 5: 분석 및 인사이트

- [ ] CV 완성도 점수 산출
- [ ] 경력 타임라인 시각화
- [ ] 스킬 매트릭스 자동 생성
- [ ] 업계 트렌드 대비 스킬 갭 분석

### Phase 6: 공식 마켓플레이스

- [ ] Claude Code 공식 마켓플레이스 등록
- [ ] 플러그인 자동 업데이트 지원
