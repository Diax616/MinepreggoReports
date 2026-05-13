# NullPointerException — Missing Pregnancy System on `PregnancyAcceleration`

> **Server:** Forge 1.20.1 · Minepreggo 1.3.1 beta  
> **Crash time:** 2026-04-26 18:41:14  
> **Trigger:** `ThrownPotion` entity tick (splash potion landing on a player)

---

## The Error

```
java.lang.NullPointerException: Cannot invoke
"dev.dixmk.minepreggo.world.entity.player.AbstractPlayerPregnancySystem.tryStartLabor()"
because "system" is null
    at PregnancyAcceleration.java:69
```

A splash potion with the `PregnancyAcceleration` effect landed on a player. The effect's code fetched the player's pregnancy `system` object via a `LazyOptional` and called `tryStartLabor()` on it — but `system` was `null`. The JVM threw a `NullPointerException` during the entity tick, which propagated up through `ServerLevel` and crashed the entire server.

---

## Why `system` Was Null — Two Known Causes

The pregnancy `system` object being null means that, from Minepreggo's perspective, **the player doesn't have an initialized pregnancy system** despite already having the `Pregnancy P0-8` effect active. There are two ways this can happen:

### Corrupted state from tick starvation
If the server suffered tick starvation severe enough to skip ticks during the initialization countdown (counter never reaching 2400), the pregnancy system was never created in memory.

### External effect removal by another mod
Any mod that strips all potion effects from a player (e.g. a cleansing mechanic, death handling, or a potion interaction) could silently remove the `Pregnancy P0-8` effect. Minepreggo depends on that effect being present to keep the system alive or to validate it on the next access, removing it externally leaves the `system` reference dangling as null while the player's data on disk still expects it to exist. The next time any Minepreggo code touches `system`, it crashes.

---

## Why This Is an External Problem

Forge's `suspected mod` field points directly at Minepreggo, but the root cause in both scenarios is **external**: either the server's tick loop failed to give Minepreggo enough ticks to initialize (infrastructure problem), or another mod modified the player's effect list in a way Minepreggo didn't anticipate (inter-mod interference). The crash itself is just Minepreggo not guarding against a state it wasn't designed to handle.

---

## Fix

The immediate fix on Minepreggo's side is a simple null check before calling `tryStartLabor()`:

```java
// PregnancyAcceleration.java:69 — current (crashes)
system.tryStartLabor();

// Fixed
if (system != null) {
    system.tryStartLabor();
}
```

This makes the effect a no-op when the system isn't initialized, instead of crashing the entire server. Addressing the root causes (tick starvation or external effect removal) requires the solutions described in the tick starvation report.