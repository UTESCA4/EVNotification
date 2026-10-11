# Emergency Vehicle Alert System

A mobile and web application project designed to notify drivers about approaching emergency vehicles using location-based alerts, a shared backend, and push notifications.

> **Status:** Planning and prototype scope. This README describes intended behavior and a proposed development workflow; it does not claim that these features are implemented. The planned technology stack is documented below. Implementation versions, the final project name, and release dates are to be confirmed.

## Overview

The project explores how location updates, geospatial processing, and mobile and browser notifications can help drivers become aware of approaching emergency vehicles. The initial proof of concept will use simulated vehicle locations to validate the core alert workflow.

## Goals

- Receive location updates from a simulated emergency vehicle.
- Identify drivers who may be affected by the vehicle's approach.
- Send relevant alerts to those drivers.
- Display driver location and relevant map information.
- Measure alert relevance, delivery latency, and behavior under failure conditions.

## Intended Users

| User | Intended interaction |
| --- | --- |
| Driver | Grants location permission and receives relevant alerts. |
| Authorized emergency vehicle operator | Provides vehicle location updates during an active journey; simulated during the initial PoC. |
| Project administrator or tester | Manages test scenarios, reviews results, and investigates issues. |

## Initial Scope

### Proof of Concept

- Simulate an emergency vehicle moving along a predefined route.
- Submit simulated driver locations to the backend.
- Apply a basic proximity rule to select alert recipients.
- Trigger an alert and record processing and delivery outcomes where observable.
- Test nearby, distant, stale-location, and duplicate-alert scenarios.

### Planned MVP

- User registration and authentication.
- Driver location sharing with permission controls.
- Map interface showing the driver's location.
- Verified emergency vehicle identity and authorized location updates.
- Alert targeting that considers proximity and, where feasible, direction or route overlap.
- Mobile and browser push notifications, alert retrieval, and basic operational monitoring.

### Outside the Initial Scope

- Integration with live emergency dispatch systems.
- Traffic signal control or automatic right-of-way enforcement.
- City-wide production operation.
- Guaranteed notification delivery or proven reductions in emergency response time.

## Proposed Architecture

| Component | Responsibility |
| --- | --- |
| Driver mobile app | React Native, Expo, and TypeScript interface for login, permissions, location updates, maps, and alerts. |
| Web app | React and TypeScript interface using HTML and CSS; retrieves alerts through the shared REST APIs and supports browser notifications. |
| Vehicle simulator or authorized vehicle client | Sends timestamped emergency vehicle location updates. |
| Backend API | Shared Node.js and Express service that validates requests, enforces access permissions, and coordinates application logic. |
| Geospatial and alert logic | Evaluates proximity, location freshness, and relevant route information. |
| Database | Shared PostgreSQL database for required user, vehicle, configuration, and alert records. |
| Authentication | Firebase Authentication for both clients, with the web SDK using the same Firebase project. |
| Notification services | Expo Push Service for mobile delivery through FCM/APNs, and FCM for web browser push. |
| Backend hosting | Google Cloud Run hosts the shared Node.js and Express backend; monitoring configuration remains to be defined. |

### Core Alert Flow

1. A vehicle simulator submits an authenticated, timestamped location update.
2. The backend validates the update and checks location freshness.
3. Alert logic compares the vehicle location with eligible driver locations.
4. The backend creates an alert for matching drivers and suppresses duplicates.
5. The notification service attempts delivery to the selected devices.
6. Available delivery outcomes and application events are recorded for evaluation.

Client-to-backend request/response communication is planned through HTTPS APIs. Any live streaming mechanism will be selected separately. Clients must not access the database directly.

## Technology Stack

The following planned stack reflects the project's technology selection. It does not imply that the services have already been configured or deployed.

| Application part | Mobile application | Web application |
| --- | --- | --- |
| Frontend / UI | React Native + Expo, TypeScript | React + TypeScript, HTML + CSS |
| Backend / logic | Node.js + Express | Same Node.js + Express backend |
| API communication | REST APIs using HTTPS + JSON | Same REST APIs using HTTPS + JSON |
| Database | PostgreSQL | Same PostgreSQL database |
| Authentication | Firebase Authentication | Firebase Authentication web SDK, using the same project |
| Fetching alerts | `GET /api/alerts`; display results on mobile | `GET /api/alerts`; display results in the browser |
| Push notifications | Expo Push Service, FCM for Android, APNs for iOS | FCM for web; HTTPS, notification permission, and a service worker |
| Backend hosting | Google Cloud Run | Same backend deployment |

### Shared Services and Alerts

- Both frontends use the same backend APIs and PostgreSQL database.
- Firebase Authentication provides user identity; the backend must validate authentication and enforce access to application data.
- `GET /api/alerts` is the planned endpoint for retrieving alerts relevant to the authenticated user. The response schema and retrieval frequency remain to be defined.
- Fetching alerts and receiving push notifications are separate flows. The API retrieves alert records; push services deliver notifications to registered devices or browsers.
- Mobile push is planned through Expo Push Service, with FCM delivering to Android devices and APNs delivering to iOS devices.
- Browser push is planned through FCM for web, with HTTPS, user permission, and a service worker.
- Google Cloud Run is selected for backend hosting. PostgreSQL hosting and web frontend hosting are separate decisions still to be confirmed.

### Remaining Decisions

- Map and routing provider, and any PostgreSQL geospatial extension.
- Runtime, framework, and SDK versions.
- Database hosting, web frontend hosting, and monitoring configuration.
- Test frameworks, CI configuration, and notification registration details.
- API schemas, location update frequency, and alert eligibility thresholds.

Source control and collaboration use Git and GitHub.

## Development Roadmap

Dates and owners should be recorded in the project board after team agreement.

| Phase | Deliverables | Completion criteria |
| --- | --- | --- |
| Planning and requirements | Problem statement, users, scope, prioritized backlog | Team agrees on the first demonstration and its acceptance criteria. |
| Design | Architecture, wireframes, data model, API contracts | Interfaces and responsibilities are documented. |
| Proof of concept | Simulated location-to-alert workflow | A repeatable scenario produces an alert for the intended recipient. |
| MVP development | Authentication, mobile UI, location processing, notifications | Agreed core user journeys work together. |
| Testing and evaluation | Functional tests, failure scenarios, latency measurements | Results are documented and release-blocking defects are resolved. |
| Pilot deployment | Test environment, configuration, monitoring, rollback instructions | A controlled test group can access the release. |
| Maintenance | Bug fixes, dependency updates, feedback backlog | Issues are tracked and prioritized for subsequent releases. |

## Getting Started

Executable setup instructions will be added when the repository contains a working implementation. No installation or startup commands have been verified yet.

Before beginning implementation:

1. Obtain access to the project repository and clone it locally.
2. Review the agreed requirements, architecture, and assigned GitHub issue.
3. Confirm the required runtime versions and package manager with the team.
4. Obtain development credentials through the team's approved private channel.
5. Create a branch for the issue you are working on.

Once the application is scaffolded, update this section with dependency installation, environment configuration, local startup, simulator usage, and test commands.

## GitHub Contribution Workflow

The following workflow is proposed for team adoption.

1. **Create or select an issue.** Describe the task, expected behavior, acceptance criteria, and relevant dependencies.
2. **Assign an owner.** Keep responsibility clear and discuss interface changes with affected teammates.
3. **Create a short-lived branch from `main`.** Use names such as `feature/driver-map`, `fix/duplicate-alerts`, or `docs/setup-guide`.
4. **Make focused commits.** Keep changes related to the issue and use descriptive commit messages.
5. **Test the change.** Include relevant automated tests and record any manual verification.
6. **Open a pull request.** Explain the change, link the issue, and include screenshots for UI changes.
7. **Request review.** Resolve feedback and ensure required checks pass before merging.
8. **Merge and close the issue.** Delete the completed branch and update the project board.

### Repository Rules

- Keep `main` stable; use pull requests for changes.
- Require at least one teammate's approval before merging, once branch protections are configured.
- Never commit credentials, API keys, private certificates, or real user location datasets.
- Keep local secret files out of Git; provide an example configuration containing placeholders only.
- Raise blockers early and document changes to APIs or shared data structures.
- Treat work as complete only when implementation, relevant tests, review, and documentation are finished.

## Testing and Evaluation

| Test area | Example checks |
| --- | --- |
| Unit tests | Distance calculations, location freshness, alert eligibility, duplicate suppression |
| Integration tests | API validation, database operations, notification provider interaction |
| End-to-end tests | Simulated vehicle update through to the driver's visible alert |
| Permission and access tests | Denied location permission, expired sessions, unauthorized vehicle updates |
| Failure scenarios | Network loss, stale coordinates, provider errors, disabled notifications |
| Performance evaluation | Backend processing latency, observable end-to-end latency, concurrent updates |

Agree on measurable acceptance thresholds before evaluating the system. Distinguish a notification accepted by a provider from one received or displayed on a device.

## Privacy and Security

- Use simulated data during early development.
- Request location access only when needed and explain its purpose.
- Authenticate clients and authorize emergency vehicle updates.
- Encrypt network communication and restrict access to stored data.
- Define retention and deletion policies before collecting real location history.
- Avoid logging precise locations, credentials, or personal information unnecessarily.

## Project Documentation

Maintain requirements, architecture decisions, API contracts, setup instructions, and evaluation results alongside the code or in the agreed project documentation space. Link those documents here when they are available.

## Limitations

This is an experimental project. Real-world usefulness depends on location accuracy, network connectivity, device permissions, notification delivery, and authorized access to vehicle data. A prototype demonstration does not establish suitability for operational emergency use.

## License

License to be confirmed by the project team and relevant stakeholders. No open-source license is declared in this README.
