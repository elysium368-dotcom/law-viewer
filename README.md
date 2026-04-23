# ⚖ 기준일 법령 조문 조회

**국가법령정보센터 Open API**를 연동하여, 특정 기준일자에 적용되는 법령 조문과 부칙을 조회하는 정적 웹사이트입니다.

👉 **[데모 페이지 바로가기](https://your-username.github.io/law-viewer/)**

---

## 주요 기능

- **기준일자** 입력 → 해당 날짜에 시행 중이던 법령 버전을 자동 탐색
- **법령명 자동완성** — 입력 중 국가법령정보센터에서 실시간 목록을 가져옴
- **연혁법령 검색** — 기준일 이하의 가장 최근 시행 버전을 자동 선택
- **조문 필터링** — 특정 조문번호 입력 시 해당 조문만 표시, 비워두면 전체 표시
- **부칙 분석** — 기준일 이전 시행된 부칙·경과규정만 필터링하여 함께 표시
- **국가법령정보센터 바로가기** 링크 제공

---

## 사용 방법

### 1. API 인증키 발급

이 사이트는 법제처 **국가법령정보 공동활용 Open API**를 사용합니다.

1. [open.law.go.kr](https://open.law.go.kr/LSO/main.do) 접속
2. 회원가입 및 로그인
3. 마이페이지 → **인증키(OC) 발급**
4. 발급받은 인증키를 사이트의 "인증키(OC)" 입력란에 붙여넣기

> 인증키는 브라우저 localStorage에만 저장되며 외부로 전송되지 않습니다.

### 2. 조회

| 입력 항목 | 설명 |
|---|---|
| 기준일자 | 법령이 적용된 날짜 (YYYY-MM-DD) |
| 법령명 | 예: `소득세법`, `근로기준법`, `민법` |
| 특정 조문 번호 | 예: `제14조`, `제14조의2` (비워두면 전체 조문) |

---

## GitHub Pages 배포

```bash
# 1. 이 저장소를 fork 하거나 클론
git clone https://github.com/your-username/law-viewer.git
cd law-viewer

# 2. GitHub Pages 활성화
# GitHub 저장소 → Settings → Pages → Source: Deploy from branch → main / root

# 3. 배포 완료
# https://your-username.github.io/law-viewer/
```

별도의 서버나 빌드 과정 없이 `index.html` 단일 파일로 동작합니다.

---

## 기술 스택

- **Vanilla HTML/CSS/JS** — 단일 파일, 빌드 도구 불필요
- **국가법령정보 Open API** — `www.law.go.kr/DRF/`
- **CORS Proxy** — `corsproxy.io` (브라우저에서 직접 law.go.kr 호출 시 필요)

---

## API 활용 흐름

```
1. lawSearch.do  → 법령명으로 연혁 목록 조회
                   기준일 이하 최신 lsiSeq 선택
2. lawService.do → lsiSeq로 법령 본문 XML 조회
3. XML 파싱      → 조문(조문단위) + 부칙 추출
4. 기준일 필터   → 부칙의 시행일자 ≤ 기준일인 것만 표시
```

---

## 주의사항

- Open API 인증키는 법제처에서 발급받아야 합니다 (무료).
- CORS 제한으로 인해 `corsproxy.io` 프록시를 거쳐 API를 호출합니다.
- 부칙 내 개별 조문 경과규정은 법적 전문 검토가 별도로 필요할 수 있습니다.

---

## 라이선스

MIT License
