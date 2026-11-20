# Logix AOI Library

Reusable Add-On Instructions (AOIs) and UDTs for Allen-Bradley Logix controllers (Studio 5000).

## Contents

| AOI | Description | Version |
|---|---|---|
| [BitDelay](AOIs/BitDelay) | On-delay, off-delay, and debounced copies of a BOOL input | 1.0 |
| [Blink](AOIs/Blink) | Flasher: output toggles at a set half-period | 1.0 |
| [PRNG](AOIs/PRNG) | Seedable pseudo-random numbers (Park–Miller) with an integer range output | 1.0 |
| [TimeCues](AOIs/TimeCues) | One-scan cues as an elapsed time passes fixed points from 10 ms to 10 s | 1.0 |
| [TwoPosActuator](AOIs/TwoPosActuator) | Confirms a two-position actuator's position from end sensors, with timeout, sensor, and parameter faults | 1.0 |

## Tests

| Test | Covers |
|---|---|
| [TwoPosActuator](Tests/TwoPosActuator) | TwoPosActuator: 24 automated cases, all passing |

Each test folder holds a Studio 5000 project (`.L5X` and `.L5K`) and a README explaining how to run it.

## Using an AOI

1. Download the `.L5X` from the AOI's folder.
2. Import Add-On Instruction in Studio 5000.
3. Create a backing tag of the AOI's type for each instance.

## Conventions

- Each AOI takes all external data through parameters (no direct controller-tag references), so it works in any project.
- The AOI's Revision field matches the Git tag (e.g. `Blink-v1.0`).
- Exported from Studio 5000 v36; importing into older versions will fail.

## License

MIT. See [LICENSE](LICENSE).
