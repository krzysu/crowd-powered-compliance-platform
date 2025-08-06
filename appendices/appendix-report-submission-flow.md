# Appendix: Report Submission Flow

This diagram illustrates the sequence of interactions involved when a user submits a report, detailing the flow from initial submission through content screening and authority assignment.

```mermaid
sequenceDiagram
    participant User
    participant API as API Gateway
    participant REP as Report Service
    participant MOD as Moderation Service
    participant AUT as Authority Service

    User->>API: Submit report (location + violation)
    API->>REP: createReport()
    REP->>MOD: screenContent()

    alt Content approved
        MOD->>REP: approved
        REP->>AUT: matchAuthority()
        AUT->>REP: authority assigned
        REP->>API: success
    else Content flagged
        MOD->>REP: flagged for review
        REP->>API: pending moderation
    end

    API->>User: Report created/pending
```