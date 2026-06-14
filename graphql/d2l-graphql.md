---
specificationVersion: "0.1.0"
aid: d2l:brightspace-graphql
name: D2L Brightspace GraphQL Schema
description: >
  Conceptual GraphQL schema for the D2L Brightspace Learning Management System,
  derived from the Brightspace Learning Platform (LP) and Learning Environment
  (LE) REST APIs. Covers organizational hierarchy, courses, enrollments, users,
  grades, content, discussions, quizzes, surveys, competencies, and authentication.
url: https://docs.valence.desire2learn.com/
schema: graphql/d2l-schema.graphql
tags:
  - Learning Management System
  - LMS
  - Education Technology
  - GraphQL
  - Courses
  - Grades
  - Users
  - Enrollments
  - Quizzes
  - Discussions
---

# D2L Brightspace GraphQL Schema

This directory contains a conceptual GraphQL schema for the **D2L Brightspace** Learning Management System. The schema is derived from the publicly documented [Brightspace Valence REST API](https://docs.valence.desire2learn.com/) and models the full academic lifecycle supported by the platform.

## Source APIs

| API | Base Path | Description |
|-----|-----------|-------------|
| Learning Platform (LP) | `/d2l/api/lp` | Org units, users, enrollments, roles, permissions |
| Learning Environment (LE) | `/d2l/api/le` | Courses, content, grades, quizzes, discussions, rubrics |
| ePortfolio | `/d2l/api/eP` | Artifacts, reflections, collections, presentations |
| Data Hub (BDS) | `/d2l/api/lp` | Bulk data exports for analytics and reporting |
| IPSIS | `/d2l/api/ipsis` | SIS integration and automated provisioning |

## Schema Overview

The schema file `d2l-schema.graphql` defines **67 named types** across the following domains:

### Scalars
`DateTime`, `JSON`, `URL`

### Enumerations (8)
`OrgUnitTypeCode`, `EnrollmentStatus`, `ContentObjectType`, `GradeObjectType`, `AttemptStatus`, `SubmissionStatus`, `PostStatus`, `QuestionType`, `TokenGrantType`

### Organization & Hierarchy (3)
`Organization`, `OrgUnit`, `OrgUnitType`

### Courses (5)
`CourseTemplate`, `CourseOffering`, `CourseDetails`, `CourseEnrollment`, `Semester`, `Term`

### Groups (3)
`GroupCategory`, `Group`, `GroupSection`

### Content (4)
`ContentModule`, `ContentTopic`, `ContentLink`, `ContentObject`

### Assignments & Dropbox (6)
`DropboxFolder`, `Submission`, `SubmissionFile`, `SubmissionFeedback`, `SubmissionDetails`, `Assignment`, `AssignmentDetails`, `Folder`

### Grades (5)
`GradeObject`, `GradeObjectCategory`, `GradeValue`, `FinalGrade`, `FinalGradeOverride`

### Rubrics (6)
`Rubric`, `RubricDetails`, `Criterion`, `CriterionLevel`, `RubricLevel`, `Score`

### Discussions (6)
`Forum`, `Topic`, `Post`, `PostDetails`, `Thread`, `Discussion`

### Announcements (2)
`NewsItem`, `Announcement`

### Quizzes (7)
`Quiz`, `QuizDetails`, `Question`, `QuestionAnswer`, `Attempt`, `AttemptDetails`, `QuestionResponse`

### Surveys (2)
`Survey`, `SurveyDetails`

### Competencies (2)
`Competency`, `LearningObjective`

### Users & Auth (7)
`User`, `UserDetails`, `UserActivation`, `SocialMediaLink`, `Enrollment`, `Role`, `Permission`

### Storage & UI (2)
`Locker`, `Widget`, `HomePage`

### Integrations & Auth (4)
`LTI`, `OAuth`, `Token`, `APIKey`

### Root Types (2)
`Query`, `Mutation`

## Authentication

The Brightspace API uses **OAuth 2.0** for authentication. The schema models the OAuth flow via the `OAuth`, `Token`, and `APIKey` types. Clients must obtain an access token using one of the supported grant types before querying the API.

## Key Relationships

```
Organization
  └── OrgUnit (hierarchical tree)
        ├── CourseOffering
        │     ├── CourseEnrollment → User × Role
        │     ├── ContentModule → ContentTopic
        │     ├── DropboxFolder → Submission
        │     ├── GradeObject → GradeValue
        │     ├── Forum → Topic → Post
        │     ├── Quiz → Question / Attempt
        │     ├── Survey
        │     ├── GroupCategory → Group
        │     └── NewsItem
        └── Semester / Term
```

## Usage Notes

- This is a **conceptual schema** — D2L does not currently expose a native GraphQL endpoint. The schema can be used as a basis for a GraphQL gateway layer over the REST APIs.
- All field names follow camelCase GraphQL conventions; underlying REST API uses both camelCase and PascalCase depending on the endpoint.
- Pagination is omitted for brevity; a production implementation should add `Connection`/`Edge` relay-style pagination or offset-based `PagedResultSet` wrappers.
- The `orgUnitId` argument is required on most queries, mirroring the Brightspace REST API's org-unit-scoped resource paths.
