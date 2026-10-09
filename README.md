# 🛠️ 재난 대피소 관리자 웹 애플리케이션

> 재난 유형별 대피소 데이터를 관리하고, 회원 및 게시글 기능을 제공하는 Spring Boot 기반 웹 애플리케이션입니다.

<!-- 🟠 [추가 예정] 아래에 관리자 메인 화면 캡처를 추가하세요. -->
<!-- 예시: ![관리자 메인 화면](docs/images/admin-main.png) -->

---

## 📌 프로젝트 소개

이 프로젝트는 지진·수해·공습 대피소 데이터를 데이터베이스에 저장하고 웹 화면에서 조회할 수 있도록 구성한 관리자/서비스용 웹 애플리케이션입니다. 회원가입과 로그인, 게시글 작성·조회·수정·삭제 기능을 함께 제공하며, 대피소 데이터는 Python 스크립트를 이용해 엑셀 또는 외부 API에서 수집·적재하도록 구성했습니다.

### 프로젝트 목표

- 재난 유형별 대피소 정보를 한 곳에서 조회할 수 있도록 구성
- Spring Boot의 계층형 구조를 활용해 화면 요청과 데이터 처리를 분리
- 대피소 데이터 수집·적재 과정을 스크립트로 분리
- 회원 및 게시글 기능을 통해 기본적인 웹 서비스 흐름 구현

## ✨ 주요 기능

### 1. 로그인 및 회원가입
- 로그인, 로그아웃, 회원가입 화면 제공
- 로그인 상태를 HTTP 세션에 저장
- 최고 관리자 전용 아이디의 일반 회원가입 제한

### 2. 재난 유형별 대피소 조회
- 지진 대피소 조회
- 수해 대피소 조회
- 공습 대피소 조회
- 각 유형별 데이터 모델과 Repository, Service, Controller를 분리해 관리

### 3. 게시판
- 게시글 목록 및 상세 조회
- 게시글 작성, 수정, 삭제
- 최신 게시글 우선 정렬
- 페이지 단위 게시글 조회
- 작성자 또는 최고 관리자 여부에 따른 수정·삭제 권한 확인

### 4. 데이터 수집 및 적재
- 엑셀 파일의 수해 대피소 데이터를 MySQL에 적재
- Python 스크립트를 통한 공습·지진 대피소 데이터 수집 및 적재
- Spring 애플리케이션 시작 시 데이터 처리 스크립트를 순서대로 실행하도록 구성

<!-- 🟠 [추가 예정] 로그인 화면과 대피소 조회 화면 캡처를 추가하세요. -->
<!-- 예시: ![로그인 화면](docs/images/login.png) -->
<!-- 예시: ![대피소 조회 화면](docs/images/shelter-list.png) -->

## 🧰 기술 스택

| 구분 | 기술 |
| --- | --- |
| Language | Java 17, Python |
| Backend | Spring Boot 3.2, Spring MVC |
| Persistence | Spring Data JPA, Hibernate |
| Database | MySQL |
| Frontend | Thymeleaf, HTML |
| Data Processing | Python, Apache Commons CSV |
| Build | Maven Wrapper |
| 기타 | Lombok, Spring Boot Actuator |

## 🏗️ 시스템 구조

```text
사용자
  │
  ▼
Controller ── 화면 요청 및 입력 처리
  │
  ▼
Service ───── 비즈니스 로직
  │
  ▼
Repository ── JPA를 통한 데이터 접근
  │
  ▼
MySQL

Python Scripts
  ├── Excel → MySQL 적재
  ├── 공습 대피소 데이터 수집·적재
  └── 지진 대피소 데이터 수집·적재
```

### 패키지 구성

```text
src/main/java/com/example/demo/
├── config/       # 초기 관리자 계정 및 Python 스크립트 실행
├── controller/  # 화면 및 기능별 요청 처리
├── domain/      # User, Post, 재난 유형별 대피소 엔티티
├── dto/         # 회원가입 및 게시글 입력 데이터
├── repository/  # JPA 데이터 접근 계층
└── service/     # 회원, 게시글, 대피소 비즈니스 로직

src/main/resources/
├── templates/   # Thymeleaf 화면
├── application.properties
├── schema.sql
└── data.sql

scripts/
├── excel_to_mysql.py
├── airstrike.py
├── earthquake.py
├── check_db.py
└── check_excel.py
```

## 🗃️ 데이터베이스 설계

프로젝트의 주요 도메인은 다음과 같습니다.

- `User`: 회원 계정 정보
- `Post`: 게시글 정보
- `EarthquakeShelter`: 지진 대피소 정보
- `FloodShelter`: 수해 대피소 정보
- `AirRaidShelter`: 공습 대피소 정보

<!-- 🟠 [추가 예정] 실제 코드와 일치하는 ERD 이미지를 이 위치에 추가하세요. -->
<!-- 이미지 파일을 docs/images/erd.png에 넣은 뒤 아래 예시를 사용하세요. -->
<!-- 예시: ![데이터베이스 ERD](docs/images/erd.png) -->

## 🚀 실행 방법

### 1. 사전 준비

- JDK 17 이상
- MySQL 서버
- Python 3
- 프로젝트에 필요한 Python 패키지
- Maven Wrapper 실행 권한

### 2. 데이터베이스 설정

MySQL에 프로젝트에서 사용할 데이터베이스를 준비합니다. 기본 설정은 `shelter_db` 데이터베이스를 사용하도록 되어 있습니다.

```sql
CREATE DATABASE shelter_db
  DEFAULT CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;
```

DB 주소, 사용자명, 비밀번호는 실행 환경에 맞게 설정해야 합니다. 현재 설정 파일에 개발 환경의 DB 주소가 지정되어 있으므로, 다른 환경에서는 반드시 수정해야 합니다.

### 3. 민감 정보 설정

DB 비밀번호와 외부 API 키를 소스 코드에 직접 작성하지 말고 환경 변수 또는 별도 로컬 설정 파일로 관리하세요. 예를 들어 다음 환경 변수를 사용할 수 있습니다.

```text
DB_URL=jdbc:mysql://localhost:3306/shelter_db
DB_USER=your_database_user
DB_PASSWORD=your_database_password
```

실제 `application.properties`가 해당 환경 변수를 읽도록 설정되어 있는지 확인해야 합니다. Python 스크립트의 DB 접속 정보와 API 인증 정보도 같은 방식으로 별도 관리해야 합니다.

> ⚠️ 보안 주의: 압축파일에 환경 설정 파일과 로그가 포함되어 있습니다. 공개 저장소에 비밀번호, API 키, 개인정보가 올라가지 않았는지 확인하세요. 실제 자격 증명이 이미 공개되었다면 값을 교체하고 Git 기록에서도 제거하는 것이 좋습니다.

### 4. Python 환경 준비

프로젝트의 `scripts/` 폴더에 있는 Python 스크립트에서 사용하는 패키지를 설치하고, 각 스크립트가 참조하는 엑셀 파일 및 API 설정이 준비되어 있는지 확인합니다.

현재 애플리케이션은 시작 과정에서 `python3` 명령으로 스크립트를 실행하도록 작성되어 있습니다. Windows 환경에서는 Python 실행 명령이 다를 수 있으므로 `PythonScriptRunner.java`의 실행 명령을 환경에 맞게 확인해야 합니다.

### 5. 애플리케이션 실행

프로젝트 최상위 폴더에서 실행합니다.

**Windows**

```bat
mvnw.cmd spring-boot:run
```

**macOS / Linux**

```bash
./mvnw spring-boot:run
```

브라우저에서 아래 주소로 접속합니다.

- 기본 경로: `http://localhost:8080/` — 로그인 페이지로 이동
- 로그인: `http://localhost:8080/login`
- 회원가입: `http://localhost:8080/signup`
- 메인 화면: `http://localhost:8080/main`
- 게시글 목록: `http://localhost:8080/posts`

> 실행 전에 MySQL 연결, Python 실행 환경, 데이터 수집 설정을 확인하세요. 애플리케이션 시작 시 Python 스크립트가 자동 실행되므로 데이터베이스에 쓰기 작업이 발생할 수 있습니다.

## 🧪 테스트 및 확인

Maven 테스트 실행:

**Windows**
```bat
mvnw.cmd test
```

**macOS / Linux**
```bash
./mvnw test
```

현재 압축파일에는 별도의 테스트 소스가 포함되어 있지 않으므로, 명령어 실행 결과만으로 모든 기능이 검증되는 것은 아닙니다. 로그인/회원가입, 게시글 권한, 재난 유형별 조회, Python 데이터 적재를 각각 확인하는 테스트를 추가하는 것을 권장합니다.

## 🔐 보안 및 개선 과제

- 비밀번호를 평문으로 저장하지 않고 BCrypt 등으로 해시 처리
- 기본 최고 관리자 계정의 초기 비밀번호 변경 및 안전한 초기화 방식 적용
- DB 접속 정보와 외부 API 키를 환경 변수로 분리
- 게시글 삭제 요청은 GET이 아닌 POST/DELETE 방식으로 변경하고 CSRF 방어 적용
- 로그인 및 게시글 작성·수정·삭제 권한을 서버 측에서 일관되게 검증
- Python 스크립트 실패 시 기존 데이터를 보호하고, 수집 후 검증된 데이터만 반영
- Python 실행 경로와 로그 인코딩을 운영체제별 설정으로 분리
- `.env`, 로그, `target/` 등 민감 정보 및 빌드 결과물이 저장소에 포함되지 않도록 `.gitignore` 정리
- 주요 Controller와 Service에 자동화 테스트 추가

## 📸 포트폴리오 자료

<!-- 🟠 [추가 예정] 실제 실행 화면을 추가하세요. 권장: 메인 화면, 로그인/회원가입, 대피소 조회, 게시판 -->
<!-- 🟠 [추가 예정] ERD 이미지를 추가하세요. -->
<!-- 🟠 [추가 예정] 직접 해결한 문제와 해결 과정을 1~2개 작성하면 기술 역량을 보여주기 좋습니다. -->

## 👤 프로젝트에서 설명할 수 있는 기술 포인트

- Spring Boot 계층형 아키텍처를 이용한 기능 분리
- Spring Data JPA 기반 엔티티 및 Repository 설계
- 세션을 활용한 로그인 상태 관리
- 게시글 페이지네이션과 작성자 기반 권한 확인
- Python과 MySQL을 연계한 외부 데이터 수집 및 적재 자동화

---

> 본 README는 제공된 소스 코드 구조를 기준으로 작성되었습니다. 실제 배포 또는 다른 PC에서 실행하기 전에는 DB 주소, 환경 변수, Python 의존성 및 데이터 수집 설정을 환경에 맞게 확인하세요.
