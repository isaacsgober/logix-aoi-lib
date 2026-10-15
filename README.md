# Logix AOI Library

Reusable Add-On Instructions (AOIs) and UDTs for Allen-Bradley Logix controllers (Studio 5000).

## Contents

| AOI | Description | Version |
|---|---|---|

## Using an AOI

1. Download the `.L5X` from the AOI's folder.
2. Import Add-On Instruction in Studio 5000.
3. Create a backing tag of the AOI's type for each instance.

## Conventions

- Each AOI takes all external data through parameters (no direct controller-tag references) to ensure ease of use in any project.
- The AOI's Revision field matches the Git tag (e.g. `Blink-v1.0`).
- Exported from Studio 5000 v36; importing into older versions will fail.
