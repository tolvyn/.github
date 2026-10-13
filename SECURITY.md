# Security Policy

## Reporting a vulnerability

Email **security@tolvyn.io**.

Please include enough detail to reproduce the issue: the endpoint or component,
the request or input, what happened, and what you expected. If a proof of
concept is easier than a description, send that.

**Please do not open a public issue for a security report.** Everything else is
welcome as an issue.

## What to expect

- **Acknowledgement** that the report arrived.
- **A verdict** once it has been checked against the code: whether it is a
  defect, whether it is already known, and what is being done. Reports that
  turn out to be duplicates or non-issues are told so, with the reason.
- **Credit**, if you would like it, for a report that is new and valid.

A report that duplicates work already tracked before it arrived is still
answered — it just is not credited as new.

## Scope

In scope: the TOLVYN proxy and API (`api.tolvyn.io`, `proxy.tolvyn.io`), the
dashboards (`app.tolvyn.io`, `admin.tolvyn.io`), and the published SDKs and CLI
listed on the organisation profile.

Out of scope: findings against the AI providers TOLVYN forwards to, and
anything that requires access to a customer's own credentials.

**Please do not test against accounts or data that are not yours.** If you need
an account to demonstrate something, sign up for one — the free tier needs no
card.

## Supported versions

The SDKs and CLI are supported at their latest published version. Fixes ship
forward: a correction is released as a new version rather than by changing a
published one.
