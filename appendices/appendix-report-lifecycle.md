# Appendix: Report Lifecycle State Diagram

This diagram illustrates the various states a report can transition through, from submission to resolution, including automated screening and human moderation steps.

```mermaid
stateDiagram-v2
    [*] --> Pending: New Report Submitted

    Pending --> Approved: Automated screening passes
    Pending --> Flagged: Automated screening flags for review
    Pending --> Rejected: Automated screening rejects as spam

    Flagged --> Approved: Human moderator approves
    Flagged --> Rejected: Human moderator rejects

    Approved --> Assigned: Matched to an authority
    Assigned --> InProgress: Authority starts investigation
    InProgress --> Resolved: Authority resolves the issue
    InProgress --> NeedsInfo: Authority requests more information
    NeedsInfo --> InProgress: Citizen provides more information

    Resolved --> [*]
    Rejected --> [*]
```