# 게임 리뷰 웹 크롤러 (Steam & OpenCritic)

게임 리뷰를 수집하는 두 개의 파이썬 크롤러입니다.
두 크롤러 모두 결과를 동일한 형태의 **JSONL**로 저장합니다.

```json
{"게임 이름": "", "작성자": "", "리뷰 내용": "", "평점": ""}
```

## 구성
```
.
├── Steam_WebCrawler/        # Steam 리뷰 크롤러
│   ├── steam_review_crawler.py
│   └── requirements.txt
└── OpenCritic_WebCrawler/   # OpenCritic 평론 크롤러
    ├── OpenCritic_WebCrawler.py
    └── requirements.txt
```

---

## 1. Steam 리뷰 크롤러

Steam 공개 리뷰 API(`store.steampowered.com/appreviews`)에서 주제(장르)별로
게임 리뷰를 수집합니다.

**특징**
- 주제별로 게임을 찾아 **모든 리뷰**를 끝까지 수집 (cursor 페이지네이션)
- 외국어 리뷰를 **한국어로 번역**해서 저장 (deep-translator)
- 추천/비추천 둘 다 수집 → `평점` = `"추천"` / `"비추천"`
- 여러 주제를 번갈아 수집(라운드 로빈), 중단해도 `progress.json` 기준 **이어받기**
- 리뷰 수집 자체는 API 키 불필요 (작성자 닉네임 변환 시에만 Steam Web API 키 사용)

**설치 & 실행**
```cmd
cd Steam_WebCrawler
pip install -r requirements.txt
python steam_review_crawler.py
```
- 설정: `steam_review_crawler.py` 상단 CONFIG에서 `TOPICS`, `MAX_GAMES`,
  `TRANSLATE_TO_KOREAN`, `STEAM_API_KEY` 등을 수정
- 결과: `output/reviews/{주제}.jsonl`

---

## 2. OpenCritic 평론 크롤러

OpenCritic API(RapidAPI)에서 게임별 평론(리뷰)을 수집합니다.

**특징**
- 전체 게임 목록을 순회하며 게임별 평론 수집
- 점수를 등급으로 변환 → `평점` = `"강력 추천"` / `"추천"` / `"평이함"` / `"정보 없음"`
- `processed_progress.json`으로 **이어받기**, 요청 실패 시 재시도/대기

**API 키 설정 (필수)**

RapidAPI의 OpenCritic API 키가 필요합니다. **키를 코드에 직접 넣지 말고**
환경변수로 설정하세요.
```cmd
cd OpenCritic_WebCrawler
pip install -r requirements.txt
set RAPIDAPI_KEY=발급받은_RapidAPI_키
python OpenCritic_WebCrawler.py
```
- 결과: `reviews_final_data.jsonl`

---

## 참고
- 수집된 데이터(`*.jsonl`), 진행상태(`*progress.json`), 로그, 가상환경(`.conda`)
  등은 용량/보안 문제로 **저장소에 포함하지 않습니다.** (`.gitignore` 참고)
- 크롤링 시 각 서비스의 이용약관과 요청 간격(rate limit)을 지켜 주세요.
- 개인 학습/연구용 프로젝트입니다.
