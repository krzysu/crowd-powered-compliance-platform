# Appendix: Data Model

This appendix provides the detailed SQL schema for the core entities of the Crowd-Powered Compliance Platform.

### Users

Supports anonymous reporting; registered users have roles (citizen, moderator, authority, admin).

```sql
CREATE TABLE "Users" (
  "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "email" VARCHAR(255) UNIQUE,
  "password_hash" VARCHAR(255) NOT NULL,
  "role" VARCHAR(50) NOT NULL CHECK(role IN ('citizen', 'moderator', 'authority', 'admin')),
  "is_verified" BOOLEAN DEFAULT false,
  "organization_id" UUID REFERENCES "Authorities"("id"),
  "created_at" TIMESTAMP DEFAULT NOW(),
  "last_login" TIMESTAMP
);
```

### Reports

Links violations to merchants and locations; tracks status and assignment.

```sql
CREATE TABLE "Reports" (
  "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "user_id" UUID REFERENCES "Users"("id"), -- NULL for anonymous reports
  "anonymous_contact" VARCHAR(255), -- Email/phone for anonymous users
  "merchant_id" UUID REFERENCES "Merchants"("id"),
  "violation_type" VARCHAR(100) NOT NULL,
  "description" TEXT NOT NULL,
  "media_urls" TEXT[], -- Array of photo/video URLs from S3
  "location" GEOGRAPHY(POINT, 4326) NOT NULL,
  "status" VARCHAR(50) DEFAULT 'submitted' CHECK(status IN ('submitted', 'under_review', 'verified', 'rejected', 'resolved')),
  "assigned_authority_id" UUID REFERENCES "Authorities"("id"),
  "created_at" TIMESTAMP DEFAULT NOW(),
  "updated_at" TIMESTAMP DEFAULT NOW()
);
```

### Authorities

Defines jurisdiction boundaries and handled violation types.

```sql
CREATE TABLE "Authorities" (
  "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "name" VARCHAR(255) NOT NULL,
  "jurisdiction" GEOGRAPHY(POLYGON, 4326) NOT NULL,
  "violation_types" TEXT[] NOT NULL, -- e.g., ['packaging', 'pricing', 'health']
  "contact_email" VARCHAR(255) NOT NULL,
  "phone" VARCHAR(50),
  "is_active" BOOLEAN DEFAULT true,
  "created_at" TIMESTAMP DEFAULT NOW()
);
```

### Merchants

Contains business data with location for proximity matching.

```sql
CREATE TABLE "Merchants" (
  "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "name" VARCHAR(255) NOT NULL,
  "address" TEXT,
  "location" GEOGRAPHY(POINT, 4326),
  "business_registry_id" VARCHAR(100) UNIQUE, -- External ID from business registry
  "category" VARCHAR(100), -- e.g., 'restaurant', 'retail', 'services'
  "phone" VARCHAR(50),
  "email" VARCHAR(255),
  "last_updated" TIMESTAMP DEFAULT NOW()
);
```

### ModerationLog

Tracks all moderation decisions and automated screening actions.

```sql
CREATE TABLE "ModerationLog" (
  "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "report_id" UUID REFERENCES "Reports"("id") NOT NULL,
  "moderator_id" UUID REFERENCES "Users"("id"), -- NULL for automated screening
  "action" VARCHAR(50) NOT NULL CHECK(action IN ('auto_approve', 'auto_flag', 'manual_approve', 'manual_reject', 'manual_flag')),
  "reason" TEXT,
  "previous_status" VARCHAR(50),
  "new_status" VARCHAR(50),
  "created_at" TIMESTAMP DEFAULT NOW()
);
```

## Entity-Relationship Diagram

```mermaid
erDiagram
    USERS {
        UUID id PK
        VARCHAR email
        VARCHAR password_hash
        VARCHAR role
        BOOLEAN is_verified
        UUID organization_id FK
        TIMESTAMP created_at
        TIMESTAMP last_login
    }

    REPORTS {
        UUID id PK
        UUID user_id FK
        VARCHAR anonymous_contact
        UUID merchant_id FK
        VARCHAR violation_type
        TEXT description
        TEXT_ARRAY media_urls
        GEOGRAPHY_POINT location
        VARCHAR status
        UUID assigned_authority_id FK
        TIMESTAMP created_at
        TIMESTAMP updated_at
    }

    AUTHORITIES {
        UUID id PK
        VARCHAR name
        GEOGRAPHY_POLYGON jurisdiction
        TEXT_ARRAY violation_types
        VARCHAR contact_email
        VARCHAR phone
        BOOLEAN is_active
        TIMESTAMP created_at
    }

    MERCHANTS {
        UUID id PK
        VARCHAR name
        TEXT address
        GEOGRAPHY_POINT location
        VARCHAR business_registry_id
        VARCHAR category
        VARCHAR phone
        VARCHAR email
        TIMESTAMP last_updated
    }

    MODERATION_LOGS {
        UUID id PK
        UUID report_id FK
        UUID moderator_id FK
        VARCHAR action
        TEXT reason
        VARCHAR previous_status
        VARCHAR new_status
        TIMESTAMP created_at
    }

    USERS ||--o{ REPORTS : submits
    AUTHORITIES ||--o{ REPORTS : assigned_to
    MERCHANTS ||--o{ REPORTS : related_to
    REPORTS ||--|{ MODERATION_LOGS : has
    USERS ||--o{ MODERATION_LOGS : performed_by
```
