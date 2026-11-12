# BitDelay

On-delayed, off-delayed, and debounced copies of a BOOL, with separate on and off delays in ms.

## Parameters

| Name | Usage | Type | Description |
|---|---|---|---|
| In | Input | BOOL | Bit to delay |
| OnDelay | Input | DINT | ms In must hold on before OnDelayed and Debounced turn on. Values below 0 are clamped to 0. |
| OffDelay | Input | DINT | ms In must hold off before OffDelayed and Debounced turn off. Values below 0 are clamped to 0. |
| OnDelayed | Output | BOOL | On after OnDelay; off immediately |
| OffDelayed | Output | BOOL | On immediately; off after OffDelay |
| Debounced | Output | BOOL | On after OnDelay; off after OffDelay |

## Behavior

- An on-pulse shorter than OnDelay never reaches OnDelayed or Debounced. An off-blip shorter than OffDelay never reaches OffDelayed or Debounced.
- A delay of 0 makes that direction immediate.
- Each delay runs its preset plus up to one scan.
- On a false rung (or a skipped call in Structured Text), the outputs hold their last state and the timers stop.

## Usage

Place it on an unconditional rung so it runs every scan. Name the instance after the signal and give the raw input alias a `_Raw` suffix, so reads look like properties of the signal.

```
Rung 0:  BitDelay Part_Present (In := Part_Present_Raw, OnDelay := 500, OffDelay := 80)
Rung 1:  XIC Part_Present.Debounced   OTE Start_Cycle
```

Structured Text:

```
BitDelay(Part_Present, Part_Present_Raw, 500, 80);
```
