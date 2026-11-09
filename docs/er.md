# ER-диаграмма

Сгенерировано: `uv run python -m backend.scripts.export_demo_docs`.

## Упрощенная схема для презентации

```mermaid
erDiagram
    USER ||--o| STUDENT : "профиль"
    USER ||--o| TEACHER : "профиль"
    FACULTY ||--o{ DEPARTMENT : "содержит"
    FACULTY ||--o{ STUDY_GROUP : "содержит"
    DEPARTMENT ||--o{ STUDENT : "обучает"
    DEPARTMENT ||--o{ TEACHER : "объединяет"
    STUDY_GROUP ||--o{ STUDENT : "включает"
    ACADEMIC_PERIOD ||--o{ TOPIC : "содержит"
    TEACHER ||--o{ TOPIC : "руководит"
    STUDENT ||--o{ APPLICATION : "подает"
    TOPIC ||--o{ APPLICATION : "получает"
    STUDENT ||--o{ ASSIGNMENT : "имеет"
    TOPIC ||--o{ ASSIGNMENT : "закрепляется"
    TEACHER ||--o{ ASSIGNMENT : "ведет"
    TOPIC ||--o{ TOPIC_DELETION_REQUEST : "имеет запрос"
    USER ||--o{ NOTIFICATION : "получает"

    USER {
        int id
        string email
        string role
    }
    STUDENT {
        int id
        int department_id
        int group_id
    }
    TEACHER {
        int id
        int department_id
        int max_students
    }
    TOPIC {
        int id
        string title
        string status
        string source
    }
    APPLICATION {
        int id
        string status
    }
    ASSIGNMENT {
        int id
        string status
        string current_stage
        int progress_percent
    }
    TOPIC_DELETION_REQUEST {
        int id
        string status
    }
```

Для объяснения на защите достаточно показать цепочку:

```text
Студент → заявка → тема → закрепление → прогресс работы
```

`Application` показывает намерение студента, а `Assignment` — фактическое
закрепление темы за студентом.

```mermaid
erDiagram
    academic_periods {
        biginteger id PK
        string name
        integer academic_year
        enum semester
        date starts_on
        date ends_on
        datetime application_opens_at
        datetime application_closes_at
        enum status
        datetime created_at
        datetime updated_at
    }
    faculties {
        biginteger id PK
        string name
        string short_name
        datetime created_at
        datetime updated_at
    }
    users {
        biginteger id PK
        string last_name
        string first_name
        string middle_name
        string email
        string password_hash
        enum role
        boolean is_active
        datetime last_login_at
        integer failed_login_attempts
        datetime locked_until
        datetime created_at
        datetime updated_at
    }
    departments {
        biginteger id PK
        biginteger faculty_id FK
        string name
        string short_name
        datetime created_at
        datetime updated_at
    }
    notifications {
        biginteger id PK
        biginteger user_id FK
        string kind
        string title
        text body
        json payload
        boolean is_read
        datetime created_at
        datetime read_at
    }
    study_groups {
        biginteger id PK
        biginteger faculty_id FK
        string name
        smallinteger enrollment_year
        datetime created_at
        datetime updated_at
    }
    students {
        biginteger id PK
        biginteger user_id FK
        biginteger group_id FK
        biginteger department_id FK
        string record_book_number
        smallinteger admission_year
        datetime created_at
        datetime updated_at
    }
    teachers {
        biginteger id PK
        biginteger user_id FK
        biginteger department_id FK
        string position
        string academic_degree
        smallinteger max_students
        boolean is_available
        datetime created_at
        datetime updated_at
    }
    topics {
        biginteger id PK
        biginteger period_id FK
        biginteger supervisor_id FK
        biginteger department_id FK
        biginteger created_by_user_id FK
        biginteger proposed_by_student_id FK
        string title
        text description
        enum status
        enum source
        string company_name
        datetime published_at
        datetime archived_at
        enum archive_reason
        datetime created_at
        datetime updated_at
    }
    applications {
        biginteger id PK
        biginteger student_id FK
        biginteger topic_id FK
        enum status
        text motivation
        biginteger decided_by_user_id FK
        datetime decided_at
        text decision_comment
        datetime expires_at
        datetime cooldown_until
        datetime created_at
        datetime updated_at
    }
    assignments {
        biginteger id PK
        biginteger student_id FK
        biginteger topic_id FK
        biginteger supervisor_id FK
        enum status
        smallinteger progress_percent
        enum current_stage
        datetime assigned_at
        string order_number
        date order_date
        text cancel_reason
        datetime created_at
        datetime updated_at
    }
    application_status_history {
        biginteger id PK
        biginteger application_id FK
        enum from_status
        enum to_status
        biginteger changed_by_user_id FK
        text comment
        datetime created_at
    }
    attachments {
        biginteger id PK
        biginteger topic_id FK
        biginteger application_id FK
        string original_name
        string storage_key
        string mime_type
        biginteger size_bytes
        biginteger uploaded_by_user_id FK
        datetime created_at
        datetime updated_at
    }
    topic_deletion_requests {
        biginteger id PK
        biginteger topic_id FK
        biginteger requested_by_user_id FK
        enum status
        text reason
        biginteger decided_by_user_id FK
        datetime decided_at
        text decision_comment
        datetime created_at
        datetime updated_at
    }
    faculties ||--o{ departments : "faculty_id"
    users ||--o{ notifications : "user_id"
    faculties ||--o{ study_groups : "faculty_id"
    users ||--o{ students : "user_id"
    study_groups ||--o{ students : "group_id"
    departments |o--o{ students : "department_id"
    users ||--o{ teachers : "user_id"
    departments ||--o{ teachers : "department_id"
    academic_periods ||--o{ topics : "period_id"
    teachers ||--o{ topics : "supervisor_id"
    departments |o--o{ topics : "department_id"
    users ||--o{ topics : "created_by_user_id"
    students |o--o{ topics : "proposed_by_student_id"
    students ||--o{ applications : "student_id"
    topics ||--o{ applications : "topic_id"
    users |o--o{ applications : "decided_by_user_id"
    students ||--o{ assignments : "student_id"
    topics ||--o{ assignments : "topic_id"
    teachers ||--o{ assignments : "supervisor_id"
    applications ||--o{ application_status_history : "application_id"
    users |o--o{ application_status_history : "changed_by_user_id"
    topics |o--o{ attachments : "topic_id"
    applications |o--o{ attachments : "application_id"
    users |o--o{ attachments : "uploaded_by_user_id"
    topics ||--o{ topic_deletion_requests : "topic_id"
    users ||--o{ topic_deletion_requests : "requested_by_user_id"
    users |o--o{ topic_deletion_requests : "decided_by_user_id"
```

Частичные уникальные индексы обеспечивают одно активное закрепление
на студента, одну активную заявку на тему и одно неотменённое закрепление на тему.

## Ключевая логика связей

- `users` — учетные записи; профили `students` и `teachers` расширяют пользователя.
- `topics` принадлежат учебному периоду, кафедре и научному руководителю.
- `applications` — заявки студентов на темы; `assignments` — итоговые закрепления.
- `application_status_history` хранит историю решений по заявкам.
- `topic_deletion_requests` хранит запрос научрука на удаление темы и решение завкафедры.
- `notifications` связаны с пользователем и сообщают о решениях и изменении прогресса.
- `attachments` могут принадлежать либо теме, либо заявке, но не обоим объектам одновременно.
