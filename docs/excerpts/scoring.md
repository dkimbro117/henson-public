# Scoring excerpt

Illustrative, abridged excerpts from the private codebase. Not runnable on their own.

## Henson Score

Every catalog destination carries three 0–10 dimension scores in its `safety_knowledge` row. The composite is a fixed, inspectable blend:

```js
/** Composite Henson Score (0–10) for sorting and display. */
export function computeHensonScore(destination) {
  const { americanScore, communitySafetyScore, culturalWelcomeScore } = getDimensionScores(
    destination?.safety_knowledge
  )
  return Number((americanScore * 0.35 + communitySafetyScore * 0.35 + culturalWelcomeScore * 0.3).toFixed(1))
}
```

## Group-aware ranking

When a trip's lived-experience profile includes a Black American tag, cultural welcome is weighted up relative to community safety. Small, explicit adjustments follow for advisories and budget/region fit:

```js
const weights = livedProfiles.includes('black_american')
  ? { american: 0.35, community: 0.3, cultural: 0.35 }
  : { american: 0.35, community: 0.35, cultural: 0.3 }

score += sk.american_score * weights.american
score += sk.community_safety_score * weights.community
score += sk.cultural_welcome_score * weights.cultural

if (!destination.geopolitical_alert) score += 0.5
// ...budget band / region adjustments (+0.3)
```

## Why deterministic

- The same inputs always produce the same ranking, so organizers can see *why* a destination ranks where it does.
- Scores carry source citations, surfaced as a tooltip on each destination's score.
- Claude receives these scores as context; it never produces or overrides them.
