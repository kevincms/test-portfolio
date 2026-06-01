# 개인 개발자 포트폴리오

[Hugo Blox](https://hugoblox.com/)의 `dev-portfolio` 템플릿을 기반으로 만든 개인 포트폴리오 사이트입니다. 모든 콘텐츠는 Markdown/YAML로 관리하고, 페이지는 미리 정의된 **블록(block)**을 쌓아 구성합니다. 테마는 Go 모듈로 가져오므로 직접 수정하지 않습니다.

- **엔진**: Hugo v0.161.1+ extended (정적 사이트 생성기)
- **빌더**: Hugo Blox (`dev-portfolio` 템플릿, 테마는 `HugoBlox/kit/modules/blox` Go 모듈)
- **배포**: GitHub Actions → GitHub Pages (`main` push 시 자동 빌드·배포)

> ⚠️ 현재 **시범 배포** 단계입니다. `static/robots.txt`로 검색엔진 색인을 차단해 두었고(`Disallow: /`), `enableRobotsTXT: false`로 설정돼 있습니다. 정식 운영 시 `static/robots.txt`를 삭제하고 `config/_default/hugo.yaml`의 `enableRobotsTXT`를 `true`로 되돌립니다.

---

## 🗺️ 사이트 구조

페이지를 만드는 단위는 두 가지입니다.

| | **블록 (Block)** | **콘텐츠 타입 (Content Type)** |
|---|---|---|
| 정체 | 한 페이지에 쌓는 **섹션 부품** | 폴더가 통째로 갖는 **페이지 레이아웃** |
| 선언 | `_index.md`의 `sections:` 배열 | 폴더명 (`content/<타입>/`) |
| URL | 같은 페이지 내 앵커 (`/#projects`) | 독립 페이지 (`/projects/`, `/blog/`, `/docs/`) |

### 홈페이지 블록 구성 (`content/_index.md` · `landing` 타입)

홈 한 장은 아래 블록을 순서대로 쌓아 만듭니다. 메뉴(`menus.yaml`)는 모두 `/#<id>` 앵커로 이 블록들로 스크롤됩니다.

| 순서 | 블록(`block`) | `id` | 역할 |
|---|---|---|---|
| 1 | `dev-hero` | `hero` | 상단 히어로 (이름·캐치프레이즈) |
| 2 | `portfolio` | `projects` | 프로젝트 카드 그리드 (태그 필터) → `/projects/` |
| 3 | `tech-stack` | `skills` | 기술 스택 아이콘 그리드 |
| 4 | `resume-experience` | `experience` | 경력·학력 타임라인 |
| 5 | `resume-awards` | `awards` | 수상 이력 |
| 6 | `resume-awards` | `certifications` | 자격증 |
| 7 | `collection` | `blog` | 블로그 미리보기 (최신 3개) → `/blog/` |
| 8 | `collection` | `docs` | 기술 문서 미리보기 → `/docs/` |
| 9 | `contact-info` | `contact` | 연락처 |
| 10 | `cta-card` | — | 하단 CTA |

### 콘텐츠 타입 (독립 페이지)

| 경로 | 타입 | 레이아웃 | 내용 |
|---|---|---|---|
| `/projects/` | `portfolio` 블록 소스 | 카드 + 상세 | 프로젝트 5건 |
| `/blog/` | 블로그 | 목록 + 상세 | digest 3종 + 회고 글 |
| `/docs/` | `docs` | **좌측 사이드바** | 기술 메모 2건 |

---

## 📁 디렉터리 구조

```
.
├── config/_default/
│   ├── hugo.yaml          # Hugo 엔진 설정 (baseURL·robots 등)
│   ├── params.yaml        # 사이트 전역 설정 (SEO·외관 등)
│   ├── menus.yaml         # 상단 네비게이션 (모두 /#<id> 앵커)
│   ├── languages.yaml     # 언어 설정
│   └── module.yaml        # 테마 모듈 마운트
├── content/
│   ├── _index.md          # 홈페이지 (landing 블록 구성)
│   ├── projects/          # 프로젝트 상세 (auto-driving, ev-charger-ops-platform,
│   │                      #                kmooc-study-automation, portfolio-site, snake)
│   ├── blog/              # 블로그 글 (anthropic/huggingface/openai-digest + 회고)
│   ├── docs/              # 기술 문서 (docker, hugo-blox) — 사이드바 레이아웃
│   ├── awards/            # 수상 (aivle-school, army-commendation, autonomous-driving)
│   └── certifications/    # 자격증 (aice-associate, info-processing, sqld)
├── data/authors/me.yaml   # 본인 프로필 (bio·skills·experience 등)
├── assets/media/          # 아바타·이미지
├── static/
│   ├── uploads/resume.pdf # 이력서
│   └── robots.txt         # 시범 배포용 색인 차단
├── .github/workflows/     # build / deploy / upgrade (GitHub Actions)
├── netlify.toml           # Netlify 배포 설정 (대안)
├── hugoblox.yaml          # 배포 타깃(deploy.host)·템플릿 메타
└── go.mod / go.sum        # 테마 Go 모듈 의존성
```

---

## 🚀 콘텐츠 추가·수정

블록과 테마 레이아웃(`.html`)은 건드리지 않고 **`.md`/`.yaml`만** 수정합니다.

| 하고 싶은 것 | 수정 위치 |
|---|---|
| 새 프로젝트 | `content/projects/<slug>/index.md` 추가 (front matter `tags`로 필터됨) |
| 새 블로그 글 | `content/blog/<slug>/index.md` 추가 |
| 새 기술 문서 | `content/docs/<slug>/index.md` 추가 (`weight`로 사이드바 순서) |
| 프로필·경력 | `data/authors/me.yaml` |
| 홈 블록 순서·옵션 | `content/_index.md`의 `sections:` |
| 네비게이션 | `config/_default/menus.yaml` |

> 새 글/프로젝트(새 폴더)를 추가한 뒤 안 보이면 `hugo server`를 **완전 재시작**하세요 (Fast Render Mode가 새 디렉터리를 watch에 넣지 못함).

---

## 🛠️ 로컬 실행

```bash
# 의존성 설치 (pnpm)
pnpm install

# 개발 서버 (드래프트 포함)
hugo server -D

# 프로덕션 빌드 (결과: public/)
hugo --gc --minify
```

> Hugo Extended 버전이 필요합니다 (`config/_default/hugo.yaml`의 버전 참고). 검색 인덱스는 `pnpm run pagefind`로 생성됩니다.

---

## 🌐 배포

`main` 브랜치에 push하면 `.github/workflows/deploy.yml`이 Hugo 빌드 → GitHub Pages 배포까지 자동 수행합니다. 배포 타깃은 `hugoblox.yaml`의 `deploy.host`로 결정됩니다 (`github-pages` / `netlify` / `vercel` / `cloudflare` / `none`).

- **baseURL**은 빌드 시 워크플로가 자동 주입하므로(`--baseURL`) `hugo.yaml`을 수정할 필요가 없습니다.
- GitHub Pages를 쓰려면 레포 **Settings → Pages → Source: GitHub Actions**로 설정합니다.

---

이 사이트는 [Hugo Blox](https://github.com/HugoBlox/kit)(MIT) 위에 만들어졌습니다.
