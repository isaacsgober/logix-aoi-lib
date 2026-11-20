# TwoPosActuator_Test

Test bench for the TwoPosActuator AOI. It runs 24 cases automatically and records the first failed check in each.

## Requirements

- 1769-L24ER-QB1B, v36. No I/O is used; the sensors are simulated with tags.
- TwoPosActuator and TimeCues, both included in the project.

To test a revised AOI, import the new version into this project, overwriting the existing definition, before running. Otherwise the run tests the old copy.

## Structure

MainRoutine calls two routines every scan:

1. **TestDriver** steps through `CaseList`, applies each case's timed actions, runs its checks, and records results.
2. **Harness** applies a case's presets when `TestMode` changes, pulses Reset, and calls TwoPosActuator.

`TestMode` = DriveType × 100 + case number. 901 and 902 test an out-of-range DriveType (-1 and 3).

## Running

1. Download and switch to Run.
2. Set `DriverRun` to 1. The run takes about 10 s, and `DriverRun` drops back to 0 when it finishes.
3. Read `FailTime[0]` through `FailTime[23]`. 0 = pass; any other value is the time (ms) of the first check that failed.

| FailTime index | TestMode |
|---|---|
| 0–18 | 1–19 |
| 19 | 111 |
| 20 | 211 |
| 21 | 215 |
| 22 | 901 |
| 23 | 902 |

To run one case by hand, leave `DriverRun` off and set `TestMode` directly, then change `Command`, `Sensor0`, `Sensor1`, and `ResetButton` as the case's actions list.

## Cases

Each case starts with `TestDeactivate` 0 and both timeouts at 100 ms, unless its setup says otherwise. t is milliseconds since the case started. "No faults" means TimeoutFault, SensorFault, and ParamFault are all off.

| Case | Name | Setup | Actions | Checks |
|---|---|---|---|---|
| 1 | At position 0 | Cmd 0, S0 1, S1 0 | — | t=50: AtPos0, Drive0, no faults. t=200: no faults. |
| 2 | Move to position 1 | Cmd 1, S0 1, S1 0 | t=50: S0 0, S1 1 | t=80: AtPos1, Drive1, no faults. t=200: no faults. |
| 3 | Timeout | Cmd 0, S0 0, S1 1 | — | t=50: no faults. t=150: TimeoutFault, Drive0. |
| 4 | Timeout, Deactivate on Fault | Cmd 0, S0 0, S1 1, TestDeactivate 1 | — | t=150: TimeoutFault, both drives off. |
| 5 | Reset retries | Cmd 0, S0 0, S1 1 | t=150: Reset | t=120: TimeoutFault. t=200: no faults. t=300: TimeoutFault. |
| 6 | Reset then arrive | Cmd 0, S0 0, S1 1 | t=150: Reset. t=200: S0 1, S1 0. | t=120: TimeoutFault. t=300: no faults, AtPos0. |
| 7 | Timer restarts on Cmd change | Cmd 1, S0 0, S1 0 | t=60: Cmd 0 | t=120: no faults. t=200: TimeoutFault. |
| 8 | Lost position | Cmd 1, S0 0, S1 1 | t=50: S1 0 | t=40: AtPos1. t=120: no faults. t=200: TimeoutFault. |
| 9 | Both sensors on | Cmd 0, S0 1, S1 1 | — | t=20: SensorFault. t=150: TimeoutFault. |
| 10 | Sensor fault reset | Cmd 0, S0 1, S1 1 | t=20: Reset. t=40: S1 0. t=50: Reset. | t=30: SensorFault. t=60: no faults. |
| 11 | Stop command | Cmd 2, S0 0, S1 0 | — | DriveType 0 or 1: t=20: ParamFault. DriveType 2: t=20: no faults, both drives off. t=150: no faults. |
| 12 | Cmd out of range | Cmd 5, S0 0, S1 0 | — | t=20: ParamFault. |
| 13 | Negative Timeout0 | Cmd 0, S0 1, S1 0, Timeout0 -1 | — | t=20: ParamFault. |
| 14 | Negative Timeout1 | Cmd 1, S0 0, S1 1, Timeout1 -1 | — | t=20: ParamFault. |
| 15 | Stop, then move | Cmd 2, S0 0, S1 0 | t=50: Cmd 1 | t=60: Drive1. t=200: TimeoutFault. |
| 16 | At position 1 | Cmd 1, S0 0, S1 1 | — | t=50: AtPos1, Drive1, no faults. t=200: no faults. |
| 17 | Move to position 0 | Cmd 0, S0 0, S1 1 | t=50: S0 1, S1 0 | t=80: AtPos0, Drive0, no faults. t=200: no faults. |
| 18 | Cmd 0 uses Timeout0 | Cmd 0, S0 0, S1 1, Timeout1 300 | — | t=150: TimeoutFault. |
| 19 | Cmd 1 uses Timeout1 | Cmd 1, S0 1, S1 0, Timeout0 300 | — | t=150: TimeoutFault. |
| 901 | DriveType -1 | Case 1's setup | — | t=20: ParamFault. |
| 902 | DriveType 3 | Case 2's setup | — | t=20: ParamFault. |

Cases 1–10 and 12–19 behave the same on every DriveType. The run covers them on DriveType 0, then runs 111, 211, and 215, where DriveType changes the result.

## Adding a case

1. Add its presets to the CASE in Harness.
2. Add its actions and checks to the CASE in TestDriver.
3. Add its `TestMode` to `CaseList`.
4. Lengthen `CaseList` and `FailTime`, and raise the two hard-coded 23s in TestDriver (the `FOR` loop and the `CaseIndex > 23` check) to the new last index.