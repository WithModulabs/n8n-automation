# 수신 메일 정리 에이전트 (Mail-to-Sheet Agent)

이 에이전트는 Kakao로 수신된 메일을 파싱하여 **구글 시트에 자동으로 정리**하는 역할을 합니다.  

---

## ✨ 주요 기능

- **메일 본문 파싱**

  - 본문에서 `어제 한 일 (Did)`, `오늘 할 일 (To-Do)`, `문제 (Problem)` 항목 자동 추출
  - `:`, `-`, 줄바꿈 등 다양한 입력 포맷을 지원

- **보낸 사람 정보 분리**

  - `from` 값을 이름과 이메일로 분리  

  - 예:  

    ```
    신영태 <youngtae0818@naver.com>
    → name: 신영태, email: youngtae0818@naver.com
    ```

- **날짜 표준화**

  - 메일 원본 날짜(`Mon, 29 Sep 2025 ...`)를 ISO 형식(`2025-09-29`)으로 변환  
  - n8n의 `$today`와 쉽게 비교 가능

- **구글 시트 연동**

  - 파싱된 업무 내용을 `주간업무보고 시트`에 기록
  - 신규 인원은 자동으로 `마스터 명단 시트`에 추가하거나 기존 인원 정보 업데이트

---

## 🛠 워크플로우 흐름

1. **이메일 수신 (Email Trigger)**  
   - IMAP 기반 Email Trigger (Kakao) 또는 Gmail Trigger 사용  
2. **업무 내용 파싱 (Parse Work Report Content)**  
   - 본문에서 Did / To-Do / Problem 추출  
3. **보낸 사람 정보 추출 (Extract Name and Email)**  
   - 이름/이메일로 분리  
4. **주간업무보고 시트 기록 (Append Weekly Report)**  
   - 추출된 데이터를 구글 시트에 행으로 추가  
5. **마스터 명단 업데이트 (Update Master Roster)**  
   - 신규 인원은 자동 추가, 기존 인원은 이메일 갱신

---

## 📊 구글 시트 구조

### 주간업무보고 시트

| name   | email                  | date       | did                      | todo                   | problem                                     |
| ------ | ---------------------- | ---------- | ------------------------ | ---------------------- | ------------------------------------------- |
| 신영태 | youngtae0818@naver.com | 2025-09-29 | n8n 사용법 데이터 다루기 | n8n 이메일 자동화 학습 | n8n email trigger와 gmail trigger 차이 모름 |

### 마스터 명단 시트

| name   | email                  |
| ------ | ---------------------- |
| 김설화 | seolhwa@example.com    |
| 신영태 | youngtae0818@naver.com |
| 김영광 | glory@example.com      |

---

## 🚀 사용 방법

1. 제공된 JSON 파일을 n8n에서 `Workflow → Import from File`로 불러오기  
2. 아래 크리덴셜을 설정  
   - **Email Trigger**: Kakao(IMAP) 또는 Gmail(OAuth2)  
   - **Google Sheets**: OAuth2 인증  
3. Google Sheets 노드에서 **사용할 시트 이름/ID** 업데이트  
4. 워크플로우 실행 → 수신 메일이 자동으로 파싱되어 시트에 기록됨