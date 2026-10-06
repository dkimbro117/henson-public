# Prompt design outline

How the generative layer is structured. This is an outline, not the production prompts.

## Request path

1. The client assembles trip context (destination intelligence, group profile, participant preferences).
2. It sends that context to the API with the user's Supabase JWT.
3. The server validates the JWT and the payload shape, applies per-user and per-IP rate limits, logs a security event, and only then calls Anthropic. API keys never reach the browser.

## Models by job

| Job | Model tier |
|---|---|
| Itineraries, Trip Briefs, destination feed, retrospectives, question triage | Claude Sonnet |
| Concierge chat replies, organizer ping drafts | Claude Haiku |

## Trip Brief prompt shape

- **Role:** a pre-trip intelligence system writing for this specific group.
- **Trip details:** group type and size, departure date, destination, lived-experience filters.
- **Destination intelligence:** cultural context, American traveler context, the three Henson Score dimensions, and any active geopolitical alert.
- **Output contract:** JSON only, with exactly six sections (overview, safety, arrival, cultural intelligence, health, emergency info), each with `category`, `title`, `content`, and optional `tips`. The response is parsed and validated before it's stored.

## Question triage (Concierge)

Participant questions get an immediate chat reply, then a background pass asks the model for a structured `{ answer, confidence }`. Answers with confidence ≥ 0.8 are filed as auto-answered; everything else is queued for the organizer to approve, edit, or escalate. If the model is unavailable, a rule-based fallback still files the question for review.
