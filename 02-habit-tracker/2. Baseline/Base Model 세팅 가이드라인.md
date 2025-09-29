# Base Model 세팅 가이드라인

# Slack 연동 전처리 과정

1. 새로운 n8n 워크플로우 창을 띄우기
2. Slack_baseline.json 파일을 [우측 상단의 … > Import from File] 탭을 통해 불러오기
3. Slack Trigger를 더블클릭
4. Credential to connect with 부분에 연결할 수 있는 Account 설정이 있다면 다음 과정으로 스킵, 없다면 Create New credential 버튼 클릭
5. [https://api.slack.com/apps/](https://api.slack.com/apps/) 에 접속, 본인 슬랙 계정 로그인
6. Create New App 버튼 클릭
    1. From scratch 클릭
    2. APP 이름 임의 설정(ex. n8n Test)
    3. Pick a workspace에서 해빗트래커 공용 슬랙계정 선택(혹은 개인 슬랙 계정)
    4. Create App 버튼 클릭
7. 다음과 같은 화면에서 좌측 Features 메뉴의 OAuth & Permissions 탭으로 이동

![image.png](./image/image.png)

1. 아래로 내려 Scopes 섹션을 찾고, Bot Token Scopes에 Add an OAuth Scope를 선택
    - app_mentions:read - 멘션된 메시지를 읽을 수 있는 권한(이벤트 기반 동작)
    - channels:history - 봇이 추가되어있는 퍼블릭 채널의 과거 메시지 읽기 권한(멘션 응답 및 맥락 파악)
    - chat:write - 채널 또는 DM에 메시지 전송 권한
    - commands - 슬래시 명령어 추가 권한
    - files:write - 파일 업로드, 수정, 삭제 권한
    - users:read - 워크 스페이스 사용자 프로필 정보 조회 권한
2. Redirect URLs에 Add New Redirect URL을 클릭 후, 다음을 입력합니다. (1차적으로 로컬 개발에 대한 URL을 첨부합니다. 향후 클라우드 기반으로 확장 시 다른 URL을 사용할 예정입니다)
    
    [`https://localhost:5678/rest/oauth2-credential/callback`](https://localhost:5678/rest/oauth2-credential/callback)
    
3. 상단으로 올라와 OAuth Tokens 를  찾고(위에서 두번째) Install to Workspace 버튼 >팝업에서 Allow를 클릭합니다.
4. 토큰이 생기면, 이를 복사하고, n8n slack trigger로 다시 돌아와 Access Token 란에 붙여넣기 합니다.

![image.png](./image/image%201.png)

![image.png](./image/image%202.png)

# Slack 이후 상세 전처리

## Edit Fields

- bot_id에 쓰여져 있는 ID를 본인 ID로 변경합니다.
- slack api 화면의 좌측 Settings 메뉴의 Basic Information 내 App ID를 활용하거나, Your Apps 페이지의 App ID를 복붙해주세요

## App Home

- 슬랙에서 사용할 봇의 이름을 설정합니다.
- 아래의 메뉴에서 원하는 봇 이름과 아이디를 선택합니다.

![image.png](./image/image%203.png)

## Active

- 상단의 Inactive를 Active로 변경합니다

![image.png](./image/image%204.png)

## 봇 이벤트 설정

- 반드시 active 처리를 한 뒤 진행해주세요
- Slack Trigger에서 위에 접혀진 Webhook URLs를 펼쳐줍니다
- Test URL이 아닌 Production URL를 선택해주시고, 해당 POST 주소를 복사합니다
- Features > Event Subscriptions를 on 하고, Request URL에 복사한 값을 붙여넣습니다. (verified가 잘 뜨지 않는다면 말씀해주세요.. 웹훅으로 우회하는 방식으로 진행해야 합니다)

![image.png](./image/image%205.png)

## Gemini 설정

- send a message1 노드는 gemini에 최적화되어 있습니다.
- 크레딧 가지고 계신 모델로 연결해주세요

## 챗봇 채널 설정

- 슬랙 내에서 채널에 해당 봇을 초대해야 기능을 사용할 수 있습니다.
- 아래 예시 이미지는 notion 채널에 봇 계정을 초대하는 과정입니다(App Home에서 설정했던 봇 이름으로 불러오시면 됩니다)

![image.png](./image/image%206.png)

![image.png](./image/image%207.png)

## 메시지 테스트

- 다음과 같이 봇을 호출하면 뉴스레터를 가져오게 됩니다

![image.png](./image/image%208.png)