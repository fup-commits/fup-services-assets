# FUP 서비스 에셋 저장소 — 작업 안내 (Claude Code용)

이 저장소는 **FUP GLOBAL PARTNERS 홈페이지(imweb)의 콘텐츠 소스**다. 홈페이지 위젯이
`https://raw.githubusercontent.com/fup-commits/fup-services-assets/main/...` 를 JS로 불러와 렌더한다.
**이 저장소는 PUBLIC이다. 시크릿·개인정보·비공개 자료를 절대 넣지 않는다.**

- 원격(origin)은 회사 계정 **fup-commits** 다. (과거 0sikH에서 이관 완료)
- 뉴스 데이터: `news/news-data.json` (배열). 사진: `news/` 폴더.
- 데이터 1건 필드: `id`, `date`("YYYY.MM.DD"), `title`, `desc`, `image`(파일명만), `link`.
- 화면은 `date` 내림차순 자동 정렬 → 최신 1건이 큰 카드. JSON 순서는 상관없다.

## 새 뉴스 기사 올리기

사용자가 "이 기사 올려줘 + 링크"라고 하면 다음을 수행한다.

1. **최신화**: `git pull --ff-only origin main`.
2. **정보 수집** (추측 금지, 원문 기준):
   - `title`: 원문 제목 전체.
   - `date`: 원문의 입력/등록/승인 날짜(수정일·오늘 아님) → `YYYY.MM.DD`.
   - `desc`: 2~3문장 직접 요약. 원문에 없는 성과·수치 추가 금지.
   - `link`: 원문 기사 전체 URL(`?idxno=...` 식별자 유지).
   - `image`: 기사 본문 대표 사진(광고·로고 아님). 사용자가 이미지를 직접 주면 그걸 쓴다.
   - **작성한 title/date/desc는 사용자에게 먼저 확인받는다.**
3. **이미지 저장**: 다음 번호 = `news/`의 `article-<N>-...` 중 **가장 큰 N + 1**
   (기사 개수 + 1이 아니다. 매번 실제로 다시 센다). 파일명 `article-<N>-<영문슬러그>.jpg`.
   실제 JPEG인지 확인. JSON의 `image`에는 **경로 없이 파일명만** 넣는다.
4. **JSON 추가**: `news/news-data.json` 배열에 1건 추가. `id`는 중복 없이(예: `news-YYYY-MMDD`).
5. **검사**: 6개 필드 존재 · 날짜 형식 · `id` 중복 없음 · 이미지 파일 존재 & JPEG · link 전체 유지.
   `node -e "JSON.parse(require('fs').readFileSync('news/news-data.json'))"` 로 JSON 유효성 확인.
6. **발행**: `git add news/ && git commit && git push origin main`.
   커밋·push는 사용자가 발행을 요청한 경우에만. push 후 raw URL과 홈페이지 뉴스 반영을 확인한다.

## 기사 내리기 / 되돌리기

"방금 기사 내려줘" 등 요청 시: `news-data.json`에서 해당 항목 제거(원하면 이미지 파일도 삭제) →
commit → `git push origin main`. 홈페이지는 새로고침 시 즉시 반영된다. git 이력으로 복원도 가능.

## 하지 말 것

- 기존 이미지 일괄 변환·이름 변경, 과거 기사 임의 수정/삭제(요청 없이).
- 일반 기사 추가 때 `sections/`·`snippets/`·`pages/`·화면 HTML 편집(뉴스는 JSON+이미지만).
- 시크릿·개인정보 커밋. 이 저장소는 공개다.
- 요청 없이 force-push·history rewrite.
