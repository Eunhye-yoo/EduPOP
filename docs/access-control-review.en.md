# EduPOP · Class Data and Access-Control Review

[한국어](./access-control-review.md) · [README](../README.en.md)

Review: 2026-11-01. Static source review; no application/DB attack test run.

## Individual Contribution

Class creation/update/status, multi-instructor/student mapping, pre-class dashboard, and student analytics. History associates class-management changes with Eunhye-yoo: [creation](https://github.com/Eunhye-yoo/EduPOP/commit/f7b5083a10cd2ef95925ed4b2f19118004e1d031), [mapping and synchronization](https://github.com/Eunhye-yoo/EduPOP/commit/ab91c0b9e85dfa215b5cb67489bcb500d51f7317). Commit authorship supports contribution; it does not prove runtime correctness.

## Code-Based Findings

| Path/control | Confirmed implementation | Validation boundary |
| --- | --- | --- |
| POST /classroom/create | Overwrites request academyId from session; @Valid and BindingResult | Cross-academy request test not run; overwriting occurs after model validation |
| GET /classroom/list | Session academy filters list | Runtime filtering not retested |
| GET /classroom/{classId} | Compares session academyId with queried class; redirects on mismatch | Runtime rejection not retested |
| Class update/status/bulk status | Uses classId; reviewed controller/service/SQL lack academy predicate for these writes | Role checks alone do not establish academy ownership |
| addTeacher/addStudent | Duplicate checks; addStudent checks capacity | Referenced user belongs to same academy/role not uniformly checked |
| syncStudents | Capacity check; delete/reassign mappings in transaction | Duplicate input and cross-academy mapping require review |

**Implemented control is not the same as a verified security outcome.** The write-path gaps are source findings; successful unauthorized modification has not been reproduced.

## Request/Data Flow

Controller session/input → ClassroomService transaction and business checks → MyBatis parameter bindings → classes/class_teachers/class_students. Class-name uniqueness is checked within active classes of an academy. Capacity checks differ between single assignment and synchronization; concurrency behavior is unverified.

## Team Features

[SecurityConfig](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/java/com/example/EduPOP/config/SecurityConfig.java) restricts /classroom/** to ADMIN and separates teacher/student URLs. Shared authentication, OAuth, and BCrypt remain team features. URL roles and object-level academy ownership are separate controls.

## Evidence

- [ClassroomController](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/java/com/example/EduPOP/controller/classroom/ClassroomController.java)
- [ClassroomService](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/java/com/example/EduPOP/service/classroom/ClassroomService.java)
- [Create DTO](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/java/com/example/EduPOP/controller/classroom/dto/ClassroomCreateRequest.java) / [Update DTO](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/java/com/example/EduPOP/controller/classroom/dto/ClassroomUpdateRequest.java)
- [SQL mapper](https://github.com/Eunhye-yoo/EduPOP/blob/e17d4ae35658338db5271f8d5fa68724983546d3/EduPOP/src/main/resources/mapper/classroom/ClassroomMapper.xml)

## Next Verification

Use two test academies/admins and students/teachers from each. Test own/other academy IDs across detail/update/status/bulk/assignment/sync routes; compare HTTP response and DB before/after. Verify duplicate IDs, over-capacity, and concurrent assignments separately. Expected denial behavior is a requirement to define and implement, not a result claimed here. Application logic was preserved in this documentation update.
