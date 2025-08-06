# Appendix: Risk Identification & Tradeoff Analysis

This document outlines the key risks identified during the platform's design and the strategic tradeoffs made to mitigate them. Each point follows a consistent structure: the decision made, the tradeoff it represents, the associated risk, and the strategy to manage that risk.

### 1. Architecture: Monolith vs. Microservices

- **Decision**: Adopt a service-oriented monolith for the initial implementation.
- **Tradeoff**: Prioritizing development velocity and deployment simplicity over the long-term scalability of a microservices architecture.
- **Risk**: The monolith could become complex and difficult to maintain as the platform scales.
- **Mitigation**: The application is designed with clear service boundaries, allowing for a straightforward future extraction into independent microservices without a complete rewrite.

### 2. User Reporting: Anonymity vs. Data Quality

- **Decision**: Allow citizens to submit reports anonymously.
- **Tradeoff**: Prioritizing higher user participation and trust over the guaranteed data quality that comes from registered-only submissions.
- **Risk**: Anonymous reporting may increase the volume of spam, malicious, or low-quality reports.
- **Mitigation**: A multi-layered moderation system (automated screening + human review) filters invalid reports, ensuring a high signal-to-noise ratio before they reach authorities.

### 3. Reliance on External Data Sources

- **Decision**: Utilize external public data sources for merchant information and jurisdictional boundaries.
- **Tradeoff**: Prioritizing data automation and accuracy over the operational self-reliance of manually curating the data.
- **Risk**: Core functionality, like report routing, depends on external data that may be unreliable, inaccurate, or unavailable.
- **Mitigation**: The system uses asynchronous batch jobs to ingest and cache external data, decoupling it from real-time source availability. A feedback channel allows authorities to report and correct data inaccuracies.

### 4. Moderator Scalability & Bias

- **Decision**: Launch with a centralized human moderation team.
- **Tradeoff**: Prioritizing initial consistency and control over the scalability of a distributed or community-based moderation model.
- **Risk**: The moderation team could become a bottleneck as report volume grows, and centralized decision-making may lead to perceived or actual bias.
- **Mitigation**: The system will track moderator performance analytics to identify bottlenecks. Post-MVP, the roadmap includes exploring AI-assisted review and community moderation to enhance scalability and fairness.
