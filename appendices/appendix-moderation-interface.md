# Appendix: Human Moderation Interface

This document outlines the design and workflow for the human moderation interface, crucial for ensuring the quality and validity of submitted reports.

## 1. Moderator Dashboard

The Moderator Dashboard is the central hub for 'moderator' role users to efficiently process flagged reports. Key components include:

*   **Report Queue:** A list of reports awaiting review, showing summary information like ID, submission date, flag reason, violation type, and location.
*   **Filters & Sorting:** Tools to prioritize reports by flag reason, violation type, or date.

## 2. Report Review Workflow

When a moderator selects a report, they access a detailed view with three sections:

1.  **Report Content:** Full description, media (photos/videos), and violation type.
2.  **Context & Metadata:** Interactive map of the incident location, reporter's information (if not anonymous), previous reports against the merchant, and the flag reason.
3.  **Moderation Actions:** Buttons to perform actions on the report.

### Moderator Actions

Moderators can choose from:

*   **Approve:** Report is valid and forwarded to the authority; status changes to `verified`.
*   **Reject:** Report is invalid; moderator selects a reason (e.g., "Spam," "Insufficient evidence"); status changes to `rejected`.
*   **Escalate:** For sensitive or uncertain reports, moderators can flag the report for further review by an administrator or senior moderator. This action is recorded as a `manual_flag` in the `ModerationLog`, with the specific reason for escalation noted.

This interface provides moderators with the necessary context for informed, consistent, and efficient decisions, maintaining platform integrity.
