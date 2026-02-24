# World Cup 2026 API

A REST API for World Cup 2026 data — all 48 teams, 104 matches, 12 groups, 16 stadiums, live standings, and real-time match events via webhooks.

**Base URL:** `https://api.wc2026api.com`
**Docs (interactive):** `https://api.wc2026api.com/docs`

---

## Authentication

All endpoints require an API key sent as a Bearer token:

```
Authorization: Bearer YOUR_API_KEY
```

Request a key by opening an issue in this repo or contacting the maintainer.

---

## Tiers

| Tier | Rate Limit | Price |
|------|-----------|-------|
| Free | 100 req/day | Free |
| Pro  | 10,000 req/day | Paid |

Both tiers have access to all endpoints. Pro unlocks webhook subscriptions for real-time match events.

---

## Endpoints

### Teams

| Method | Path | Description |
|--------|------|-------------|
| GET | `/teams` | All 48 teams |
| GET | `/teams/:id` | Single team by ID |

**Query params — `/teams`:** none

**Example response — `/teams/:id`:**
```json
{
  "id": 1,
  "name": "United States",
  "code": "USA",
  "flag_url": "https://...",
  "group": "A"
}
```

---

### Groups

| Method | Path | Description |
|--------|------|-------------|
| GET | `/groups` | All 12 groups (A–L) with teams |
| GET | `/groups/:id` | Single group + live standings |

**Example response — `/groups/:id`:**
```json
{
  "id": 1,
  "name": "A",
  "teams": [...],
  "standings": [
    { "team": "USA", "played": 2, "w": 1, "d": 1, "l": 0, "gf": 3, "ga": 1, "gd": 2, "points": 4 }
  ]
}
```

---

### Matches

| Method | Path | Description |
|--------|------|-------------|
| GET | `/matches` | All 104 matches |
| GET | `/matches/:id` | Single match by ID |

**Query params — `/matches`:**

| Param | Description | Example |
|-------|-------------|---------|
| `group` | Filter by group letter | `?group=A` |
| `round` | Filter by round | `?round=group`, `?round=R16`, `?round=final` |
| `status` | Filter by status | `?status=live`, `?status=scheduled`, `?status=completed` |
| `team` | Filter by team code | `?team=BRA` |

**Round values:** `group`, `R32`, `R16`, `QF`, `SF`, `3rd`, `final`

**Example response:**
```json
{
  "id": 1,
  "match_number": 1,
  "round": "group",
  "group": "A",
  "home_team": { "id": 1, "name": "United States", "code": "USA" },
  "away_team": { "id": 2, "name": "Mexico", "code": "MEX" },
  "stadium": { "id": 1, "name": "SoFi Stadium", "city": "Los Angeles" },
  "kickoff_utc": "2026-06-11T20:00:00Z",
  "home_score": null,
  "away_score": null,
  "status": "scheduled"
}
```

---

### Stadiums

| Method | Path | Description |
|--------|------|-------------|
| GET | `/stadiums` | All 16 stadiums |
| GET | `/stadiums/:id` | Single stadium by ID |

**Example response:**
```json
{
  "id": 1,
  "name": "SoFi Stadium",
  "city": "Los Angeles",
  "country": "USA",
  "capacity": 70240
}
```

---

## Webhooks (Pro tier)

Subscribe to real-time match events delivered via signed HTTP POST.

| Method | Path | Description |
|--------|------|-------------|
| GET | `/webhooks` | List your subscriptions |
| POST | `/webhooks` | Create a subscription |
| DELETE | `/webhooks/:id` | Delete a subscription |

**Create subscription — request body:**
```json
{
  "url": "https://your-server.com/hook",
  "events": ["goal", "yellow_card", "red_card", "substitution", "status_update", "score_update"]
}
```

**Webhook payload delivered to your URL:**
```json
{
  "event": "goal",
  "match_id": 42,
  "minute": 67,
  "team_id": 1,
  "description": "Header from a corner",
  "payload": {},
  "timestamp": "2026-06-11T21:07:00Z"
}
```

**Signature verification:**
Each delivery includes an `X-WC2026-Signature: sha256=<hmac>` header. Compute `HMAC-SHA256(body, your_secret)` and compare to verify authenticity.

---

## Tournament Format

- **48 teams**, **12 groups of 4** (A–L)
- Top 2 per group + 8 best third-place teams = **32 teams** advance
- Knockout rounds: R32 → R16 → QF → SF → 3rd place → Final
- **104 total matches** across **16 stadiums** in USA, Canada, and Mexico

---

## Issues & Feedback

Open an issue in this repo to:
- Request an API key
- Report a bug or data issue
- Request a feature

---

## Postman Collection

Import [`wc2026-api.postman_collection.json`](./wc2026-api.postman_collection.json) to get all endpoints pre-configured with environment variables.

Set the `api_key` collection variable to your key before running requests.
