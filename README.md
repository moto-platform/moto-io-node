# moto-io-node

Part of [moto-platform](https://github.com/moto-platform), an SDV-style diagnostics, telemetry and rider-assistance platform for motorcycles (first vehicle: Honda CL250).

A combined zone-controller firmware running on **STM32G0** (G0B1 class; prototype: the STM32F103 on hand). Three modules live on the same node, each separate under `src/features/`:
- `blind_spot/`: 2× 24 GHz radar → approach logic → mirror LEDs (green / solid red / blinking red). The decision is made entirely here; only the result is written to CAN.
- `immobilizer/`: NFC (PN532) primary, handlebar PIN as backup → bistable relay.
- `power/`: INA226 monitoring, low voltage (<12.2 V) → low-power mode, park mode (IMU wake-on-motion, target <1 mA).

**Status:** skeleton, no code yet. The build system, tests and CI are added by `/repo-bootstrap moto-io-node` when work on this repo starts (setup order: `moto-vehicle-defs/docs/ARCHITECTURE.md` §9).

- Architecture and decisions: [moto-vehicle-defs/docs](https://github.com/moto-platform/moto-vehicle-defs/tree/main/docs) (`ARCHITECTURE.md`, `DECISIONS.md`)
- Signals, CAN IDs and DIDs come only from [moto-vehicle-defs](https://github.com/moto-platform/moto-vehicle-defs) (git submodule pinned to a tag)
- Scope rules for contributors and Claude Code: [`CLAUDE.md`](CLAUDE.md)

## License

MIT, see [LICENSE](LICENSE) (D-036).
