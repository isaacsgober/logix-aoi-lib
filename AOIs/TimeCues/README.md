# TimeCues

One-scan cues when an elapsed time passes fixed points from 10 ms to 10 s, for timed sequences and test timelines.

## Parameters

| Name | Usage | Type | Description |
|---|---|---|---|
| Elapsed | Input | DINT | Elapsed time in ms, such as a timer's ACC. Restart it at 0 for each new timeline. |
| T10 … T10000 | Output | BOOL | On for one call when Elapsed passes that many ms. |

Cue points (ms):

| Range | Cues |
|---|---|
| 10–100 | every 10 |
| 100–1000 | 120, 150, 200, 250, 300, 400, 500, 750, 1000 |
| 1000–5000 | 1500, then every 500 to 5000 |
| 5000–10000 | every 1000 |

## Behavior

- Each cue is on for exactly one call: the first call where Elapsed is at or past its value and the previous call's Elapsed was below it. The AOI stores the previous value internally.
- A cue fires on the first scan at or past its time, so it can be up to one scan late.
- If Elapsed passes several cue points in one scan, all of them fire on that scan.
- When Elapsed returns to 0 and counts up again, every cue fires again on the new timeline.

## Usage

Call it every scan, unconditionally, so it sees Elapsed return to 0 between timelines. With a conditional call, the stored value can stay high, and a cue early in the next timeline can be missed.

```
TimeCues(Cue, StepTimer.ACC);
IF Cue.T500 THEN
    // action at 500 ms
END_IF;
```

If Elapsed comes from a timer, set its PRE above the last cue you use. The ACC stops at PRE, so cues past it never fire.

## Verification

Used as the clock for `Tests/TwoPosActuator`, which exercises T20, T30, T40, T50, T60, T80, T120, T150, T200, and T300. There's no dedicated test for TimeCues yet.