# 대피할지도 | Admin 관리 서비스

1차 팀 프로젝트의 재난 유형별 대피소·회원·게시글 관리 웹 애플리케이션

**Admin 개발: 다른 팀원(ehdgus4026-tech). 김규현 담당: Service 개발 전담 및 Docker Host 구성.**

이 저장소는 팀 결과물을 함께 살펴보기 위한 Admin 소스 복사본입니다. 김규현의 Admin 개발 실적으로 기재하지 않습니다.

[팀원 Admin 원본](https://github.com/ehdgus4026-tech/admin) · [김규현 담당 Service](https://github.com/kyu0318/service) · [1차 프로젝트 소개](https://road-bloom-792.notion.site/1-3e110d3ee8d380f89984c41d0ea02594) · [Docker Host 구축 기록](https://road-bloom-792.notion.site/1-Docker-Host-3c910d3ee8d380e780f7c6db82d112a8)

## 팀 프로젝트 구성과 역할

| 구성 | 담당 | 범위 |
| --- | --- | --- |
| Service | 김규현 | 사용자 지도 화면, Kakao Maps 연동, 대피소 조회 API, 기존 재난 유형 탭 API |
| Admin | 다른 팀원 | 회원가입·로그인, 대피소 관리, 게시판, 데이터 수집·적재 연계 |
| Docker Host | 김규현 | 호스트 구성, 해당 호스트의 DB 연결, 데이터 처리 컨테이너·Cron 설정 기록 |

Service와 Admin은 같은 팀 프로젝트의 별도 애플리케이션. 개인 기여와 팀 전체 구현 범위를 구분

## 코드에서 확인한 기능

| 기능 | 내용 | 주요 근거 |
| --- | --- | --- |
| 계정 | 회원가입·로그인·로그아웃, HTTP 세션 상태 관리, `super` 아이디의 일반 가입 제한 | `UserController`, `UserService` |
| 대피소 관리 | 지진·수해·공습 대피소의 조회·등록·수정·삭제 | 유형별 `Controller`, `Service`, `Repository` |
| 수집 연계 | 유형별 동기화 요청과 Python 스크립트 실행 | 유형별 Controller의 `/shelters/sync/*`, `PythonScriptRunner` |
| 게시판 | 목록·상세·작성·수정·삭제, 페이지 나누기, 작성자·최고 관리자 구분 | `PostController`, `PostService` |
| 데이터 적재 | 엑셀·외부 API 데이터 수집, MySQL 적재, 애플리케이션 초기화 시 스크립트 순차 실행 | `scripts/`, `PythonScriptRunner` |

원본의 인증·권한 확인 방식과 초기화 동작 유지. 모든 관리 경로에 대한 접근 통제 검증은 별도 과제

## 사용 기술과 구조

| 구분 | 사용 범위 |
| --- | --- |
| Java 17 · Spring Boot 3.2.0 | `pom.xml` 선언, Spring MVC Controller·Service |
| Spring Data JPA · Hibernate · MySQL | 엔티티·Repository·데이터 저장 |
| Thymeleaf · HTML · JavaScript | 회원·대피소·게시판 화면 |
| Python | `pandas`, `openpyxl`, `SQLAlchemy`, `PyMySQL`, `requests`, `python-dotenv`를 사용하는 수집·적재 스크립트 |
| Maven Wrapper · Lombok | 빌드 구성 및 보조 코드 |

Commons CSV·Actuator는 의존성 선언 확인. 별도 운영 성과나 모니터링 구축 실적으로 기재하지 않음

```text
.mvn/wrapper/             Maven Wrapper 설정
src/main/java/.../        config / controller / domain / dto / repository / service
src/main/resources/      application.properties / schema.sql / data.sql / templates
scripts/                 엑셀·API 데이터 수집·적재 및 확인 스크립트
config/.env.example      Python용 비공개 환경 설정 예시
```

## 원본과 복사 범위

- 원본: `ehdgus4026-tech/admin`
- 기준 커밋: `b7aa250fe7a53566f36f8da275a5bc5980d5fda4`
- 방식: 기존 `admin-main` 이력을 유지한 파일 복사. GitHub Fork·저장소 소유권 이전·원본 Git 이력 병합에 해당하지 않음
- 제외: `target/`, `nohup.out`, 실제 `config/.env`, 빈 임시 텍스트 파일
- 공개 설정 정리: DB 비밀번호 기본값 제거, 일부 Python 스크립트의 고정 DB 비밀번호를 환경변수 참조로 변경, `.env.example`·`.gitignore` 정리
- 원본 개발자와 기능 구현 범위 유지. 복사·공개 설정·문서 편집 커밋과 원본 개발 기여를 구분

## 실행 준비

Java 17, MySQL, Python 3 및 위 Python 패키지 준비. 아래 절차는 현재 소스 기준이며 이번 복사 작업에서 애플리케이션 실행·배포는 수행하지 않았습니다.

1. 테스트용 MySQL과 `shelter_db` 준비
2. Java 프로세스 환경변수 `DB_PASSWORD` 설정. 필요 시 `DB_USER`, `DB_URL` 지정
3. `config/.env.example`을 로컬 `config/.env`로 복사하고 Python용 `DB_PASSWORD`, `API_KEY`, `EARTHQUAKE_API_KEY` 입력. 실제 값의 공개 커밋 제외
4. `check_db.py`, `excel_to_mysql.py`는 프로세스 환경변수 `DB_PASSWORD` 참조. API 스크립트는 `config/.env` 로딩. DB 호스트·계정·입력 엑셀 경로를 실행 환경에 맞춰 확인
5. 원본 `PythonScriptRunner`의 `python3` 명령과 `MS949` 로그 디코딩 확인. Windows 환경에서 실행 명령 조정 필요 가능

**애플리케이션 시작·동기화 시 데이터 교체·삭제 작업 발생 가능. 운영 DB와 분리한 테스트 환경에서 실행**

Windows 명령 프롬프트:

```bat
mvnw.cmd spring-boot:run
```

Linux·macOS의 Bash:

```bash
bash mvnw spring-boot:run
```

기본 경로: `http://localhost:8080/`, `/login`, `/signup`, `/main`, `/posts`

## 확인 범위와 남은 과제

- 확인: 원본 대비 파일 복사 범위, Python 구문·POM XML 형식, 공개본의 기존 접속정보 잔존 여부
- 미실시: DB 연동, API 수집, 브라우저 기능 테스트, 배포
- 원본의 기본 최고 관리자 계정 초기화와 평문 비밀번호 처리 유지. 실행 전 초기 비밀번호 변경 및 해시 저장 개선 필요
- 삭제 GET 경로, 관리 경로의 인증·인가·CSRF, 데이터 적재 실패 시 기존 데이터 보호는 별도 개선 과제
- 새 재난 유형 등록·연결은 팀의 고도화 계획. 기존 대피소 레코드 등록 기능과 구분
