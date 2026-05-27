# API Versioning Strategy

## Current Version

Current API version: v1

Base URL example:

/api/v1/templates

---

## Versioning Method

This project uses URI Versioning.

Examples:

- /api/v1/templates
- /api/v1/history
- /api/v1/direct-send

---

## Backward Compatibility

Minor updates and documentation improvements
must not break existing consumers.

Breaking changes require a new API version.

Example:

- v1 -> compatible updates
- v2 -> breaking changes allowed

---

## Deprecation Policy

Deprecated endpoints will remain available
for at least one semester before removal.

Consumers will receive documentation notice
before endpoint removal.

---

## Contract-First Principle

API contracts must be finalized and reviewed
before implementation.

OpenAPI specification is the source of truth
for Provider and Consumer teams.