# TwoPosActuator

Two-position actuator monitor that confirms the commanded position from end-of-travel sensors, with a timeout fault and a sensor conflict fault. One call handles single-solenoid, double-solenoid, and three-position center valves.

## Parameters

Inputs are listed in call order.

| Name | Usage | Type | Description |
|---|---|---|---|
| DriveType | Input | INT | 0 = one drive, returns to position 0 when off (single-solenoid spring return). 1 = two drives, holds when both off (double-solenoid detented). 2 = two drives, stops when both off (three-position center). |
| DeactivateOnFault | Input | BOOL | If true, any latched fault turns both drives off. If false, the drives keep following Cmd. |
| Cmd | Input | INT | 0 = go to position 0, 1 = go to position 1, 2 = stop (DriveType 2 only). |
| Pos0Sensor | Input | BOOL | Sensor at position 0. |
| TimeoutTo0 | Input | DINT | Time allowed to reach position 0 after Cmd becomes 0 (ms). |
| Pos1Sensor | Input | BOOL | Sensor at position 1. |
| TimeoutTo1 | Input | DINT | Time allowed to reach position 1 after Cmd becomes 1 (ms). |
| Reset | Input | BOOL | Clears latched faults and restarts the timeout. Momentary; holding it suspends the timeout. |
| Drive0 | Output | BOOL | On when Cmd is 0. Off on a latched fault if DeactivateOnFault is true. |
| Drive1 | Output | BOOL | On when Cmd is 1. Off on a latched fault if DeactivateOnFault is true. |
| AtPos0 | Output | BOOL | Cmd is 0, Pos0Sensor is on, and Pos1Sensor is off. |
| AtPos1 | Output | BOOL | Cmd is 1, Pos1Sensor is on, and Pos0Sensor is off. |
| TimeoutFault | Output | BOOL | Commanded position not confirmed in time. Latched until Reset. |
| SensorFault | Output | BOOL | Both sensors on at once. Latched until Reset. |
| ParamFault | Output | BOOL | DriveType or Cmd out of range, Cmd 2 with DriveType 0 or 1, or a negative timeout. Latched until Reset. |

## Behavior

- The drives depend only on Cmd. Drive0 is on for Cmd 0, Drive1 for Cmd 1, and Cmd 2 or any invalid value turns both off. DriveType only decides whether Cmd 2 is legal.
- A single-solenoid valve uses Drive1 only, and its spring returns it to position 0. A double-solenoid or center valve uses both drives.
- The timeout runs while Cmd is 0 or 1 and the commanded position isn't confirmed. It restarts when Cmd changes or Reset is on, and it uses TimeoutTo0 or TimeoutTo1 for the direction commanded.
- If a sensor drops out after arrival, the timeout starts again, so a lost position raises TimeoutFault. With DeactivateOnFault on, that also turns the drives off.
- Cmd 2 runs no timeout, and both AtPos outputs stay off.
- Reset clears all three faults, and any fault whose cause is still present re-latches in the same call. Because Reset also restarts the timeout, pulsing it retries a timed-out move.

## Usage

Call it every scan, unconditionally. The timeout timer only advances when the AOI is called, so a conditional call freezes it.

```
TwoPosActuator(Gate, 0, 1, GateCmd, Gate_Closed, 1500, Gate_Open, 1500, Fault_Reset);
Gate_Solenoid := Gate.Drive1;
IF Gate.AtPos1 THEN
    // gate confirmed open
END_IF;
```

Each instance decides which physical end is position 0. For a single-solenoid valve, position 0 is the de-energized end, which is also where the device goes on power loss.

## Verification

Tested with `Tests/TwoPosActuator` (TwoPosActuator_Test): 24 cases covering all three DriveTypes, normal moves in both directions, timeouts, retries, lost position, sensor faults, and invalid parameters. All pass, and a deliberately broken check confirmed that failures are recorded.