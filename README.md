
# 🚓 잡아라 (JAVARA)
### Edge Device & AI Object Detection을 활용한 실시간 수배차량 자동 단속 시스템
> 프로젝트 명: **잡아라** (JAVARA)
> 개발 기간: **2025.03 ~ 2025.06**

<br>

## 📖 프로젝트 소개

**잡아라(JAVARA)** 는 AI 기반 객체 탐지(YOLO) 기술과 엣지 디바이스(순찰차, 일반 차량 장착 가능)를 활용하여 기존 단속 시스템의 한계를 극복하고, **수배 차량을 자동으로 탐지하고 실시간 알림을 전송함으로써**, 효율적이고 신속한 공공 안전망을 구축하는 시스템입니다.

<br>
엣지 디바이스가 실시간으로 번호판을 분석해 수배 차량을 탐지하고, 위치와 이미지를 서버로 전송합니다. 서버는 이를 바탕으로 가장 가까운 경찰에게 임무를 자동으로 할당해 신속한 대응과 단속 자동화를 실현합니다.


<br>
<br>

![introduction](https://raw.githubusercontent.com/gammapasta/Javara/refs/heads/main/img/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EC%86%8C%EA%B0%9C.png)

<br>


---

## 📜 목차

1. [주요 기능](#주요-기능)
2. [실제 구현 화면](#실제-구현-화면)
3. [기술 스택](#기술-스택)
4. [아키텍처 구성도](#아키텍처-구성도)
5. [ERD](#erd)
6. [API 명세서](#api-명세서)
7. [디렉터리 구조](#디렉터리-구조)
8. [팀원 소개](#팀원-소개)


<br>

---

## ⭐️ 주요 기능 <a id="주요-기능"></a>

### 백엔드 시스템

| 기능 | 상세 설명 |
| --- | --- |
| **실시간 통신 및 데이터 처리 (WebSocket)** | 엣지 디바이스와 서버를 WebSocket 연결하여, 사용자의 상태(순찰중, 추적중, 비활성 등)에 따른 명령어를 실시간으로 전송하고, 차량의 위치 데이터를 지속적으로 수신 및 처리한다. |
| **수배차량 자동 감지 및 검증** | 엣지 디바이스에서 촬영하는 동영상을 기반으로, YOLO로 탐지된 번호판이 실제 DB에 등록된 수배차량인지 검증한다. 검증 완료 시 서버로 자신의 위치와 이미지를 전송한다. |
| **임무 할당 시스템** | **1. 엣지 디바이스 기반:** 순찰 중인 경찰이 직접 수배차량을 발견하면 서버에서 해당 경찰에게 추적 임무를 부여한다.<br>**2. 서버 주도 (스케줄러):** 일반 시민이나 다른 업무 중인 경찰이 발견한 경우, 서버 스케줄러가 주기적으로 가장 가까운 경찰에게 임무를 부여한다. |
| **실시간 경보 및 알림 (SSE)** | 임무가 할당되면 경찰의 모바일 앱으로 Server-Sent Events(SSE)를 통해 실시간 push 알림을 전송하여, 추적 수락/거절 여부를 결정 가능하다. |
| **보안 및 인증 (Spring Security & JWT)** | JWT기반의 인증 시스템을 사용하고, 사용자의 역할(경찰, 시민, 관리자)에 따른 접근 권한을 제어한다. |
| **시스템 안정성 및 관리** | - **DB 백업:** 스케줄러를 통해 매일 특정 시간에 DB를 자동으로 백업한다.<br>- **로그 관리:** 매일 로그 파일을 생성 및 저장하여 시스템 모니터링이 가능하다.<br>- **자동 배포 (CI/CD):** GitHub Actions를 통해 `main` 브랜치에 코드를 PUSH할 때마다 학교 서버에 자동으로 빌드 및 배포가 가능하다. |

<br>

### 👮 경찰 사용자 기능

-   **로그인/회원가입**: JWT 기반으로 사용자 인증 및 관리 기능을 제공한다.
-   **순찰 모드**: 순찰 시작/종료 시 엣지 디바이스로 상태 명령어를 전송하며, 위치 데이터 전송받는다.
-   **실시간 추적 요청**: SSE를 통해 모바일로 수배차량 발견 알림을 수신하고, 임무를 수락하거나 거절할 수 있다.
-   **지도 기반 추적**: 임무 수락 시, 모바일 앱의 지도 위에서 자신의 위치와 수배차량의 실시간 위치를 확인한다.
-   **임무 결과 보고**: 추적 종료 후, '성공' 또는 '실패' 결과를 서버에 보고하여 임무를 종결한다.

<br>

### 👨‍👩‍👧‍👦 일반 시민 사용자 기능

-   **디바이스 페어링 및 주행 모드**: 모바일 앱에 로그인 하여 엣지 디바이스를 서버와 페어링 하고, '주행 시작' 버튼으로 자동 신고 기능을 활성화한다.
-   **자동 신고 및 실적 조회**: 주행 중 수배차량 발견 시 자동으로 서버에 신고되며, 앱 내에서 자신의 신고 건수, 현재 벌점, 벌점 감면 예상 실적 등 조회할 수 있다.

<br>

---

## 🖥️ 실제 구현 화면 <a id="실제-구현-화면"></a>

![screen1](https://raw.githubusercontent.com/gammapasta/Javara/refs/heads/main/img/%EB%AA%A8%EB%B0%94%EC%9D%BC%20%EC%8B%9C%EC%97%B0%EC%98%81%EC%83%811.gif)

<br>

![screen2](https://raw.githubusercontent.com/gammapasta/Javara/refs/heads/main/img/%EB%AA%A8%EB%B0%94%EC%9D%BC%20%EC%8B%9C%EC%97%B02.gif)

<br>

![screen3](https://raw.githubusercontent.com/gammapasta/Javara/refs/heads/main/img/%EC%9B%B9%20%EC%8B%9C%EC%97%B0.gif)

<br>

---

## 🛠️ 기술 스택 <a id="기술-스택"></a>

### 백엔드
![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring](https://img.shields.io/badge/spring-%236DB33F.svg?style=for-the-badge&logo=spring&logoColor=white)
![SpringSecurity](https://img.shields.io/badge/springsecurity-6DB33F?style=for-the-badge&logo=springsecurity&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-black?style=for-the-badge&logo=JSON%20web%20tokens)
![MariaDB](https://img.shields.io/badge/MariaDB-003545?style=for-the-badge&logo=mariadb&logoColor=white)

<br>

---

## 🏗️ 아키텍처 구성도 <a id="아키텍처-구성도"></a>

![architecture-diagram](https://raw.githubusercontent.com/gammapasta/Javara/refs/heads/main/img/%EC%95%84%ED%82%A4%ED%85%8D%EC%B3%90.png)

- 엣지 디바이스 <= <small>WebSocket, RESR API</small> => 백엔드 서버
- 백엔드 서버 <= <small>SSE, REST API</small> => 모바일 앱


<br>

---

## 🗂️ ERD <a id="erd"></a>


![erd-diagram](https://raw.githubusercontent.com/gammapasta/Javara/refs/heads/main/img/ERD.png)

<br>

---

## 📖 API 명세서 <a id="api-명세서"></a>

<details>
<summary>API 목록 (클릭하여 펼치기)</summary>

### **Auth**

| 태그 | 이름 | URL | 만든사람 |
|---|---|---|---|
| POST | 로그인 | `/api/auth/login` | 최준호 |
| POST | 회원가입 | `/api/auth/signup` | 최준호 |
| POST | 로그아웃 | `/api/auth/logout` | 최준호 |
| POST | 리프레시 토큰으로 엑세스 토큰 발급 | `/api/auth/refresh` | 최준호 |

### **App Member**

| 태그 | 이름 | URL | 만든사람 |
|---|---|---|---|
| POST | 비밀번호 변경 | `/api/app/members/password` | 최준호 |
| POST | 디바이스id 변경 | `/api/app/members/device/{deviceId}` | 최준호 |
| GET | 벌점 조회 | `/api/app/members/penalty` | 최준호 |
| GET | 내 정보 조회 | `/api/app/members/me` | 최준호 |

### **App Wanted Vehicle**

| 태그 | 이름 | URL | 만든사람 |
|---|---|---|---|
| GET | 수배차량 조회 | `/api/app/wanted-vehicles/{wanted-vehicle}` | 최준호 |
| GET | 수배차량 전체 조회 | `/api/app/wanted-vehicles` | 최준호 |
| GET | 내가 발견한 수배차량 목록 조회 | `/api/app/wanted-vehicles/detections` | 최준호 |

### **Tracking Decision**

| 태그 | 이름 | URL | 만든사람 |
|---|---|---|---|
| POST | 추적 수락 여부 결정 | `/api/app/decision/tracking` | 최준호 |
| POST | 임무 결과 설정 | `/api/app/decision/result` | 최준호 |

### **SSE**

| 태그 | 이름 | URL | 만든사람 |
|---|---|---|---|
| GET | SSE 연결 | `/api/sse/connect` | 최준호 |

### **Edge Device**

| 태그 | 이름 | URL | 만든사람 |
|---|---|---|---|
| POST | 순찰모드 | `/api/edge-devices/app/police/{status}` | 최준호 |
| POST | (일반 시민) 주행모드 | `/api/edge-devices/app/user/{status}` | 최준호 |
| POST | 수배차량검증 | `/api/edge-devices/verification` | 최준호 |
| GET | 최신 수배차량 목록 가져오기 | `/api/edge-devices/WantedVehicleList` | 최준호 |

### **관리자 API**

| 태그 | 이름 | URL | 만든사람 |
|---|---|---|---|
| POST | 수배차량 등록 | `/api/admin/wanted-vehicles` | 신준호 |
| GET | 전체 수배차량 조회 | `/api/admin/wanted-vehicles` | 신준호 |
| GET | 특정 수배차량 조회(ID로) | `/api/admin/wanted-vehicles/{id}` | 신준호 |
| PUT | 특정 수배차량 수정(ID로) | `/api/admin/wanted-vehicles/{id}` | 신준호 |
| DELETE | 특정 수배차량 삭제(ID로) | `/api/admin/wanted-vehicles/{id}` | 신준호 |
| POST | 멤버 등록 | `/api/admin/members` | 신준호 |
| GET | 전체 멤버 조회 | `/api/admin/members` | 신준호 |
| GET | 특정 멤버 조회(ID로) | `/api/admin/members/{id}` | 신준호 |
| PUT | 특정 멤버 수정(ID로) | `/api/admin/members/{id}` | 신준호 |
| DELETE | 특정 멤버 삭제(ID로) | `/api/admin/members/{id}` | 신준호 |
| GET | 웹소켓에 접속한 사람 수 | `/api/admin/websocket-connections` | 최준호 |
| GET | sse에 접속한 엣지 디바이스 수 | `/api/admin/sse-connections` | 최준호 |


</details>


<br>

---

## 📁 디렉터리 구조 <a id="디렉터리-구조"></a>

<details>
<summary>디렉터리 구조 (클릭하여 펼치기)</summary>

```

main
├─ .gitattributes
├─ .github
│  ├─ ISSUE_TEMPLATE
│  │  └─ feature-issue-template.md
│  └─ workflows
│     └─ spring-server-deploy.yml
├─ .gitignore
├─ LICENSE
├─ README.md
├─ build.gradle
├─ gradlew
├─ gradlew.bat
├─ settings.gradle
└─ src
   ├─ main
   │  ├─ java
   │  │  └─ com
   │  │     └─ team109
   │  │        └─ javara
   │  │           ├─ JavaraApplication.java
   │  │           ├─ domain
   │  │           │  ├─ admin
   │  │           │  │  ├─ controller
   │  │           │  │  │  └─ AdminController.java
   │  │           │  │  └─ dto
   │  │           │  │     ├─ SessionConnectionsResponseDto.java
   │  │           │  │     └─ SseConnectionsResponseDto.java
   │  │           │  ├─ auth
   │  │           │  │  ├─ controller
   │  │           │  │  │  └─ AuthController.java
   │  │           │  │  ├─ dto
   │  │           │  │  │  ├─ LoginRequestDto.java
   │  │           │  │  │  ├─ RefreshRequestDto.java
   │  │           │  │  │  ├─ SignupRequestDto.java
   │  │           │  │  │  └─ TokenResponseDto.java
   │  │           │  │  ├─ entity
   │  │           │  │  │  └─ RefreshToken.java
   │  │           │  │  ├─ jwt
   │  │           │  │  │  ├─ JwtAuthenticationFilter.java
   │  │           │  │  │  ├─ JwtTokenInfo.java
   │  │           │  │  │  └─ JwtTokenProvider.java
   │  │           │  │  ├─ repository
   │  │           │  │  │  └─ RefreshTokenRepository.java
   │  │           │  │  └─ service
   │  │           │  │     ├─ AuthService.java
   │  │           │  │     └─ CustomUserDetailsService.java
   │  │           │  ├─ edgeDevice
   │  │           │  │  ├─ controller
   │  │           │  │  │  └─ EdgeDeviceController.java
   │  │           │  │  ├─ dto
   │  │           │  │  │  └─ VerificationResponseDto.java
   │  │           │  │  └─ service
   │  │           │  │     └─ EdgeDeviceService.java
   │  │           │  ├─ event
   │  │           │  │  ├─ event
   │  │           │  │  │  ├─ EdgeDeviceEvent.java
   │  │           │  │  │  ├─ ServerInitiatedEvent.java
   │  │           │  │  │  └─ TaskEvent.java
   │  │           │  │  ├─ eventListener
   │  │           │  │  │  └─ EventListener.java
   │  │           │  │  └─ service
   │  │           │  │     ├─ AsyncDecisionService.java
   │  │           │  │     ├─ AsyncFirstTaskDecision.java
   │  │           │  │     └─ AsyncServerInitiatedEvent.java
   │  │           │  ├─ image
   │  │           │  │  ├─ dto
   │  │           │  │  │  └─ ImageResponse.java
   │  │           │  │  ├─ entity
   │  │           │  │  │  └─ Image.java
   │  │           │  │  ├─ repository
   │  │           │  │  │  └─ ImageRepository.java
   │  │           │  │  └─ service
   │  │           │  │     └─ ImageService.java
   │  │           │  ├─ location
   │  │           │  │  ├─ controller
   │  │           │  │  │  ├─ PoliceLocationController.java
   │  │           │  │  │  └─ WantedVehicleLocationController.java
   │  │           │  │  ├─ dto
   │  │           │  │  │  ├─ PLResponseDto.java
   │  │           │  │  │  ├─ PoliceLocationRequestDto.java
   │  │           │  │  │  ├─ PoliceLocationResponseDto.java
   │  │           │  │  │  └─ WantedVehicleLocationResponseDto.java
   │  │           │  │  ├─ entity
   │  │           │  │  │  ├─ PoliceLocation.java
   │  │           │  │  │  └─ WantedVehicleLocation.java
   │  │           │  │  ├─ repository
   │  │           │  │  │  ├─ PoliceLocationRepository.java
   │  │           │  │  │  └─ WantedVehicleLocationRepository.java
   │  │           │  │  └─ service
   │  │           │  │     ├─ PoliceFindService.java
   │  │           │  │     ├─ PoliceLocationService.java
   │  │           │  │     ├─ PoliceLocationServiceImpl.java
   │  │           │  │     └─ WantedVehicleLocationService.java
   │  │           │  ├─ member
   │  │           │  │  ├─ controller
   │  │           │  │  │  ├─ AdminMemberController.java
   │  │           │  │  │  └─ AppMemberController.java
   │  │           │  │  ├─ dto
   │  │           │  │  │  ├─ MemberInfoResponseDto.java
   │  │           │  │  │  ├─ MemberPenaltyPointsDto.java
   │  │           │  │  │  ├─ MemberRequestDto.java
   │  │           │  │  │  ├─ MemberSearchRequestDto.java
   │  │           │  │  │  ├─ MemberUpdateByFeildRequestDto.java
   │  │           │  │  │  ├─ MemberUpdatePasswordDto.java
   │  │           │  │  │  └─ MyInfoDto.java
   │  │           │  │  ├─ entity
   │  │           │  │  │  ├─ Member.java
   │  │           │  │  │  └─ enums
   │  │           │  │  │     ├─ Gender.java
   │  │           │  │  │     ├─ MemberStatus.java
   │  │           │  │  │     └─ Role.java
   │  │           │  │  ├─ repository
   │  │           │  │  │  └─ MemberRepository.java
   │  │           │  │  └─ service
   │  │           │  │     ├─ AdminMemberService.java
   │  │           │  │     ├─ AdminMemberServiceImpl.java
   │  │           │  │     └─ MemberService.java
   │  │           │  ├─ task
   │  │           │  │  ├─ dto
   │  │           │  │  │  └─ taskAndWantedDto.java
   │  │           │  │  ├─ entity
   │  │           │  │  │  ├─ Task.java
   │  │           │  │  │  └─ enums
   │  │           │  │  │     └─ TaskStatus.java
   │  │           │  │  ├─ repository
   │  │           │  │  │  └─ TaskRepository.java
   │  │           │  │  └─ service
   │  │           │  │     └─ TaskService.java
   │  │           │  ├─ tracking
   │  │           │  │  ├─ controller
   │  │           │  │  │  └─ TrackingDecisionController.java
   │  │           │  │  ├─ dto
   │  │           │  │  │  ├─ TrackingDecisionRequestDto.java
   │  │           │  │  │  ├─ TrackingDecisionResponseDto.java
   │  │           │  │  │  └─ TrackingResultRequestDto.java
   │  │           │  │  └─ service
   │  │           │  │     └─ TrackingDecisionService.java
   │  │           │  ├─ vehicle
   │  │           │  │  ├─ component
   │  │           │  │  │  └─ WantedSet.java
   │  │           │  │  ├─ controller
   │  │           │  │  │  ├─ AppWantedVehicleController.java
   │  │           │  │  │  └─ WantedVehicleController.java
   │  │           │  │  ├─ dto
   │  │           │  │  │  ├─ AppWantedVehicleRequestDto.java
   │  │           │  │  │  ├─ ArrestStatusDto.java
   │  │           │  │  │  ├─ EdgeDeviceWantedVehicleResponseDto.java
   │  │           │  │  │  ├─ SearchRequestDto.java
   │  │           │  │  │  ├─ WantedVehicleRequestDto.java
   │  │           │  │  │  └─ WantedVehicleResponseDto.java
   │  │           │  │  ├─ entity
   │  │           │  │  │  ├─ WantedVehicle.java
   │  │           │  │  │  └─ enums
   │  │           │  │  │     └─ WantedVehicleStatus.java
   │  │           │  │  ├─ repository
   │  │           │  │  │  └─ WantedVehicleRepository.java
   │  │           │  │  └─ service
   │  │           │  │     ├─ AppWantedVehicleService.java
   │  │           │  │     ├─ WantedVehicleService.java
   │  │           │  │     └─ WantedVehicleServiceImpl.java
   │  │           │  └─ webSocket
   │  │           │     ├─ component
   │  │           │     │  └─ SessionConnections.java
   │  │           │     ├─ controller
   │  │           │     │  └─ WebSocketController.java
   │  │           │     ├─ dto
   │  │           │     │  ├─ DeviceLocationDto.java
   │  │           │     │  └─ SessionCommand.java
   │  │           │     ├─ eventListener
   │  │           │     │  └─ WebSocketEventListener.java
   │  │           │     ├─ registry
   │  │           │     │  └─ SessionRegistry.java
   │  │           │     └─ service
   │  │           │        ├─ CommandService.java
   │  │           │        └─ WebSocketService.java
   │  │           └─ global
   │  │              ├─ common
   │  │              │  ├─ exception
   │  │              │  │  ├─ ErrorCode.java
   │  │              │  │  ├─ GlobalException.java
   │  │              │  │  └─ GlobalExceptionHandler.java
   │  │              │  ├─ response
   │  │              │  │  └─ BaseResponse.java
   │  │              │  └─ scheduler
   │  │              │     ├─ DatabaseBackupScheduler.java
   │  │              │     └─ ServerInitiatedTaskAssignmentScheduler.java
   │  │              ├─ config
   │  │              │  ├─ AsyncConfig.java
   │  │              │  ├─ SwaggerConfig.java
   │  │              │  ├─ WebConfig.java
   │  │              │  ├─ WebSocketConfig.java
   │  │              │  └─ security
   │  │              │     ├─ CorsConfig.java
   │  │              │     ├─ JwtConfig.java
   │  │              │     ├─ SecurityConfig.java
   │  │              │     ├─ WebSocketSecurityConfig.java
   │  │              │     └─ auth
   │  │              │        ├─ CustomAccessDeniedHandler.java
   │  │              │        └─ CustomAuthenticationEntryPoint.java
   │  │              ├─ sse
   │  │              │  ├─ component
   │  │              │  │  └─ SseConnections.java
   │  │              │  ├─ controller
   │  │              │  │  └─ SseController.java
   │  │              │  └─ service
   │  │              │     ├─ NotificationService.java
   │  │              │     └─ SseEmitterService.java
   │  │              └─ test.java
   │  └─ resources
   │     ├─ application-prod.yml
   │     └─ logback-spring.xml
   └─ test
      └─ java
         └─ com
            └─ team109
               └─ javara
                  └─ JavaraApplicationTests.java

```
<small>©generated by [Project Tree Generator](https://woochanleee.github.io/project-tree-generator)</small>
</details>

<br>

---

## 🧑‍💻 팀원 소개 <a id="팀원-소개"></a>
| 이름 | 역할 | 담당 | 깃허브 링크 |
| --- | --- | --- | --- |
| **조윤성** | AI, 팀장 | AI 기능 담당| |
| **맹주석** | AI  | AI 기능 담당| |
| **김예은** | 프론트엔드 | 관리자 페이지, 안드로이드 앱| |
| **신준호** | 백엔드 | 관리자 CRUD | https://github.com/wnsgh7368 |
| **최준호** | 백엔드 | 전체 시스템 설계 및 백엔드 총괄 | https://github.com/gammapasta |
