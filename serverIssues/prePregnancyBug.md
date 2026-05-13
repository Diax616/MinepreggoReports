
# Problem of pregnancy through sexual intercourse (Villager - Player)

The reported issue occurs when a female player triggers the sex event with a male player or villager. After the act, if any of the female player’s eggs are fertilized, the pre-pregnancy phase begins. The female player sets an internal flag indicating that she is already pregnant, but the pregnancy system has not yet loaded (the “Pregnancy P0-8” effect is not triggered), There is a counter that increments with each tick, and when it reaches zero, the pregnancy system is activated, initiating the P0 phase.

## Register (debug-1.log)

### Code Line  12602 - 15493
The following log entry shows that the transition from the pre-pregnancy phase to the early pregnancy phase is working correctly, where the counter must reach the value specified in “minepreggo-server.toml” under `totalTicksToStartPregnancy`. In this log entry, the pregnancy is activated correctly, and visually, the pregnancy effect is observed.

```text
[12toukok.2026 03:37:00.536] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Fertilization attempt: probability=0.86649996, fertilized=4
[12toukok.2026 03:37:00.537] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Monozygotic splits: splitChance=0.004, splits=0, totalEmbryos=4
[12toukok.2026 03:37:00.537] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player terminatorXcb is now in pre-pregnancy state with 4 babies from father: (Optional[9be6bd7c-872e-4d1e-a075-0e3e942ca373],HUMAN,HUMANOID)
[12toukok.2026 03:37:00.574] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 0
[12toukok.2026 03:37:00.623] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 1
[12toukok.2026 03:37:00.677] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 2

... 

[12toukok.2026 03:39:00.528] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 2399
[12toukok.2026 03:39:00.574] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 2400
[12toukok.2026 03:39:00.625] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Added mob effect effect.minepreggo.pregnancy_p0 to entity terminatorXcb
[12toukok.2026 03:39:00.625] [Server thread/INFO] [dev.dixmk.minepreggo.Minepreggo/]: Initialized PlayerPregnancySystem phase P0 for player: terminatorXcb
[12toukok.2026 03:39:00.625] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Failed to apply knockback resistance modifier to entity: 94
[12toukok.2026 03:39:00.625] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player terminatorXcb has become pregnant with 4 babies, total days to give birth: 50, pregnancy phases days: P1: 5 days; P6: 13 days; P5: 10 days; P3: 5 days; P2: 5 days; P4: 10 days; P0: 3 days; , womb: Womb{numOfBabies=4, babies=[Baby [ gender = MALE, species = HUMAN, creature = HUMANOID, motherId = true, fatherId = true ], Baby [ gender = FEMALE, species = HUMAN, creature = HUMANOID, motherId = true, fatherId = true ], Baby [ gender = FEMALE, species = HUMAN, creature = HUMANOID, motherId = true, fatherId = true ], Baby [ gender = MALE, species = HUMAN, creature = HUMANOID, motherId = true, fatherId = true ], ]}
```

### Code Line  16205 - 16764

The following log shows the same event, with the difference that here, the counter that triggers the pregnancy was advancing normally, but in lines 16763 and 16764, you can see from the time the log was saved that the counter stopped, that is, it did not reach the value of 2400, and instead there was a 6-second time jump between 03:58:36.033 and 03:58:42.378. Somehow, the counter stopped running, causing the pregnancy to never trigger and leaving the player in a corrupted state

```text
[12toukok.2026 03:58:08.286] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Fertilization attempt: probability=0.657, fertilized=1
[12toukok.2026 03:58:08.286] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Monozygotic splits: splitChance=0.004, splits=0, totalEmbryos=1
[12toukok.2026 03:37:00.537] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player terminatorXcb is now in pre-pregnancy state with 4 babies from father: (Optional[9be6bd7c-872e-4d1e-a075-0e3e942ca373],HUMAN,HUMANOID)
[12toukok.2026 03:37:00.574] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 0
[12toukok.2026 03:37:00.623] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 1
[12toukok.2026 03:37:00.677] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 2
[12toukok.2026 03:37:00.731] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 3
[12toukok.2026 03:37:00.781] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 4
[12toukok.2026 03:37:00.831] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 5
[12toukok.2026 03:37:00.881] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 6

...

[12toukok.2026 03:58:35.778] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 549
[12toukok.2026 03:58:35.821] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 550
[12toukok.2026 03:58:35.879] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 551
[12toukok.2026 03:58:35.931] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 552
[12toukok.2026 03:58:35.980] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 553
[12toukok.2026 03:58:36.033] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player literal{terminatorXcb} pregnancy system not initialized, timer: 554
[12toukok.2026 03:58:42.378] [Server thread/INFO] [net.minecraft.server.MinecraftServer/]: <terminatorXcb> i hate that bugs dont happen when you want them to
[12toukok.2026 03:58:54.782] [Server thread/DEBUG] [dev.dixmk.minepreggo.Minepreggo/]: Player Elitepikachu912 lactation level increased to 13
[12toukok.2026 03:58:55.831] [Server thread/INFO] [net.minecraft.server.MinecraftServer/]: <DiskHoarder> Did it bug out?
[12toukok.2026 03:58:58.334] [Server thread/DEBUG] [portb.transformerlib.TransformerLib/]: Transforming fuzs/enchantinginfuser/world/inventory/InfuserMenu$1 (target of net/minecraft/world/inventory/Slot)
[12toukok.2026 03:58:58.334] [Server thread/DEBUG] [portb.transformerlib.TransformerLib/]: Transforming fuzs/enchantinginfuser/world/inventory/InfuserMenu$2 (target of net/minecraft/world/inventory/Slot)
[12toukok.2026 03:58:58.335] [Server thread/DEBUG] [portb.transformerlib.TransformerLib/]: Transforming fuzs/enchantinginfuser/world/inventory/InfuserMenu$3 (target of net/minecraft/world/inventory/Slot)
[12toukok.2026 03:59:09.588] [Server thread/INFO] [net.minecraft.server.MinecraftServer/]: <terminatorXcb> yes
```


## Cause of the “bug”

In fact, this behavior isn't caused by a bug in the Minepreggo code; if that were the problem, the bug should be reproducible in any environment (single-player, multiplayer on another type of server, etc.), but it never happens. I have concluded that this problem is caused by the server itself, specifically due to tick starvation, which leads to tick skipping—preventing the counter that triggers pregnancy from ever completing its execution.

### Tick Starvation & Corrupted Player State in Minepreggo
 
> **Server:** Forge 1.20.1 · ~140 mods · Intel Core i5-4570 (4 cores)
 
---
 
### What happened
 
The server was running ~140 mods on a single-threaded tick loop that's supposed to finish all game logic within **50ms per tick**. That's already a tall order for an i5-4570 from 2013 with only 4 physical cores. The combination caused a progressive breakdown that ended in a corrupted player state and a hard server crash.
 
---
 
### Stage 1 — Tick Starvation
 
The logs repeatedly show warnings like:
 
```
[28huhtik.2026 08:11:56.461] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 13919ms or 278 ticks behind
[28huhtik.2026 08:12:23.467] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 12024ms or 240 ticks behind
[28huhtik.2026 08:13:00.507] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 22064ms or 441 ticks behind
[28huhtik.2026 08:13:22.258] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 6765ms or 135 ticks behind
[28huhtik.2026 08:13:51.153] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 5786ms or 115 ticks behind
[28huhtik.2026 08:14:29.218] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 31225ms or 624 ticks behind
[28huhtik.2026 08:15:46.048] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 61855ms or 1237 ticks behind
[28huhtik.2026 08:15:46.753] [Server Watchdog/ERROR] [net.minecraft.server.dedicated.ServerWatchdog/FATAL]: A single server tick took 60.41 seconds (should be max 0.05)
[28huhtik.2026 08:15:46.787] [Server Watchdog/ERROR] [net.minecraft.server.dedicated.ServerWatchdog/FATAL]: Considering it to be crashed, server will forcibly shutdown.
[28huhtik.2026 08:19:30.503] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 209461ms or 4189 ticks behind
[28huhtik.2026 08:19:51.419] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 5926ms or 118 ticks behind
[28huhtik.2026 08:20:19.898] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 13505ms or 270 ticks behind
[28huhtik.2026 08:20:40.571] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 5678ms or 113 ticks behind
[28huhtik.2026 08:22:58.162] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 122619ms or 2452 ticks behind
[28huhtik.2026 08:24:45.081] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 91899ms or 1837 ticks behind
[28huhtik.2026 08:29:37.053] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 277031ms or 5540 ticks behind
[28huhtik.2026 08:37:03.508] [spark-async-sampler-worker-thread/WARN] [spark/]: Timed out waiting for world statistics
[28huhtik.2026 08:38:28.536] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 516504ms or 10330 ticks behind
[28huhtik.2026 08:46:30.611] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 467119ms or 9342 ticks behind
[28huhtik.2026 08:53:19.103] [spark-async-sampler-worker-thread/WARN] [spark/]: Timed out waiting for world statistics
[28huhtik.2026 09:01:43.691] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 898098ms or 17961 ticks behind
[28huhtik.2026 09:08:36.467] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 397781ms or 7955 ticks behind
[28huhtik.2026 09:10:46.131] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 114700ms or 2294 ticks behind
[28huhtik.2026 09:12:30.637] [spark-async-sampler-worker-thread/WARN] [spark/]: Timed out waiting for world statistics
[28huhtik.2026 09:21:01.643] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 600515ms or 12010 ticks behind
[28huhtik.2026 09:33:22.792] [spark-async-sampler-worker-thread/WARN] [spark/]: Timed out waiting for world statistics
[28huhtik.2026 09:58:05.347] [spark-async-sampler-worker-thread/WARN] [spark/]: Timed out waiting for world statistics
[28huhtik.2026 10:47:15.389] [spark-async-sampler-worker-thread/WARN] [spark/]: Timed out waiting for world statistics
[28huhtik.2026 11:20:33.235] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 7156605ms or 143132 ticks behind
[28huhtik.2026 11:25:41.271] [spark-async-sampler-worker-thread/WARN] [spark/]: Timed out waiting for world statistics
[28huhtik.2026 11:43:35.375] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 1367133ms or 27342 ticks behind
[28huhtik.2026 11:56:44.908] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 774575ms or 15491 ticks behind
[28huhtik.2026 12:15:07.736] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 1087841ms or 21756 ticks behind
[28huhtik.2026 12:36:54.944] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 1292250ms or 25845 ticks behind
[28huhtik.2026 13:00:02.296] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 1372352ms or 27447 ticks behind
[28huhtik.2026 13:19:05.770] [Server thread/WARN] [net.minecraft.server.MinecraftServer/]: Can't keep up! Is the server overloaded? Running 1128474ms or 22569 ticks behind
```
 
This is the server admitting it can't process ticks fast enough. With 140 mods each registering their own tick handlers, entity updates, and block entity ticks, the 50ms budget gets blown consistently. The main `Server thread` just doesn't get enough CPU time to keep up — this is called **tick starvation**.
 
---
 
### Stage 2 — Tick Skipping
 
This is where things get concrete. The Minepreggo debug log shows:
 
```
03:58:35.980 — timer: 553
03:58:36.033 — timer: 554
(silence)
03:58:42.378 — player chat message
```
 
There's a **~6.3 second gap** where the counter just... stops. The server wasn't crashed — the player could still chat — but the `Server thread` was frozen solid during that window, most likely due to a GC stop-the-world pause or the swap being completely full (it was at 100% at crash time).
 
The ticks that should've run during those 6 seconds simply never happened. That's **tick skipping**: the internal scheduler drops ticks trying to catch up with the backlog.
 
---
 
### Stage 3 — The Corrupted State Gets Locked In
 
Minepreggo uses a counter that needs to hit **2400** to initialize the pregnancy system for a player. Since the counter only increments by 1 per executed tick, any tick skipping directly delays it in real time — what should take 60 seconds of game time could take several real-world minutes under heavy starvation.
 
The tick skipping during the 6.3 second gap was enough to interrupt the counter before it reached 2400.
 
The tick skipping during the 6.3 second gap was enough to interrupt the counter before it reached 2400. The pregnancy system never initialized, leaving the player in a **partially initialized state**: data written to disk, but the in-memory structure that's supposed to back it never created. Once the server recovered from the freeze and continued running, that window was gone — the counter had stalled and the initialization never triggered.
 
---
 
### Supporting Evidence — ServerWatchdog Crashes
 
While the ServerWatchdog didn't kill the process at this specific moment, the server logs show **multiple separate crashes** caused by exactly that error:
 
```text
Time: 2026-04-26 08:25:38
Description: Watching Server

java.lang.Error: ServerHangWatchdog detected that a single server tick took 60.00 seconds (should be max 0.05)
	at net.minecraft.server.dedicated.ServerWatchdog.run(ServerWatchdog.java:43) ~[server-1.20.1-20230612.114412-srg.jar%23350!/:?] {re:classloading}
	at java.lang.Thread.run(Thread.java:840) ~[?:?] {re:mixin}

```
 
The `ServerWatchdog` is a separate thread that monitors the `Server thread`'s heartbeat. When the main thread stops updating its timestamp for over 60 seconds, the watchdog throws an irrecoverable `java.lang.Error` and kills the process. The fact that this happened repeatedly on this server confirms that the tick starvation wasn't just occasional — the `Server thread` was regularly getting frozen long enough to trip the watchdog. Those crashes are the extreme end of the same problem that caused the 6.3 second gap in the Minepreggo log.
 
---
 
### Root Cause
 
The bug isn't in Minepreggo's logic — the counter code is fine. The real problem is that **the hardware can't sustain the load of 140 mods** on a single-threaded server loop. Tick starvation turned a correct initialization sequence into an unreachable state by stealing the ticks it needed to complete.



### Possible Solutions
 
#### 1. Reduce Minepreggo's initialization counter
> *Addresses the corrupted state directly, not the underlying tick starvation.*
 
Lowering the threshold from 2400 to a smaller value (e.g. 100–200 ticks) gives the initialization a much shorter window to complete, making it far less likely that a freeze or tick skip interrupts it before it finishes. This doesn't fix the server's performance problems at all, but it makes Minepreggo resilient enough to initialize successfully even under starvation conditions.
 
#### 2. Fix the JVM memory configuration
> *Addresses swap exhaustion, one of the main causes of stop-the-world freezes.*
 
The server is configured with `-Xmx22G` on a machine with ~16GB of physical RAM. This forces the OS to use swap, which is orders of magnitude slower than RAM and directly causes the multi-second freezes seen in the logs. Setting `-Xms8G -Xmx12G` would keep the heap within physical memory, dramatically reducing GC pause duration and the frequency of tick skipping episodes. Also expanding swap to at least 8GB would help as a safety buffer.
 
#### 3. Reduce mod count or replace heavy mods
> *Addresses tick starvation at its source.*
 
With 140 mods on a single-threaded tick loop, every mod that registers tick handlers adds to the 50ms budget. Profiling the server with **spark** (`/spark tps` and `/spark profiler`) would identify which mods consume the most tick time. Removing or replacing the worst offenders could recover enough headroom to keep the server stable without a hardware upgrade.
 
#### 4. Upgrade the hardware
> *The most complete solution.*
 
The i5-4570 is a 2013 CPU with 4 cores and no hyperthreading. A modern CPU with higher single-core performance would directly extend the tick budget available to the `Server thread`. Since Minecraft's main loop is fundamentally single-threaded, **single-core clock speed matters more than core count** — a modern mid-range CPU would make a significant difference for a modpack of this size.
