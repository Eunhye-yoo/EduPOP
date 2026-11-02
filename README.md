<p align="center">
  <img src="./EduPOP/src/main/resources/static/images/exp/character.png" alt="EduPOP 성장 캐릭터" width="150">
</p>

<h1 align="center">EduPOP</h1>

<p align="center"><strong>반 운영부터 시험·성적 분석·복습까지 연결한 학원 관리 서비스</strong></p>
<p align="center"><a href="./README.en.md">English</a> · <a href="./docs/EduPOP_Portfolio_Eunhye_Yoo.pdf">포트폴리오 PDF</a> · <a href="https://youtu.be/mkAcPCD7VOY">시연 영상</a></p>

반 관리, 시험, 성적 분석과 복습을 연결한 팀 프로젝트. **반 관리·강사·학생 배정, 수업 전 대시보드와 개인 성적표 구현 담당.**

## 담당 기능·접근통제

### 반 관리·강사·학생 배정

- 반 생성·수정, ACTIVE/CLOSED 상태를 통한 종강 관리
- 한 반에 여러 강사를 연결하는 N:M 매핑, 학생 배정·동기화
- 중복 배정·정원 검사
- 반 생성 시 로그인 사용자의 학원 ID 적용, 상세 조회 시 학원 소유권 비교
- Controller → Service → MyBatis → MySQL 연결

[Controller](./EduPOP/src/main/java/com/example/EduPOP/controller/classroom/ClassroomController.java) · [Service](./EduPOP/src/main/java/com/example/EduPOP/service/classroom/ClassroomService.java) · [구현 메모](./docs/access-control-review.md)

### 수업 전 대시보드·개인 성적표

- 최근 시험의 반 평균·전체 평균 비교
- 반·학생별 취약 영역과 오답률, 위험·주의·안정 신호 표시
- 개인별 성적 추이·영역별 성취도, 강점 Top 3·취약 유형 Worst 3 제공
- 분석 메서드 분리 및 월간 리포트 공통 활용

‘3분 대시보드’는 수업 직전 확인을 위한 사용 목표입니다.

[AnalyticsService](./EduPOP/src/main/java/com/example/EduPOP/service/analytics/AnalyticsService.java) · [AnalyticsController](./EduPOP/src/main/java/com/example/EduPOP/controller/analytics/AnalyticsController.java)

## 팀 공통 기능

| 영역 | 프로젝트 공통 코드 |
| --- | --- |
| 시험 | 시험 제작·온라인 응시·OMR 입력·PDF 텍스트 추출 |
| 복습 | 오답 복습·AI 유사 문제·단어 게임 |
| 인증 | 세션 로그인·BCrypt·역할별 URL 접근·Kakao/Naver/Google OAuth |
| 학원 등록 | 국세청 사업자등록정보 진위확인 API |

## 애플리케이션 구조

~~~mermaid
flowchart TB
    U["관리자 · 교사 · 학생"] --> V["Thymeleaf View"]
    V --> S["Spring MVC · Spring Security"]
    S --> B["Controller · Service"]
    B --> D["MyBatis · MySQL"]
    B --> X["OAuth · OpenAI · 국세청 API"]
    B --> P["PDFBox"]
    V --> C["Chart.js"]
~~~

반·강사·학생 관계의 매핑 테이블 관리. Service·Mapper의 성적 데이터 조회·가공 및 Thymeleaf·JavaScript 차트 연동.

## 기술

Java 17 · Spring Boot 4.1.0 · MyBatis Starter 4.0.1 · MySQL · Thymeleaf · JavaScript · Chart.js · Maven.

반 관리·분석 기능은 MyBatis 기반입니다. 저장소에는 JPA 의존성과 일부 Report 도메인 코드도 포함되어 있습니다.

## 실행

JDK 17과 MySQL 8.x를 준비합니다. `EduPOP/DB/schema.sql`에는 테이블 정의와 후속 ALTER 문이 섞여 있고 중복 열 추가 구문이 있으므로 적용할 스키마를 먼저 확인합니다.

DB와 필요한 외부 서비스 설정은 실행 환경에서 주입합니다.

| 환경변수 | 용도 |
| --- | --- |
| DB_URL, DB_USERNAME, DB_PASSWORD | MySQL |
| JWT_SECRET | JWT 사용 시 설정 |
| OPENAI_API_KEY | AI 유사 문제 |
| KAKAO_CLIENT_ID, KAKAO_REDIRECT_URI | Kakao 로그인 |
| NAVER_CLIENT_ID, NAVER_CLIENT_SECRET, NAVER_REDIRECT_URI | Naver 로그인 |
| GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET, GOOGLE_REDIRECT_URI | Google 로그인 |
| NTS_BUSINESS_API_KEY | 사업자등록 검증 |

실행 전 application.properties의 연동 항목과 Google 인증정보 등 필수 설정을 확인합니다.

~~~bash
git clone https://github.com/Eunhye-yoo/EduPOP.git
cd EduPOP/EduPOP
./mvnw spring-boot:run
~~~

Windows에서는 `mvnw.cmd spring-boot:run` 사용. 기본 접속 주소는 http://localhost:8080.

## 후속 작업

수정·상태 변경·배정 경로의 학원 소유권 검사를 통일하고 학원 2개 계정으로 요청·DB 결과를 비교할 예정입니다. 학부모용 성적표 공유 링크도 보완할 항목입니다.

[코드 기준 접근통제 검토](./docs/access-control-review.md) · [프로필](https://github.com/Eunhye-yoo)
