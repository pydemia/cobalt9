# Cobalt9 색상 미세조정

2026-09-29. 원래 Cobalt9의 색상값을 대부분 복원하고, 동일하거나
가까운 색 때문에 구분하기 어려웠던 선언·참조·호출에만 기존 팔레트의
색을 재배치했다. 초기 제안 10개는
[`colormap-candidates.json`](colormap-candidates.json)에 보관한다.

## 기준

VS Code 1.4.2와 JetBrains 1.1.6을 원래 Cobalt9의 기준으로 삼았다.
두 구현체의 색 배분에는 차이가 있어 공통 구문 역할에는 VS Code의
원래 팔레트를 기준으로 삼고, JetBrains의 언어별 기존 값은 가능한
유지했다. 배경 `#072539`, 흰 본문, 파란 주석 `#0088FF`, 초록 문자열
`#3AD900`, 주황 키워드 `#FF9D00`, 분홍 함수 선언 `#F92672`가
Cobalt9의 기본 인상이다.

| 역할 | 색 | 기존 팔레트에서의 출처 |
| --- | --- | --- |
| 배경 | `#072539` | Cobalt9 편집기 배경 |
| 본문 | `#FFFFFF` | Cobalt9 기본 전경 |
| 주석 | `#0088FF` | Cobalt9 주석 |
| 문자열 | `#3AD900` | Cobalt9 문자열 |
| statement·storage | `#FF9D00` | Cobalt9 키워드 |
| 클래스 선언 | `#D7BA7D` | Cobalt9 class name |
| 타입 참조 | `#FFDD00` | Cobalt9 library class |
| 함수 선언 | `#F92672` | Cobalt9 function name |
| 함수·메서드 호출 | `#FFC600` | Cobalt9 Python generic call |
| 변수 | `#FFFFFF` | Cobalt9 기본 전경 |
| 매개변수 | `#FFFFFF` | Cobalt9 기본 전경 |
| 프로퍼티 | `#FFFFFF` | Cobalt9 기본 전경 |
| 숫자 | `#FB71A3` | Cobalt9 VS Code number |
| 내장 상수 | `#FF628C` | Cobalt9 built-in constant |
| 연산자 | `#B267E6` | Cobalt9 debug token 보라 |
| escape·정규식 | `#06A6A8` | Cobalt9 character/regexp |

선언과 사용 위치에 같은 구문 범주가 붙는 경우를 구분하려고 VS Code
semantic token과 TextMate scope를 함께 설정했다. JetBrains에서는
기존 scheme 항목을 복원하면서 공통 함수 호출·클래스 선언·지역 변수
항목에 위 색을 적용했다. 원본 JetBrains XML의 `88ff`처럼 자릿수가
짧았던 색은 `0088FF`처럼 6자리로 표기했다.

Vim과 Neovim은 Cobalt9 배경을 유지하고 위 구문 색에 맞췄다. iTerm의
ANSI 16색은 VS Code 1.4.2의 원래 terminal 색을 사용한다. 따라서
각 프로그램에서 사용할 수 있는 구문 그룹의 범위는 다르지만,
공통 역할에 새 색상 계열을 추가하지 않았다.

## 2026-09-30 조정

변수, 인스턴스 이름, 매개변수, 프로퍼티는 흰색 `#FFFFFF`로 맞췄다.
함수·클래스 선언과 호출·타입 참조의 구분은 유지한다. `*`, `<=`처럼
구문을 연결하는 연산자는 기존 팔레트의 보라색 `#B267E6`로 표시한다.
괄호·쉼표·점·세미콜론은 흰색 계열로 두어 연산자와 구분한다.

## 2026-10-01 조정

Python 함수 선언의 매개변수 이름과 호출의 키워드 인자 이름에는 기존
팔레트의 살구색 `#F4ABA4`를 사용한다. VS Code에서는 Pylance의
`parameter.declaration`, `parameter.keywordArgument` semantic token과
해당 TextMate scope를 지정한다. 일반 변수와 매개변수 참조는 흰색으로
둔다. JetBrains에서는 `PY.PARAMETER`와 `PY.KEYWORD_ARGUMENT`를
지정한다. JetBrains의 `PY.PARAMETER`는 선언과 참조를 별도 색으로
나누지 못할 수 있다. Vim 기본 Python syntax에는 두 이름을 구분하는
그룹이 없어 기존 흰색을 유지한다.

연산자는 `#B267E6`에서 약간 밝힌 `#BA76E9`로 조정했다. 기존
`token.debug-token` 색은 연산자가 아니므로 바꾸지 않았다.

타입 참조는 `#FFDD00`에서 선명한 노랑 `#E5CA48`로, 함수·메서드
호출은 `#FFC600`에서 진한 노랑 `#E2B40D`로 조정했다. 클래스 선언
`#D7BA7D`와는 채도 차이를 두고, statement 주황 `#FF9D00`과도
색조를 분리했다. Vim의 UI 노랑은 변경하지 않고 구문 색만 조정했다.

## 배포 상태 (2026-10-01)

VS Code 1.5.5는 Marketplace에 게시했다. JetBrains 1.2.4는
Marketplace에 업로드했으며 현재 `Under review` 상태다. 최종 색상을
적용한 IntelliJ Java·JSON 화면을 JetBrains 갤러리의 첫 두 장으로
등록했다. JetBrains 등록 화면과 심사 상태는
[`jetbrains-final-gallery.png`](jetbrains-final-gallery.png),
[`jetbrains-1.2.4-under-review.png`](jetbrains-1.2.4-under-review.png)에
기록했다.
