# Crash ServerWatchdog Report 

> **Date:** April 26, 2026 at 08:25:38
> **Version:** Forge 47.4.10 on Minecraft 1.20.1
> **Mods loaded:** ~140

---

## What happened

The server crashed because the `ServerWatchdog` caught a single server tick taking **60 full seconds** to complete — when the normal limit is 50 milliseconds. That's 1,200× slower than it should be. Once the watchdog saw that, it killed the process.

---

## Root cause

The server ran completely out of memory.

The JVM was set up with `-Xmx22G`, but the machine only has **16 GB of physical RAM** and **2 GB of swap**, giving a total of 18 GB. The JVM was basically promised more memory than the system can physically provide.

On top of that, the `AllTheLeaks` mod reported at crash time that objects were being held in memory and never released:

| Leaked type | Count |
|---|---|
| `LevelChunk` | **362** |
| `ServerLevel` | 5 |
| `ServerPlayer` | 2 |

With 140 mods constantly loading and generating chunks, those 362 `LevelChunk` objects kept piling up in the heap. The garbage collector couldn't free them because something was still holding references to them. Over time, this filled up all the physical RAM, then completely maxed out the swap too.

Once swap hit 100%, the OS entered **thrashing** — every time the JVM needed to access an object, instead of reading from RAM (nanoseconds), it had to read from disk (milliseconds). The server's main thread, which is single-threaded and can't move on until each operation finishes, got stuck waiting on disk I/O mid-tick until the watchdog pulled the plug.

---

## Progressive evidence

The crash didn't come out of nowhere. The `Can't keep up!` warnings in the logs show the server had been struggling for hours before the final collapse:

```log
[26Apr2026 04:33:12.747] [Server thread/WARN]: Can't keep up! Is the server overloaded? Running 26932ms or 538 ticks behind
```

These warnings were showing up repeatedly with growing tick counts, which lines up perfectly with a system slowly drowning in memory pressure. The main thread was still alive but getting slower and slower — classic thrashing behavior.

---

## Hardware context

| Component | Spec |
|---|---|
| CPU | Intel Core i5-4570 (2013, Haswell, 4 cores) |
| RAM | 16 GB |
| Swap | 2 GB |
| JVM heap configured | `-Xms16G -Xmx22G` |

For a 140-mod pack, this setup is pretty underpowered. The JVM, the OS, and all 140 mods are competing for the same 4 cores during the tick loop, and the memory config actively works against the server by asking for more than the system has.

---

## What to do about it

**Short term — stop the bleeding:**

Lower the JVM heap to something the machine can actually handle:
```
-Xms8G -Xmx12G
```
This leaves breathing room for the OS and won't fix the chunk leak, but it'll prevent the system from collapsing from OOM every time.

**Medium term — find the leak:**

Use **spark** (already installed on the server) to profile the heap during normal operation and track down which mod is holding onto those 362 chunks. Dimension-adding mods like Twilight Forest or The Aether are reasonable suspects given the `ServerLevel` leaks as well.

```
/spark heapsummary
```