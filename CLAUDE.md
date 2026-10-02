# CLAUDE.md

이 파일은 Claude Code(claude.ai/code)가 이 저장소에서 작업할 때 참고하는 안내서입니다.

## 프로젝트 소개

가상의 바닷가 마을 '해솔마을'을 소개하는 정적 여행 안내 웹사이트입니다. `PRD.md`가 기준 문서입니다. 사이트의 목적, 페이지 목록, 메뉴와 링크 규칙, 만들지 않을 기능(로그인, 예약, 결제, 방명록)이 모두 여기에 적혀 있습니다. 화면에 보이는 글은 모두 한국어로 쓰고, 사용자도 한국어로 대화합니다.

빌드 단계, 패키지 관리자, 린트, 테스트는 없습니다. 사이트는 HTML 파일과 JPG 사진만으로 이루어져 있습니다.

## 실행과 확인

- **내 컴퓨터에서 미리 보기:** `python3 -m http.server`를 실행하고 `http://localhost:8000/`을 엽니다. 사진 경로가 상대 경로라서 HTML 파일을 직접 열어도 됩니다.
- **실제 사이트:** GitHub Pages가 `main` 브랜치의 최상위 폴더를 https://nana990710san-jpg.github.io/day02-haesolwebsite/ 로 보여 줍니다. `main`에 푸시하면 1~2분 뒤에 다시 배포됩니다.
- **클라우드 환경에서 화면 점검:** Playwright와 Chromium(`executablePath: '/opt/pw-browsers/chromium'`)을 씁니다.
  - 1280px과 390px 너비로 화면을 캡처합니다.
  - `naturalWidth === 0`인 `<img>`가 없는지 확인합니다. 0이면 사진이 안 뜬 것입니다.
  - `document.documentElement.scrollWidth`가 화면 너비보다 크지 않은지 확인합니다. 크면 화면이 옆으로 넘친 것입니다.

## 구조

- **페이지:** 최상위 폴더에 HTML 파일 10개가 있습니다. `index`, `about`, `spots`, `food`, `festival`, `course`, `stay`, `gallery`, `map`, `faq`입니다. 각 파일은 혼자서도 완전한 문서이고, PRD에 따라 CSS와 JS를 파일 안에 넣었습니다.
- **공통 부분은 페이지마다 복사되어 있습니다.** 10개 파일이 `<style>` 블록, `<header>` 메뉴, `<footer>` 연락처, 맨 끝 `<script>`를 똑같이 하나씩 가지고 있습니다. 그래서 스타일, 메뉴, 연락처, 동작을 고칠 때는 10개 파일을 모두 고쳐야 합니다. 페이지마다 다른 부분은 네 가지뿐입니다.
  - `<title>`
  - 메타 설명(description)
  - `class="active" aria-current="page"`가 붙는 메뉴 링크
  - `<main>` 안의 내용
- **새 페이지 만들기:** 처음에는 `index.html`을 틀로 써서 페이지를 만들었지만, 그 생성 스크립트는 저장소에 올리지 않았습니다. 새 페이지가 필요하면 기존 페이지 하나를 복사한 뒤 위의 네 가지를 바꿉니다.
- **JavaScript 없이 동작:** 사용자의 아이패드나 Claude 앱 미리보기에서는 스크립트가 막힐 수 있어서, 화면 동작은 되도록 JavaScript 없이 만들었습니다.
  - **휴대폰 메뉴:** CSS 체크박스 방식입니다(`#menu-toggle` + `label.menu-btn`, `.menu-toggle:checked ~ .menu`).
  - **자주 묻는 질문:** `<details class="faq-item">`과 `<summary class="faq-q">`를 씁니다.
  - **큰 사진 확대:** `.hero`에 `tabindex="0"`이 있습니다. 마우스를 올리면(`:hover`) CSS 애니메이션 `hero-pop`이, 누르면(`:focus`) `hero-pop-click`이 실행됩니다. 끝에 있는 짧은 스크립트는 큰 사진을 다시 눌렀을 때 애니메이션을 처음부터 다시 실행하는 역할만 합니다.
- **화면 구성:**
  - 큰 사진이 있는 페이지는 `<section class="hero">` 안에 `img.hero-img`와 `.hero-text`를 넣습니다.
  - 큰 사진이 없는 페이지는 `<section class="page-head">`(노을빛 제목 띠)를 씁니다.
  - 내용은 `.grid`와 `article.card`(`img.card-img`, `.tag`, `.price`), `.two`, `.box`, `table.info`, `.timeline`, `.gallery`로 배치합니다.
- **색상:** 색은 `:root`의 CSS 변수로 정해져 있습니다. 바다색은 `--sea-deep`, `--sea`, `--sea-light`, 노을색은 `--sunset`, `--sunset-light`, `--dusk`입니다. PRD가 정한 "파란 바다 + 노을빛" 색감을 지킵니다.

## 사진

- **위치와 형식:** 사진은 `images/` 폴더에 JPG로 저장합니다. 너비는 1200px 이하(큰 사진 `hero.jpg`만 1672px), 품질은 82 안팎입니다. 파일 이름은 `<페이지>-<내용>.jpg` 형식입니다.
- **변환:** 사용자가 PNG를 올리면 Pillow로 크기를 줄이고 JPG로 바꿔서 씁니다.
- **`<img>` 규칙:** 모든 `<img>`에는 실제 `width`/`height` 값과 한국어 `alt` 설명을 넣습니다. 화면 아래쪽 사진에는 `loading="lazy"`를 붙입니다.
- **카드 사진:** `object-fit: cover`로 4:3 비율에 맞춰 잘립니다. 주인공이 가운데에서 벗어난 사진은 `object-position`을 직접 지정합니다. 예를 들어 `spots-lighthouse.jpg`가 그렇습니다.
- **약도:** `map.html`의 약도는 사진이 아니라 일부러 SVG 그림으로 둔 것입니다.

카드와 페이지의 글은 사진 내용과 맞아야 합니다. 그래서 명소, 음식, 숙소 이름 일부를 사진에 맞게 바꿨습니다. 이름을 바꿀 때는 그 이름이 나오는 곳을 모두 함께 고칩니다. 특히 `course.html`, `faq.html`, `index.html`의 카드를 확인합니다.

## 작업 방식

- 작업은 `main` 브랜치에 바로 커밋하고 푸시합니다. GitHub Pages가 `main`에서 사이트를 배포합니다.
- 사용자는 아이패드로 작업합니다. 바뀐 결과는 사파리에서 GitHub Pages 주소로 여는 것이 가장 확실하고, 파일을 직접 여는 것은 잘 되지 않습니다.
