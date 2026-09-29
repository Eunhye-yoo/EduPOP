<p align="center">
  <img src="./EduPOP/src/main/resources/static/images/exp/character.png" alt="EduPOP character" width="150">
</p>

<h1 align="center">EduPOP</h1>

<p align="center"><strong>An education platform that connects academy operations and test results to the next learning cycle</strong></p>

<p align="center">
EduPOP connects test creation, grading, analysis, review, and growth reporting in one service so teachers can prepare the next class with data and students can clearly identify their weak areas.
</p>

<p align="center">
  <a href="./README.ko.md">한국어 README</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Java-17-007396?style=flat-square&logo=openjdk&logoColor=white" alt="Java 17">
  <img src="https://img.shields.io/badge/Spring_Boot-4.1.0-6DB33F?style=flat-square&logo=springboot&logoColor=white" alt="Spring Boot 4.1.0">
  <img src="https://img.shields.io/badge/MyBatis-4.0.1-000000?style=flat-square" alt="MyBatis 4.0.1">
  <img src="https://img.shields.io/badge/MySQL-8.x-4479A1?style=flat-square&logo=mysql&logoColor=white" alt="MySQL">
  <img src="https://img.shields.io/badge/Thymeleaf-3.x-005F0F?style=flat-square&logo=thymeleaf&logoColor=white" alt="Thymeleaf">
  <img src="https://img.shields.io/badge/OpenAI_API-412991?style=flat-square&logo=openai&logoColor=white" alt="OpenAI API">
</p>

## 📌 Project Overview

EduPOP is a team project for private academies serving elementary and middle school students. It connects academy operations and learning activities in a single web service.

### Core Learning Cycle

~~~mermaid
flowchart LR
    A["Academy Operations"] --> B["Test Creation & Taking"]
    B --> C["Grading & Performance Analysis"]
    C --> D["Review · AI Practice · Games"]
    D --> E["Growth Report"]
    E -. "Next Learning Cycle" .-> B
~~~

### Main Features by User

| User | Main Features |
| --- | --- |
| Admin | Academy registration and verification, member approval and management, class management, teacher/student assignment |
| Teacher | Test creation, PDF question extraction, OMR grading, performance analysis, 3-minute dashboard, student reports, reading feedback |
| Student | Online tests, wrong-answer review, daily review, AI-generated similar questions, vocabulary games, reading activities, growth reports |

## 🔎 Key Features

### Test Creation and Grading
- Create tests manually or duplicate existing test templates
- Extract text from PDFs using Apache PDFBox and parse question content
- Manage multiple-choice and short-answer questions with category/subcategory structures
- Save both online test results and teacher-entered OMR results

### Personalized Review
- Provide daily review questions based on recent incorrect answers
- Generate similar questions with the OpenAI API
- Validate generated output for question count, number of options, answer range, and duplication
- Provide vocabulary review in a game format

### Performance Analysis and Reporting
- Visualize test scores and category-level performance using Chart.js
- Analyze performance and weak areas by category and subcategory
- Show score trends and individual strengths/weaknesses
- Aggregate tests, review activity, and reading activity into monthly learning reports

### Authentication and Access Control
- Includes local login and Kakao, Naver, and Google social-login integration code
- Uses Spring Security to separate URL access by admin, teacher, and student roles
- Uses BCrypt for password storage and session-based authentication
- External OAuth features require each provider's credentials and callback configuration

### Academy Registration Verification
- Uses the Korean National Tax Service business-registration verification API
- Verifies business registration number, representative name, opening date, and business status

## 👤 My Contribution

> **I proposed the initial product concept, led key parts of service planning, and implemented features that help teachers turn test results into actionable input for the next class.**

### 1. Product Planning and Data Flow Design
- Proposed the EduPOP concept and helped define the overall service direction
- Designed the user flow connecting test results, analysis, review, and growth reporting
- Designed the information structure from class-level analysis to student-level reports
- Structured analytics logic so it could also be reused in monthly student reports

### 2. Class Management and Student / Multi-Instructor Mapping
- Implemented class creation, update, status management, and teacher/student assignment
- Built an N:M mapping structure to support multiple instructors per class
- Added validation for duplicate student assignment and class capacity
- Organized features with Controller–Service–MyBatis Mapper layers
- Added ownership and soft-delete checks to support safer class operations

### 3. 3-Minute Pre-Class Dashboard
- Compared class average with overall average based on recent tests
- Analyzed weak areas and incorrect-answer rates at class and student levels
- Provided risk / warning / stable signals
- Designed the dashboard so teachers could identify students and topics requiring attention before class

### 4. Student Performance Report and Reusable Analytics Logic
- Visualized score trends and category/subcategory performance across test attempts
- Provided Top 3 strengths and Worst 3 weak areas
- Handled no-data cases with empty or default output
- Separated analytics methods so they could be reused by another team member's monthly report feature

## 🧩 Tech Stack

| Area | Technology |
| --- | --- |
| Backend | Java 17, Spring Boot 4.1.0, Spring MVC, Spring Security |
| Persistence | MyBatis, MySQL |
| Frontend | Thymeleaf, HTML5, CSS3, JavaScript |
| Visualization | Chart.js |
| AI | OpenAI Java SDK |
| Document Processing | Apache PDFBox |
| Authentication | Session, OAuth 2.0 Authorization Code, BCrypt |
| External API | Korean National Tax Service business-registration verification API |
| Build | Maven Wrapper, Lombok |

> Note: Spring Data JPA is also included in the project's pom.xml and some report-domain code uses JPA-related components. However, the main data-access flow and the class-management / analytics features I implemented are primarily based on MyBatis.

## 🏗️ Application Structure

~~~mermaid
flowchart TB
    U["Admin · Teacher · Student"] --> V["Thymeleaf View"]
    V --> S["Spring MVC · Spring Security"]
    S --> B["Controller · Service"]
    B --> D["MyBatis · MySQL"]
    B --> X["OAuth · OpenAI · National Tax Service API"]
    B --> P["PDFBox"]
    V --> C["Chart.js"]
~~~

### Analytics Data Flow

~~~text
Controller
    ↓
AnalyticsService
    ↓
AnalyticsMapper
    ↓
MySQL
    ↓
StudentTrendResponse / ClassWarningResponse
    ↓
Thymeleaf + JavaScript
    ↓
Charts · Comparison Metrics · Risk Signals
~~~

## 💡 Design Decisions

### Reusable Analytics Logic
Instead of calculating the same exam data independently on each screen, analytics logic was separated into reusable Service/Mapper methods so the same results could be reused in other reporting features.

### Class-Level to Student-Level Analysis

~~~text
Class Performance
   ↓
Class Weak Areas
   ↓
Students Requiring Attention
   ↓
Student Weak Areas
   ↓
Individual Performance Analysis
   ↓
Next Learning / Intervention
~~~

### Operational Data Relationships

~~~text
Academy
  └── Class
       ├── Teachers (N:M)
       └── Students (N:M)
~~~

Separate mapping tables were used to support multiple instructors per class and manage student membership safely.

---

## 🎬 Portfolio & Demo

- 🎥 **[EduPOP Demo Video](https://youtu.be/mkAcPCD7VOY)** — Demonstrates the implemented user flow and key features

The portfolio PDF is not currently included in this repository. A link should only be added after the actual file is uploaded.

---

## 🚀 Getting Started

### Requirements
- JDK 17
- MySQL 8.x
- Git
- API keys / OAuth credentials for external integrations

### Clone the Repository

~~~bash
git clone https://github.com/Eunhye-yoo/EduPOP.git
cd EduPOP/EduPOP
~~~

### Database and Configuration

`EduPOP/DB/schema.sql` contains both table definitions and later ALTER statements, including some statements that add columns already declared elsewhere. Therefore, it has **not been verified as a clean database-initialization script**.

Before running the project locally, review `EduPOP/src/main/resources/application.properties` and configure the database connection and required external integration values.

| Environment Variable | Purpose |
| --- | --- |
| OPENAI_API_KEY | AI-generated similar questions |
| KAKAO_CLIENT_ID, KAKAO_REDIRECT_URI | Kakao login |
| NAVER_CLIENT_ID, NAVER_CLIENT_SECRET, NAVER_REDIRECT_URI | Naver login |
| GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET, GOOGLE_REDIRECT_URI | Google login |
| NTS_BUSINESS_API_KEY | Business-registration verification |

Do not commit API keys or client secrets to the repository. Inject them through the runtime environment.

### Run

~~~bash
./mvnw spring-boot:run
~~~

On Windows:

~~~bash
mvnw.cmd spring-boot:run
~~~

If the default port is used, the application is available at `http://localhost:8080`.

### Current Reproduction Scope

The repository includes code for class management, analytics, monthly reports, and related screens/APIs. A fully reproducible clean-database setup has not yet been verified, and some end-to-end paths still require integrated testing.

---

<p align="center"><strong>EduPOP — Turning the end of a test into the start of the next learning cycle</strong></p>
