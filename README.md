# NTvector (OPEN_NTVECTOR)

A lightweight, high-performance **C++17 automation & bot framework for NetEase Minecraft (网易我的世界)**, built directly on the game's engine protocol.

## Features

- **High-performance C++17 core** — no runtime dependencies beyond the standard toolchain
- **Engine API wrapper** — send commands, messages, move the player, respawn, read server IP/port, and more
- **Plugin / script modes** — MCP plugin mode plus Python script mode, switchable at runtime
- **WebSocket & data-channel transport** for real-time communication
- **Cross-platform build** with CMake (Windows / Linux / macOS)

## Build

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release
```

## License

Released under the [MIT License](LICENSE).

## Disclaimer

For learning and research purposes only. Use in compliance with the game's terms of service.
