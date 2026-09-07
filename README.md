# Restful-Booker API Test Suite

[![API Tests](https://github.com/mikhailkaneko/postman-restful-booker-api-tests/actions/workflows/api-tests.yml/badge.svg)](https://github.com/mikhailkaneko/postman-restful-booker-api-tests/actions)

An API test suite for [Restful-Booker](https://restful-booker.herokuapp.com), a public REST API built specifically for API testing practice. Built as a **Postman collection** and run from the command line via **Newman**, with the full suite executing automatically on every push and weekly on a schedule via **GitHub Actions**.

## Project Overview

Restful-Booker exposes a simple hotel booking resource behind a token-based authentication endpoint. This project automates authentication and the full CRUD lifecycle of a booking, including chained requests and negative/security scenarios such as attempting writes without a valid auth token.


## Tech Stack

- **Postman** — request collection, test scripts (JavaScript / Chai assertions via `pm.test`)
- **Newman** — CLI runner for executing the Postman collection outside the Postman app
- **GitHub Actions** — CI pipeline that runs the suite on every push, PR, and a weekly schedule
- **Node.js** — runtime for Newman

## Test Coverage

| Area | Scenarios |
|---|---|
| Health check | API availability (`GET /ping`) |
| Authentication | Valid login (token issued), invalid credentials (rejected without a token) |
| Booking - Read | List all bookings, 404 on a non-existent booking id |
| Booking - Create | Create a booking, verify the data was actually persisted (not just echoed) |
| Booking - Update | Reject update without auth (403), authenticated full update (`PUT`), authenticated partial update (`PATCH`) |
| Booking - Delete | Reject delete without auth (403), authenticated delete, verify the booking is gone afterward (404) |

- **13 requests / test scripts** across 6 folders
- Covers **positive and negative** paths, plus **authentication/authorization** checks
- Uses **collection variables** to chain requests
- Вocuments two of the API's non-obvious behaviors: `POST /auth` returns `200` with a `reason` field on bad credentials (not `401`), and `DELETE /booking/:id` returns `201` rather than `200`/`204` — the kind of detail worth flagging in a real bug report or API doc review, not something to silently code around

## Setup Instructions

**Prerequisites:** Node.js 18+

```bash
# 1. Clone the repository
git clone https://github.com/mikhailkaneko/postman-restful-booker-api-tests.git
cd postman-restful-booker-api-tests

# 2. Install dependencies
npm install

# 3. Run the full suite (CLI output + HTML report in report/report.html)
npm test

# Optional: run with a JUnit report (same format used in CI)
npm run test:ci
```

You can also open `collections/restful-booker.postman_collection.json` directly in the Postman app (File → Import) to inspect or run requests interactively, using the paired environment file in `environments/`.

## Project Structure

```
├── collections/       # Postman collection (requests + test scripts)
├── environments/      # Postman environment (base_url variable)
├── .github/workflows/ # CI pipeline definition
└── package.json       # Newman + reporter dependencies, npm scripts
```
