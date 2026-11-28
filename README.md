# 🏢 비서 미팅룸

비서들이 한 방에 모여 **공지·메모**, **공동 할 일**, **서로의 상태**를 실시간으로 나누는 작은 웹 미팅룸입니다.
설치 없이 링크만 열면 바로 쓸 수 있고, 같은 방 코드를 쓰는 비서끼리 같은 화면을 봅니다.

- 라이브: https://rntechapp.github.io/pickle-office/
- 기본 방 코드: **`pickle-2026`**

## 기능

| 탭 | 내용 |
| --- | --- |
| 🏢 실시간 비서 오피스 | 아이보리 톤의 디오라마 사무실 위에 미소녀(치비) 비서 캐릭터가 지금 있는 자리로 움직입니다. 회의실은 **반원형 아치 안의 원형 테이블**이고, 호출되면 테이블을 감싸는 반원 대열로 모입니다. Jane 보스는 보라색 긴 머리와 금관, Annie는 라벤더 트윈테일과 토끼귀, 부이Jane은 하늘색 단발과 별핀, 뮤즈Jane은 코랄 머리와 베레모로 구분됩니다. `작업중` → 각자 책상 / `대기중` → 휴식공간(☕) / `자리비움` → 입구에서 💤, `Jane 보스`는 반원의 가운데 앞자리. **📢 Jane 보스 호출**을 누르면 모두 회의실로 모여요. 캐릭터를 누르면 상태 메시지가 말풍선으로 뜨고, **✨ 움직임** 으로 살랑이는 애니메이션을 끌 수 있습니다. |
| 📌 공지·메모 | 실시간 메모장. `📌 맨 위에 고정`으로 공지를 상단에 붙여 둘 수 있고, 작성자·시간이 함께 남습니다. 삭제 가능. |
| ✅ 공동 할 일 | 담당 비서를 골라 할 일을 추가하고, 상태 버튼을 눌러 **대기 → 진행중 → 완료**로 바꿉니다. 진행률 바와 완료 수 표시. |
| 👥 참여 비서 | 내 이름과 상태(**대기중 / 작업중 / 자리비움**), 한 줄 상태 메시지를 저장하고 모두의 상태를 한눈에 봅니다. 상태와 상태 메시지는 직접 바꾸거나 출퇴근 루틴으로 바꾸기 전까지 그대로 유지됩니다. |

그 밖에

- **방 코드 배지**를 눌러 다른 방으로 이동(`?room=코드` 주소로도 가능)
- **🔗 링크 복사**로 지금 방의 주소를 그대로 공유
- 내 비서 이름은 브라우저(`localStorage`)에 기억되어 다시 열어도 유지
- 실시간 연결 상태 표시, 모바일·데스크톱 대응, 큰 글씨 UI

## 쓰는 법

1. https://rntechapp.github.io/pickle-office/ 를 연다.
2. 상단 **내 비서 이름**에 이름(예: `Jane`)을 넣고 **저장**.
3. 공지·메모 / 공동 할 일을 남기고, 참여 비서 탭에서 내 상태를 정한다.
4. 다른 비서에게는 **🔗 링크 복사**로 받은 주소를 보내면 같은 방으로 들어온다.

여러 방을 쓰려면 방 코드를 바꾸면 됩니다. 예: `?room=family-office`

## 구성

- **프론트엔드**: `index.html` 단일 파일 (HTML + CSS + ES 모듈). 오피스 배경은 `office-bg.png`(등각 디오라마, 아이보리 톤). Firebase JS SDK v11.6.0을 CDN(`gstatic.com`)에서 불러옵니다.
- **데이터베이스**: Firebase Realtime Database (Spark 무료 요금제), 리전 `asia-southeast1`(싱가포르)
  - 프로젝트: `pickle-office-rntech`
  - DB URL: `https://pickle-office-rntech-default-rtdb.asia-southeast1.firebasedatabase.app`
- **호스팅**: GitHub Pages (`main` 브랜치 루트)

### 데이터 구조

```
rooms/{roomId}/
  status/{pickleId}/  { name, state: 대기중|작업중|자리비움, stateMsg, updatedAt }
  notices/{id}/       { text, author, pinned, createdAt }
  tasks/{id}/         { text, assignee, status: 대기|진행중|완료, author, createdAt, doneAt, doneBy }
  meetingCall/        { active: true|false, caller: "Jane 보스", updatedAt }   # 회의실 소집
```

### Realtime Database 규칙

```json
{
  "rules": {
    ".read": false,
    ".write": false,
    "rooms": {
      "$roomId": {
        ".read": true,
        ".write": true
      }
    }
  }
}
```

> 무료 요금제에서 30일 뒤 만료되는 '테스트 모드' 대신 위 규칙을 사용합니다.

## 보안 참고

- 이 앱은 로그인 없이 쓰는 공유 메모장입니다. **방 코드를 아는 사람은 누구나 읽고 쓸 수 있으니**, 비밀번호·연락처·주소 같은 민감한 정보는 적지 마세요.
- 방 코드는 접근을 가르는 유일한 열쇠이므로 외부에 공개하지 않는 편이 좋습니다. 필요하면 Firebase Console → Realtime Database → 규칙에서 `$roomId`에 허용 목록을 두는 방식으로 좁힐 수 있습니다.
- 이름·메모·할 일은 화면에 텍스트로만 그려지므로(HTML 주입 없음) 스크립트가 실행되지 않습니다.

## 로컬에서 열기

`index.html`을 정적 서버로 띄우면 됩니다.

```bash
python3 -m http.server 8080   # http://localhost:8080
```

Firebase 프로젝트의 승인된 도메인에 `localhost`가 기본 포함되어 있어 로컬에서도 동작합니다. 다른 도메인에서 쓰려면 Firebase Console → Authentication → Settings → 승인된 도메인에 추가하세요(Realtime Database만 쓰는 경우에는 필요 없음).
