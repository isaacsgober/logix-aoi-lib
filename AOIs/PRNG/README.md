# PRNG

Pseudo-random number generator (Park–Miller minimal standard), with seeding and an integer range output.

## Parameters

| Name | Usage | Type | Description |
|---|---|---|---|
| Seed | Input | DINT | Starting value, loaded on Reseed's rising edge. Values outside 1–2147483646 load as 1. |
| Reseed | Input | BOOL | Each rising edge loads Seed into the generator once. |
| Min | Input | DINT | Lowest value of Out |
| Max | Input | DINT | Highest value of Out. If below Min, Out is Min. |
| Value | Output | DINT | Raw generator value (1 to 2147483646) |
| Out | Output | DINT | Min + (Value MOD (Max - Min + 1)) |

## Behavior

- Each call advances the sequence one step: `x := (16807 × x) MOD 2147483647`, computed with Schrage's method so no intermediate value exceeds a DINT.
- Reseed is edge-detected between calls. The first call after a reseed returns the first value of the sequence, not the seed.
- If the internal state is invalid (for example, a new instance), the next call loads Seed automatically.
- The same Seed always produces the same sequence. The cycle is 2,147,483,646 values long.
- Not suitable for security or cryptographic use.

## Usage

Call it only when a new number is needed. Each call consumes one value, and the outputs hold between calls.

```
IF Need_Number THEN
    PRNG(Pick, 12345, Reseed_PB, 0, 2);
END_IF;
```

In ladder, condition the rung with a one-shot so it executes once per request.

## Verification

From Seed 1, the first call returns 16807 and the 10,000th call returns 1043618065, Park and Miller's published check value.