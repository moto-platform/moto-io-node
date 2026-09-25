# CLAUDE.md — moto-io-node

@.claude/PLATFORM-RULES.md

## What this repo is

A combined zone-controller firmware running on **STM32G0** (G0B1 class; prototype: the STM32F103 on hand). Three modules live on the same node, each separate under `src/features/`:
- `blind_spot/`: 2× 24 GHz radar → approach logic → mirror LEDs (green / solid red / blinking red). The decision is made entirely here; only the result is written to CAN.
- `immobilizer/`: NFC (PN532) primary, handlebar PIN as backup → bistable relay.
- `power/`: INA226 monitoring, low voltage (<12.2 V) → low-power mode, park mode (IMU wake-on-motion, target <1 mA).

## What this repo is NOT

- The cornering decision is not here (`moto-safety-node`). EKF is not here (`moto-rt-core`).
- Wi-Fi/GSM transmission is not here: the park alarm is relayed to `moto-connectivity-node` over the platform CAN.
- The blind spot system is not connected to the AI assistant. It needs reflex speed; the right interface is an LED.

## Safety-critical rules (no exceptions)

1. The immobilizer intervenes **ONLY on the starter relay coil circuit**. It NEVER touches ignition, the fuel pump, or the ECU bus.
2. Fail-safe: the default position is **unlocked** (if the system dies, the engine can still start), a hidden mechanical bypass exists, and if no decision is made within 10 s it moves to the unlocked position.
3. No dynamic memory in the blind-spot or immobilizer logic. Blind spot keeps working even if rt-core or CAN goes down (the LED is driven locally).
4. The false-alarm filter (distinguishing a stationary object from an approaching vehicle) is the heart of the system. Threshold changes are commented with their rationale.
5. The `safety-reviewer` agent is invoked after any change (item 6 for the immobilizer).

## Hardware note

The F103 is a prototype only: 20 KB RAM, a single bxCAN, classic CAN. Code is written to be portable to the G0: the HAL wrapper lives in `src/hal/`, nothing chip-specific leaks into `features/`. Overlap with the F103's role in the HIL bench → Q-003.

## Dependencies

`external/moto-vehicle-defs` (tagged), only `gen/c/io/`. Connected only to the platform bus. Turn-signal state comes from the platform bus or directly from GPIO.

## Build

CMake + STM32CubeMX + arm-none-eabi-gcc (D-007), bare-metal (D-012). Decision logic is HAL-free and tested under `tests/host/`.

## Context

ARCHITECTURE §2-4 · `../moto-vehicle-defs/docs/hardware-architecture.md` §5b.1, §5b.3.
