# GitHub Commit Email Notifier

GitHub 리포지토리의 커밋을 자동으로 이메일로 알려주는 n8n 워크플로우입니다.

## 주요 기능

- GitHub 리포지토리에 새 커밋이 푸시되면 자동으로 이메일 전송
- main 브랜치 커밋만 필터링 (다른 브랜치로 변경 가능)
- 커밋 정보 포함:
  - 커밋 메시지
  - 작성자 정보
  - 변경된 파일 목록 (추가/수정/삭제)
  - 커밋 링크
  - 타임스탬프

## 설정 방법

### 1. n8n에 워크플로우 Import

1. n8n 에디터 열기
2. 우측 상단 `…` > `Import from File` 클릭
3. `github-commit-email.json` 파일 선택
4. Import 완료

### 2. SMTP 이메일 설정

1. **Send Email** 노드 더블클릭
2. Credential 설정:
   - Gmail 사용 시:
     - SMTP Server: `smtp.gmail.com`
     - Port: `587` (TLS) 또는 `465` (SSL)
     - User: 본인 Gmail 주소
     - Password: [Gmail 앱 비밀번호](https://myaccount.google.com/apppasswords) 생성 후 입력
   - 다른 메일 서비스 사용 시 해당 SMTP 설정 입력

3. 이메일 주소 설정:
   - **From Email**: 발신자 이메일 주소
   - **To Email**: 수신자 이메일 주소 (쉼표로 구분하여 여러 명 가능)

### 3. GitHub Webhook 설정

1. n8n에서 **GitHub Webhook** 노드 클릭
2. **Production URL** 복사 (예: `https://your-n8n.com/webhook/github-webhook`)

3. GitHub 리포지토리 설정:
   - 리포지토리 페이지에서 `Settings` > `Webhooks` > `Add webhook` 클릭
   - **Payload URL**: 복사한 n8n webhook URL 붙여넣기
   - **Content type**: `application/json` 선택
   - **Which events would you like to trigger this webhook?**:
     - `Just the push event` 선택
   - **Active** 체크
   - `Add webhook` 클릭

### 4. 워크플로우 활성화

1. n8n 에디터 상단의 **Inactive** 토글을 **Active**로 변경
2. 설정 완료!

## 커스터마이징

### 다른 브랜치 추적하기

**Filter Main Branch** 노드에서 브랜치 조건 변경:
```
refs/heads/main → refs/heads/develop
```

### 모든 브랜치 추적하기

**Filter Main Branch** 노드 삭제 또는 비활성화

### 이메일 포맷 변경하기

**Format Email Content** 노드의 JavaScript 코드에서 `emailBody` 부분 수정

### 특정 파일 변경만 알림받기

**Filter Main Branch** 노드 뒤에 추가 필터 노드를 만들어 파일 경로 조건 추가:
```javascript
{{ $json.commits.some(c => c.modified.some(f => f.includes('.json'))) }}
```

## 워크플로우 구조

```
GitHub Webhook
    ↓
Filter Main Branch (main 브랜치만 필터링)
    ↓
Format Email Content (이메일 내용 생성)
    ↓
Send Email (SMTP로 이메일 전송)
    ↓
Respond to Webhook (GitHub에 응답)
```

## 테스트 방법

1. 워크플로우 활성화 후 GitHub 리포지토리에 테스트 커밋 푸시
2. n8n의 **Executions** 탭에서 실행 기록 확인
3. 설정한 이메일 주소로 알림 메일 수신 확인

## 문제 해결

### Webhook이 트리거되지 않을 때
- n8n의 webhook URL이 공개적으로 접근 가능한지 확인
- GitHub Webhook 설정에서 Recent Deliveries 확인
- n8n 워크플로우가 Active 상태인지 확인

### 이메일이 전송되지 않을 때
- SMTP 자격증명 확인
- Gmail 사용 시 "보안 수준이 낮은 앱 허용" 또는 앱 비밀번호 사용
- n8n Executions 탭에서 에러 메시지 확인

### 일부 커밋만 알림을 받고 싶을 때
- Filter 노드 추가하여 커밋 메시지, 작성자, 파일 경로 등으로 필터링

## 참고 사항

- GitHub Webhook은 푸시 이벤트마다 즉시 트리거됩니다
- 한 번의 푸시에 여러 커밋이 포함될 수 있습니다
- 이메일은 푸시당 1회 전송됩니다 (커밋별로 개별 전송 아님)
