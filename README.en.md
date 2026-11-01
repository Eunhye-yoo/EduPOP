<p align="center">
  <img src="./EduPOP/src/main/resources/static/images/exp/character.png" alt="EduPOP character" width="150">
</p>

<h1 align="center">EduPOP</h1>

<p align="center"><strong>Academy operations and learning management</strong></p>
<p align="center"><a href="./README.md">한국어</a> · <a href="./docs/EduPOP_Portfolio_Eunhye_Yoo.pdf">Portfolio PDF, Korean</a> · <a href="https://youtu.be/mkAcPCD7VOY">Demo</a></p>

A team project connecting class management, tests, performance analysis, and review. **I implemented class management, teacher/student assignment, the pre-class dashboard, and individual performance reports.**

## My Work

### Class Management and Assignment

- Class creation/update and ACTIVE/CLOSED status management
- N:M instructor mapping and student assignment/synchronization
- Duplicate assignment and capacity checks
- Academy ID taken from the logged-in user during class creation; academy comparison on class-detail requests
- Controller → Service → MyBatis → MySQL implementation

[Controller](./EduPOP/src/main/java/com/example/EduPOP/controller/classroom/ClassroomController.java) · [Service](./EduPOP/src/main/java/com/example/EduPOP/service/classroom/ClassroomService.java) · [Implementation notes](./docs/access-control-review.en.md)

### Pre-Class Dashboard and Student Reports

- Class and overall averages for recent tests
- Weak areas, incorrect-answer rates, and risk/warning/stable signals
- Individual score trends, category performance, and Top 3/Worst 3 summaries
- Analytics methods reused in monthly reports

The “3-minute dashboard” names the intended pre-class review use, rather than a measured processing time.

[AnalyticsService](./EduPOP/src/main/java/com/example/EduPOP/service/analytics/AnalyticsService.java) · [AnalyticsController](./EduPOP/src/main/java/com/example/EduPOP/controller/analytics/AnalyticsController.java)

## Team Features

| Area | Shared project code |
| --- | --- |
| Tests | Test creation, online attempts, OMR input, PDF text extraction |
| Review | Wrong-answer practice, AI-generated similar questions, vocabulary games |
| Authentication | Session login, BCrypt, role-based URL rules, Kakao/Naver/Google OAuth |
| Academy registration | National Tax Service business-registration verification API |

## Application Structure

~~~mermaid
flowchart TB
    U["Admin · Teacher · Student"] --> V["Thymeleaf View"]
    V --> S["Spring MVC · Spring Security"]
    S --> B["Controller · Service"]
    B --> D["MyBatis · MySQL"]
    B --> X["OAuth · OpenAI · NTS API"]
    B --> P["PDFBox"]
    V --> C["Chart.js"]
~~~

Class, instructor, and student relationships are stored in separate mapping tables. Analytics data is retrieved through Service/Mapper methods and passed to Thymeleaf and JavaScript charts.

## Stack

Java 17 · Spring Boot 4.1.0 · MyBatis Starter 4.0.1 · MySQL · Thymeleaf · JavaScript · Chart.js · Maven.

Class management and analytics use MyBatis. The repository also contains JPA dependencies and report-domain code.

## Run

Prepare JDK 17 and MySQL 8.x. Review `EduPOP/DB/schema.sql` before importing: it contains table definitions and subsequent ALTER statements, including repeated column additions.

Set DB and required external-service values in the runtime environment.

| Environment variables | Purpose |
| --- | --- |
| DB_URL, DB_USERNAME, DB_PASSWORD | MySQL |
| JWT_SECRET | JWT configuration, where used |
| OPENAI_API_KEY | Similar-question generation |
| KAKAO_CLIENT_ID, KAKAO_REDIRECT_URI | Kakao login |
| NAVER_CLIENT_ID, NAVER_CLIENT_SECRET, NAVER_REDIRECT_URI | Naver login |
| GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET, GOOGLE_REDIRECT_URI | Google login |
| NTS_BUSINESS_API_KEY | Business-registration verification |

Check `application.properties` for the integrations required at startup, including Google credentials.

~~~bash
git clone https://github.com/Eunhye-yoo/EduPOP.git
cd EduPOP/EduPOP
./mvnw spring-boot:run
~~~

Windows: `mvnw.cmd spring-boot:run`. Default address: http://localhost:8080.

## Follow-Up Work

Apply academy-ownership checks consistently across update, status, and assignment routes, then test requests from two academies and compare DB changes. The parent-facing report share link also needs completion.

[Code-based access-control review](./docs/access-control-review.en.md) · [Profile](https://github.com/Eunhye-yoo)
