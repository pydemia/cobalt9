# Cobalt9 구현 현황

기준: 2026-09-28 로컬 파일. 실행 화면이나 마켓플레이스의 현재 배포 상태는
확인하지 않았다. 이 문서는 개편 전 파일 구조와 색상 지정 방식을 기록한다.

## 저장소와 실제 진입점

상위 `cobalt9/`는 추적 파일이 없는 별도 Git 저장소이며, 아래 세 디렉터리도
각각 독립된 Git 저장소다. 현재 작업 트리에는 세 디렉터리가 상위 저장소의
미추적 항목으로 보인다. 따라서 한 저장소의 변경이 다른 구현체에 자동으로
반영되는 구조는 아니다.

| 저장소 | 실제 테마 파일과 사용 경로 | 함께 있는 자료 |
| --- | --- | --- |
| `cobalt9-vscode` | `package.json`이 `themes/Cobalt9-color-theme.json` 하나를 `Cobalt9`/`vs-dark`로 등록 | `themes/monokai.json`, `theme.xml`, CSS 메모, 예제 코드·이미지, `release/*.vsix` |
| `cobalt9-jetbrains` | `resources/META-INF/plugin.xml`의 `themeProvider` → `resources/Cobalt9.theme.json`의 `editorScheme` → `resources/Cobalt9.xml` | `out/production/`, `exported/`, 예제 코드·이미지 |
| `pydemia-theme` | Vim 색 구성, iTerm 프로필, Windows Terminal 설정, Bash·Zsh 프롬프트가 각각 독립적으로 존재 | 설치 스크립트, 셸 설정, `.dircolors`, 외부 `zsh-syntax-highlighting` 복사본, 기타 개발 환경 설정 |

VS Code의 `themes/monokai.json`과 `theme.xml`은 확장에 등록되지 않았다.
`theme.xml`에는 `#272822` 등 별도의 색상이 들어 있다.
`create_tmTheme.py`는 빈 사전만 선언한 미완성 파일이며, `package.json`에는
생성·검증 스크립트가 없다. 세 저장소를 아우르는 공통 팔레트 정의나
자동 변환 경로도 확인되지 않았다.

## 구현체별 구성

### VS Code

`themes/Cobalt9-color-theme.json`은 `colors` 267개와 `tokenColors` 규칙
81개로 구성된다. `semanticTokenColors`와 `semanticHighlighting` 지정은 없다.
Workbench UI와 편집기, 터미널 ANSI 16색을 한 파일에서 설정한다. 일반
TextMate scope 외에 Python, Java, Kotlin 전용 규칙이 있다. 기본 규칙은
주석 `#0088FF`(italic), 문자열 `#3AD900`, 키워드 `#FF9D00`(bold),
함수 이름 `#F92672`(bold), 변수 `#FB94FF`(italic bold)다. Java의
`meta.method.java`와 Kotlin의 annotation scope처럼 중복된 항목도 있고,
`,meta.method.java`처럼 앞에 쉼표가 붙은 scope 문자열도 있다.

UI 배경 계열에는 `#072539`(편집기·activity bar), `#19364B`(side bar)가
쓰이지만, 팝업과 입력창에는 `#252526`, `#3C3C3C` 등 다른 계열의 회색도
남아 있다. 확장 버전은 `package.json` 기준 1.4.2이고, 보관된 VSIX는
1.3.0~1.4.0이다. 로컬 최근 커밋은 2025-12-17의 `v1.4.2`다.
`package.json`의 `repository.url`은 실제 `origin` 주소와 다르다.

### JetBrains

`resources/Cobalt9.theme.json`은 dark UI 테마이며 `basicBackground`
`#072539`, `secondaryBackground` `#19364B`, `contrast` `#FF9D00` 등
별칭을 UI 항목에 연결한다. 일부 아이콘 색상은 별칭 대신 직접 지정한다.
`resources/Cobalt9.xml`은 `Darcula`를 부모로 하는 편집기 색상표로,
`colors` 옵션 26개와 `attributes` 옵션 372개가 있다. 공통 속성과
Python·Java·Kotlin·Markdown·XML·JSON·YAML 등의 개별 속성이 섞여 있다.
XML에는 생성·수정 시각 `2020-07-03`, IDE 버전 `2020.1.2`가 기록돼 있다.
`resources/META-INF/plugin.xml`의 플러그인 버전은 1.1.6이고 로컬 최근
커밋은 2020-07-12다.

`out/production/cobalt9/Cobalt9.theme.json`은 원본과 같지만, 같은 위치의
`Cobalt9.xml`에는 원본의 Markdown 속성 6개가 빠져 있다. 별도의
`exported/Cobalt9.jar`에는 UI 테마가 아닌 `colors/Cobalt9.xml`과
`META-INF/plugin.xml`만 들어 있다. 이 JAR의 플러그인 ID/버전은
`color.scheme.Cobalt9`/0.1이며 `resources`의 UI 테마 플러그인
`org.pydemia.theme.cobalt9`/1.1.6과 다르다. `exported/colors/Cobalt9.xml`의
색상·속성 옵션 값은 `resources/Cobalt9.xml`과 같지만 메타정보와 형식은
다르다. 어느 산출물을 실제로 배포했는지는 로컬 파일만으로 확정할 수 없다.

### `pydemia-theme`

- `vim/.vim/colors/cobalt2.vim`: 원래 이름과 파일 내 `colors_name`이
  `cobalt2`다. `g:cobalt_bg` 기본값은 `#193549`이며 `g:green`,
  `g:dark_blue` 같은 Vim 변수와 `s:X()` 호출로 GUI/터미널 색을 지정한다.
  기본 그룹 외에도 언어별 그룹을 길게 정의한다.
- `vim/.vim/colors/cobalt2_neovim.vim`: 별도의 직접 `hi` 선언 방식이다.
  `Normal` 배경 `#193549`, `String` `#35D900` 등으로 위 파일과도
  완전히 같지 않다. `.vimrc.after.nvim`은 `colorscheme cobalt2`를
  지정하므로 파일명 `cobalt2_neovim.vim`을 직접 선택하지 않는다. 주 설치
  스크립트도 이 파일을 설치하지 않는다.
- `Cobalt9.json`과 `pydemia-iterm2.json`: iTerm 프로필이다. 표시 이름은
  각각 `Cobalt9`, `Default`이지만 배경·전경·선택·ANSI 16색은 8비트 RGB로
  환산하면 같다. ANSI 9·11의 원본 부동소수점 값에는 미세한 차이가 있다.
  색상 외에도 글꼴, 단축키, 작업 디렉터리 등의 개인 프로필 설정이 있다.
- `pydemia-windows-terminal-settings.json`: 전체 Windows Terminal 설정이며
  기본 `colorScheme`은 `Cobalt2-pydemia`다. `schemes`에는 이 색상표와
  다른 기본·예시 색상표가 함께 있다. 기본 프로필, 글꼴, 키 바인딩 등도
  포함한다.
- Bash·Zsh 프롬프트는 `cobalt2-pydemia`라는 이름을 쓴다. 프롬프트
  구간에 `red`, `green`, `blue`, `yellow`, `cyan` 같은 터미널 색상 이름이나
  ANSI 코드를 사용하므로 최종 색은 터미널 팔레트에 좌우된다. Zsh의
  `.dircolors`는 파일 종류별 `ls` 색 설정이며 Solarized용으로 작성된
  자료다. `color.py`는 일반 ANSI 이스케이프 코드 열거형으로,
  Cobalt9의 hex 팔레트를 정의하지 않는다.
- `colormap/listed.md`는 우선순위·범주 색상의 여러 팔레트를 비교한
  표와 이미지다. `cobalt9` 행이 있지만 위 테마 파일을 생성하거나
  설치 스크립트에서 읽는 연결은 없다.

`install_themes.sh`는 원격 `pydemia-theme`를 `~/.pydemia-theme`에 새로
clone한 뒤 Vim `cobalt2.vim`과 `.vimrc`, Bash/Zsh 프롬프트를 설치한다.
iTerm·Windows Terminal 프로필은 이 경로에 포함되지 않는다. 설치 중
Vim 구성은 로컬 복사본이 아닌 원격 `master` 파일을 내려받는다.
`bootstrap/`에는 별도의 오래된 설치 절차가 있다. 이 스크립트들은 홈
디렉터리의 기존 설정 및 디렉터리를 교체하거나 삭제하므로 이번 조사에서는
실행하지 않았다.

## 현재 색상 대응

아래 값은 각 파일의 대표 규칙이다. JetBrains XML의 앞자리 0을 생략한
hex 값은 6자리로 맞춰 적었고, iTerm의 0~1 RGB 성분은 8비트로 반올림했다.
Vim의 `Statement`·`Constant`와 다른 편집기의 scope는 완전히 같은
토큰 분류가 아니다.

| 용도 | VS Code | JetBrains | Vim `cobalt2.vim` | Neovim 파일 |
| --- | --- | --- | --- | --- |
| 편집기 배경 | `#072539` | `#072539` | `#193549` | `#193549` |
| 기본 글자 | `#FFFFFF` | `#FFFFFF` | `#FFFFFF` | `#FFFFFF` |
| 주석 | `#0088FF` | `#0088FF` | `#0088FF` | `#0088FF` |
| 문자열 | `#3AD900` | `#3AD900` | `#3AD900` | `#35D900` |
| 키워드/구문 | `#FF9D00` | `#FF9D00` | `#FF9A00` (`Statement`) | `#FF9D00` (`Keyword`) |
| 숫자/상수 | `#FB71A3` (`constant.numeric`) | `#FF628C` (`DEFAULT_NUMBER`) | `#FF628C` (`Constant`) | `#FF628C` (`Number`) |
| 함수 | `#F92672` | `#F92672` (`DEFAULT_FUNCTION_DECLARATION`) | `#FFC600` | `#FFC600` |

| 터미널 색 | VS Code 내장 터미널 | iTerm 두 프로필 | Windows Terminal `Cobalt2-pydemia` |
| --- | --- | --- | --- |
| 배경 / 전경 | `#072539` / `#CCCCCC` | `#132738` / `#FFFFFF` | `#072539` / `#CCCCCC` |
| ANSI red | `#FF2600` | `#FF0000` | `#F92672` |
| ANSI green | `#3DDF2B` | `#38DE21` | `#82B414` |
| ANSI blue | `#1478DB` | `#1460D2` | `#268BD2` |

VS Code의 `terminal.foreground`는 `#CCCCCC`로 지정돼 있다.
셸 프롬프트는 ANSI 색상 이름을 사용하므로 같은
프롬프트라도 이 세 터미널에서 다른 색으로 렌더링될 수 있다.

현재 색상 수정 지점은 VS Code JSON, JetBrains UI JSON과 편집기 XML,
Vim 두 파일, iTerm 두 프로필, Windows Terminal scheme, 셸 프롬프트로
분산돼 있다. 화면 대비나 색 조화에 대한 판단은 실제 렌더링과 접근성
검사를 아직 하지 않았으므로 이 문서에 포함하지 않았다.
