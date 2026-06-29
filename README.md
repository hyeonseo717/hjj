# 지박령 팀 기획서 기반 국방 응급 알리미 v5

이 프로젝트는 지박령 팀의 **국방 응급 알리미** 기획서를 기준으로 작성한 전체 구현 코드입니다.

## 핵심 구현 내용

- 스마트폰 보고자 화면
- PC 관리자/군의관/지휘관 대시보드
- 부대코드 + 직책 + 비밀번호 기반 역할 로그인
- 로그인 후 보고자 화면 / 관리자 대시보드 자동 분기
- 원터치 체크박스 기반 하이브리드 부상 보고
- 상세 경위 자연어 입력
- T1, T2, T3, T4 현장 간부 판단값 입력
- 규칙 기반 AI 트리아지 자동 분류
- 간부 판단값과 AI 판단값 크로스 체크
- 불일치 시 Human-in-the-Loop 경고 표시
- GPS/OpenStreetMap 미사용
- HTML5/CSS 기반 폐쇄형 가상 지도
- 지도 클릭으로 X/Y 상대좌표 저장
- PC 대시보드에서 실시간 핀 점멸 표시
- T1 → T2 → T3 → T4 위험도순 자동 정렬
- 군의관 피드백 저장
- 부대 관리, 사용자 관리, 시스템 설정, 작업 로그
- SQLite DB 자동 생성
- 모바일 반응형 웹 + PWA 기본 구성
- 서비스 구현에서 어려운 점 반영 화면
- 프로젝트 추가 문의사항 반영 화면
- 군의관 피드백 누적 및 AI 개선 파이프라인 API
- AI 신뢰도/불확실성 필드 저장

## 폴더 구조

```text
jibakryeong-defense-alert-v5-full
├─ backend
│  ├─ main.py
│  └─ requirements.txt
└─ frontend
   ├─ package.json
   ├─ index.html
   ├─ public
   │  ├─ manifest.webmanifest
   │  └─ icon.svg
   └─ src
      ├─ api.js
      ├─ App.jsx
      ├─ main.jsx
      └─ styles.css
```

## 백엔드 실행

```powershell
cd backend
python -m venv venv
.\venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

백엔드 확인:

```text
http://127.0.0.1:8000/docs
```

## 프론트엔드 실행

새 PowerShell 창에서 실행합니다.

```powershell
cd frontend
npm install
npm run dev
```

PC 접속:

```text
http://localhost:5173
```

스마트폰 접속:

```text
http://PC_IP주소:5173
```

PC IP 확인:

```powershell
ipconfig
```

## 테스트 계정

| 역할 | 부대코드 | 직책 | 비밀번호 | 접속 화면 |
|---|---|---|---|---|
| 보고자 | GOP-01 | 소대장 | 1234 | 스마트폰 보고 화면 |
| 보고자 | GP-02 | 소초장 | 1234 | 스마트폰 보고 화면 |
| 보고자 | ISL-03 | 분대장 | 1234 | 스마트폰 보고 화면 |
| 군의관 | HQ-00 | 군의관 | doctor1234 | PC 대시보드 |
| 지휘관 | HQ-00 | 지휘관 | commander1234 | PC 대시보드 |
| 관리자 | HQ-00 | 관리자 | admin1234 | PC 대시보드 |

## 데이터 저장 위치

백엔드 실행 시 아래 SQLite DB 파일이 자동 생성됩니다.

```text
backend/jibakryeong_defense_alert_v5.db
```

스마트폰에서 등록한 부상 보고, AI 트리아지 결과, 지도 X/Y 좌표, 군의관 피드백, 작업 로그가 이 DB 파일에 저장됩니다.

## AI 트리아지 안내

본 코드는 학생 발표와 로컬 실행을 위해 외부 유료 LLM API 없이 작동하는 **규칙 기반 AI 모의 엔진**을 사용합니다.
실제 LLM API를 붙이고 싶다면 `backend/main.py`의 `classify_triage()` 함수를 LLM 호출 코드로 교체하면 됩니다. v5에서는 기획서 부록의 어려운 점과 추가 문의사항을 반영하기 위해 `/api/project/implementation-issues`, `/api/project/mentoring-questions`, `/api/project/model-pipeline`, `/api/project/compliance` API를 추가했습니다.

## v5.1 수정 사항
- 스마트폰 보고 화면에서 입력한 데이터가 `/api/reports`로 정상 저장되도록 SQL INSERT 구문을 수정했습니다.
- 같은 초에 여러 건을 등록해도 보고번호가 중복되지 않도록 마이크로초 단위 보고번호를 적용했습니다.
- 저장된 보고는 PC 관리자 대시보드의 통계 카드, 가상 지도 핀, 부상 보고 목록에 표시됩니다.
