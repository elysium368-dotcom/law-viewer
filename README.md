# ⚖ 기준일 법령 조문 조회

**국가법령정보센터 Open API**를 연동하여, 특정 기준일자에 적용되는 법령 조문과 부칙을 조회하는 웹사이트입니다.

---

## 주요 기능

- 기준일자 입력 → 해당 날짜에 시행 중이던 법령 버전 자동 탐색
- 법령명 자동완성 (↑↓ Enter 키보드 지원)
- 조문번호 자유 입력: `제1조` · `1,3,5` · `1~10` · `제14조의2` · 혼합
- 부칙 자동 분석 (기준일 이전 공포된 부칙만)
- 국가법령정보센터 원문 링크

---

## 🚨 CORS 프록시 문제 해결 — 개인 Cloudflare Worker 구축 (5분, 완전 무료)

공용 CORS 프록시(`corsproxy.io`, `allorigins.win` 등)는 자주 다운되거나 law.go.kr 접근이 차단됩니다.
**개인 Cloudflare Worker를 만들면 이 문제가 완전히 해결됩니다.** 무료 계정에서 하루 10만 요청까지 지원되며, 개인 용도로는 충분합니다.

### 단계 1: Cloudflare 가입

1. [dash.cloudflare.com](https://dash.cloudflare.com/sign-up) 접속
2. 이메일 · 비밀번호로 회원가입 (신용카드 불필요)

### 단계 2: Worker 생성

1. 로그인 후 좌측 메뉴 **"Workers & Pages"** 클릭
2. **"Create"** → **"Create Worker"** 클릭
3. 이름 입력 (예: `law-proxy`) → **"Deploy"** 클릭
4. 배포 후 **"Edit code"** 클릭

### 단계 3: 코드 붙여넣기

기본 코드를 모두 지우고 아래를 붙여넣으세요:

```javascript
export default {
  async fetch(request) {
    const url = new URL(request.url);
    const target = url.searchParams.get('url');
    if (!target) return new Response('missing url param', { status: 400 });

    const decoded = decodeURIComponent(target);
    if (!decoded.startsWith('https://www.law.go.kr/')) {
      return new Response('only law.go.kr allowed', { status: 403 });
    }

    const res = await fetch(decoded, {
      headers: { 'User-Agent': 'Mozilla/5.0' }
    });
    const text = await res.text();

    return new Response(text, {
      headers: {
        'Content-Type': 'text/xml; charset=utf-8',
        'Access-Control-Allow-Origin': '*',
        'Cache-Control': 'public, max-age=300',
      },
    });
  },
};
```

**"Deploy"** 버튼 클릭. 잠시 후 상단에 Worker 주소가 표시됩니다.
예: `https://law-proxy.your-username.workers.dev`

### 단계 4: 사이트에 연결

1. 사이트 접속
2. 상단 **"API 인증키 설정됨 · ▾ 변경"** 클릭
3. **"커스텀 프록시 URL"** 칸에 발급받은 주소 뒤에 `/?url=` 을 붙여 입력:

   ```
   https://law-proxy.your-username.workers.dev/?url=
   ```

4. 입력만 하면 자동 저장 및 적용됩니다.

이제 공용 프록시 상태와 무관하게 항상 빠르고 안정적으로 동작합니다.

---

## 일반 사용법

### 1. API 인증키 발급

1. [open.law.go.kr](https://open.law.go.kr/LSO/main.do) 회원가입
2. 마이페이지 → 인증키(OC) 발급 (무료)
3. 사이트의 **"API 인증키 설정 → 변경"** 에서 입력

> 키는 브라우저 localStorage에만 저장됩니다.

### 2. 법령 조회

| 필드 | 입력 예 |
|---|---|
| 기준일자 | 2023-01-01 |
| 법령명 | 소득세법 (2자 이상 입력 → 자동완성) |
| 조문 번호 | (선택) `제1조` / `1,3,5` / `1~10` / `제14조의2` |

**키보드 단축키**: 법령명 칸에서
- `↑` `↓` 자동완성 탐색
- `Enter` 첫번째 또는 하이라이트된 법령 자동 선택
- `Esc` 드롭다운 닫기

---

## GitHub Pages 배포

1. 이 저장소를 fork 또는 clone
2. Settings → Pages → Source: **Deploy from a branch** → `main` / `/ (root)` → Save
3. 1~2분 후 `https://[사용자명].github.io/law-viewer/` 로 접속

---

## 파일 구조

```
law-viewer/
├── index.html   # 단일 파일 (HTML + CSS + JS 통합)
└── README.md
```

외부 빌드 도구 불필요. `index.html` 하나만 배포하면 동작합니다.

---

## 라이선스

MIT
