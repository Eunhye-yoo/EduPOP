# EduPOP · 반 데이터·접근통제 검토

[English](./access-control-review.en.md) · [README](../README.md)

검토일 2026-11-01. 소스 정적 검토이며 앱·DB 공격 시험 미실행.

## 본인 기여

반 생성·수정·상태, 다중 강사·학생 매핑, 수업 전 대시보드, 개인 성적 분석. 반 관리 변경의 Eunhye-yoo 커밋 이력 확인: [반 생성](https://github.com/Eunhye-yoo/EduPOP/commit/f7b5083a10cd2ef95925ed4b2f19118004e1d031) · [배정·동기화](https://github.com/Eunhye-yoo/EduPOP/commit/ab91c0b9e85dfa215b5cb67489bcb500d51f7317). 작성자 이력은 기여 근거이며 실행 정확성의 증거와 구분.

## 코드 기준 확인

| 경로·통제 | 확인한 구현 | 검증 경계 |
| --- | --- | --- |
| POST /classroom/create | 세션 academyId로 요청값 덮어쓰기, @Valid·BindingResult | 타 학원 요청 시험 미실행. 덮어쓰기는 Model 검증 이후 수행 |
| GET /classroom/list | 세션 학원 기준 목록 조회 | 실제 필터링 재시험 없음 |
| GET /classroom/{classId} | 조회한 반과 세션의 academyId 비교, 불일치 시 목록 Redirect | 실행 차단 재시험 없음 |
| 반 수정·단일/일괄 상태 변경 | classId 사용. 검토한 Controller·Service·SQL 쓰기 경로에 학원 조건 없음 | 역할 검사만으로 학원 소유권 보장 불가 |
| addTeacher·addStudent | 중복 검사, addStudent 정원 검사 | 참조한 사용자의 동일 학원·역할 검사가 일관되게 적용된 것은 아님 |
| syncStudents | 정원 검사 후 트랜잭션에서 매핑 삭제·재배정 | 중복 ID·타 학원 배정 별도 검토 필요 |

**통제 코드의 존재와 보안 시험 성공을 구분.** 쓰기 경로의 검사 차이는 소스 검토 결과이며, 실제 무단 변경 성공을 재현한 것은 아님.

## 요청·데이터 흐름

Controller의 세션·입력 → ClassroomService의 트랜잭션·업무 검사 → MyBatis 파라미터 바인딩 → classes/class_teachers/class_students. 활성 반 이름의 학원 내 중복 검사. 단일 학생 배정과 동기화의 정원 검사 방식 차이 확인. 동시 요청 결과 미검증.

## 팀 공통 기능

[SecurityConfig](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/java/com/example/EduPOP/config/SecurityConfig.java)의 /classroom/** ADMIN 제한, 교사·학생 URL 분리 확인. 공통 인증·OAuth·BCrypt는 팀 기능. URL 역할과 개별 반의 학원 소유권은 별도 통제.

## Evidence

- [ClassroomController](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/java/com/example/EduPOP/controller/classroom/ClassroomController.java)
- [ClassroomService](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/java/com/example/EduPOP/service/classroom/ClassroomService.java)
- [Create DTO](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/java/com/example/EduPOP/controller/classroom/dto/ClassroomCreateRequest.java) / [Update DTO](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/java/com/example/EduPOP/controller/classroom/dto/ClassroomUpdateRequest.java)
- [SQL Mapper](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/resources/mapper/classroom/ClassroomMapper.xml)

## 후속 검증

학원 2개·관리자 2명·각 학원의 강사·학생으로 본인/타 학원 ID를 상세·수정·상태·일괄·배정·동기화 경로별 시험. HTTP 응답과 DB 변경 전후 대조. 중복 ID·정원 초과·동시 배정 별도 확인. 거절 방식은 앞으로 정의·구현할 요구사항이며 완료 결과로 기재하지 않음. 이번 개편에서 앱 로직 보존.
