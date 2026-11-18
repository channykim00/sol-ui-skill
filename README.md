# Sol UI — Claude Code design skill

차분하고 단순한 리스트 중심의 디자인 시스템을 [Claude Code](https://claude.com/claude-code) 스킬로 만든 것입니다.
Claude에게 "솔 UI로 로그인 화면 만들어줘"라고 하면 이 규칙과 컴포넌트대로 화면을 만들어요.

- **초록 대표 색상 하나 + 무채색 기반**: 흰 글자 대비 4.5:1(WCAG AA)을 통과하는 `#09855F`와 그래픽용 `#12A97B`
- **Pretendard + Lucide 아이콘**: 무료 라이선스 폰트·아이콘만 사용
- **세 가지 레이아웃 모드**: App(모바일 한 열) · Site(랜딩·소개) · Console(대시보드·관리자)
- **라이트 / 다크 모드**: 시맨틱 토큰만 바꿔서 자동 전환
- **한국어 UX 라이팅 규칙**: 해요체, 다음 동작이 보이는 버튼 문구, 다크패턴 금지

## 설치

```bash
git clone https://github.com/channykim00/sol-ui-skill.git
cp -R sol-ui-skill/sol-ui ~/.claude/skills/
```

Claude Code를 다시 시작하면 `sol-ui` 스킬이 목록에 나타나요.
스킬을 계속 수정할 생각이라면 복사 대신 심볼릭 링크를 걸어두면 편해요.

```bash
ln -s "$(pwd)/sol-ui-skill/sol-ui" ~/.claude/skills/sol-ui
```

## 사용법

Claude Code에서 평소처럼 말하면 돼요.

```
솔 UI로 회원가입 화면 만들어줘
sol-ui로 매출 대시보드 페이지 만들어줘   → Console 모드
내 디자인으로 서비스 소개 랜딩 페이지 만들어줘 → Site 모드
```

처음 쓰는 프로젝트에서는 Claude가 `tokens.css`, `components.css`를 복사하고, Pretendard와 `lucide-react`를 설치하고, `src/components/ui/`에 공통 컴포넌트를 만들어요.

## 구성

```
sol-ui/                      ← 스킬 폴더 (~/.claude/skills/ 에 넣는 부분)
├── SKILL.md                 # 언제 쓰는지, 핵심 규칙, 완료 체크리스트
├── assets/
│   ├── tokens.css           # 색상·타이포·간격·모서리·모션·다크 모드 토큰
│   └── components.css       # .sol-* 컴포넌트 클래스 (모바일 + 데스크톱)
└── references/
    ├── components.md        # React + TS 컴포넌트 (Button, ListRow, Dialog, Toast …)
    ├── layout.md            # App / Site / Console 모드, 브레이크포인트, 데스크톱 규칙
    └── writing.md           # 한국어 UX 라이팅 규칙
examples/                    ← 브라우저로 바로 열어보는 데모
├── index.html               # 토큰·컴포넌트 미리보기 + 모바일 데모
├── app.html                 # App 모드: 물 기록 앱
├── console.html             # Console 모드: 대시보드
└── site.html                # Site 모드: 랜딩 페이지
```

## 데모 보기

`examples/index.html`을 브라우저에서 열면 돼요. 데모는 `sol-ui/assets/`의 CSS를 그대로 불러오기 때문에, 스킬을 고치면 데모에도 바로 반영돼요.

## 색상 바꾸기

`sol-ui/assets/tokens.css`의 `--g-50` ~ `--g-900` 값만 바꾸면 전체 테마가 바뀌어요.
버튼 배경으로 쓰는 `--g-600`은 흰 글자와 대비 4.5:1 이상이 되는 색으로 골라주세요.

## 라이선스

[MIT](LICENSE)
