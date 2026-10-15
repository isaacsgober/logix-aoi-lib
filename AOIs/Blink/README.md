# Blink

Free-running flasher AOI. Output `Bit` toggles every `PRE` milliseconds (50% duty cycle).

## Parameters

| Name | Usage | Type | Description |
|---|---|---|---|
| PRE | Input | DINT | Half-period in ms (on time = off time = PRE). Values below 100 are clamped to 100. |
| Bit | Output | BOOL | Flashing output |

## Behavior

- Runs continuously while its rung is true; `Bit` toggles every PRE ms.
- On a false rung (or a skipped call in Structured Text), `Bit` holds its last state.

## Usage

Place one instance on an unconditional rung and gate `Bit` with your own logic at each output. All lights sharing the instance flash in sync.

    Rung 0:  Blink_500ms (PRE := 500)
    Rung 1:  XIC Drive_Faulted   XIC Blink_500ms.Bit   OTE Fault_Light
    Rung 2:  XIC Low_Air         XIC Blink_500ms.Bit   OTE Air_Light

A light may stay dark for up to one half-period after its condition goes true, until the shared flash cycles on.