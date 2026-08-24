# NTvector (OPEN_NTVECTOR)

**NetEase Minecraft Bedrock Edition Bot Client (Vector)** — built in **C++17 with CMake**, embedding **Python 3.12.2** to run plugins (migrated from Python 2.7). Primary platform: **Windows (MSVC)**; Linux x86_64 / Android (Termux) were previously supported (see `docs/`).

All third-party C/C++ dependencies are bundled and compiled from source — **no external package manager or network required at build time**.

## Features

- **Minecraft Bedrock protocol client**: login / auth / packet encode-decode
- **Multiple network backends**:
  - **SLikeNet** (RakNet fork) — online communication
  - **libwebsockets** — WebSocket protocol
  - **libdatachannel** — WebRTC DataChannel (libjuice / libsrtp / usrsctp)
  - **NetherNet** connection mode (`--start_nethernet`)
- **Embedded Python 3.12.2 runtime**: registers 15 built-in modules (`engine`, `setting`, `mod_log`, `utility`, `pkt`, `aes`, `_chacha`, `_websocket`, `rotor`, `fop`, `_client`, `client_instance`, `easy_utils`, `_raknet`, `tan_lobby_game_clicpp_wrapper`)
- **Plugin hot-reload**: in `source` mode, scans `scripts/` every 1.5s — changes apply on save
- **Crypto & auth**: AES-GCM / AES-CTR / AES-ECB, ChaCha20, Rotor, ECC/ECDSA key exchange, Base64
- **MCP filesystem mode**: toggled via `no_launch_mcp` in `client_cfg.json` (legacy feature)
- **Skin system**: skin data parsing, skin/model file loading & conversion

## Environment

| Platform | Requirement |
|---|---|
| Windows (primary) | Visual Studio 2026 (MSVC 14.51), CMake 4.x, Python 3.12.2 dev install (for linking) |
| Linux x86_64 | Legacy (clang/gcc); pending build-script update after Py3 migration |
| Android ARM64 | Legacy (Termux native); pending update |

Python 3.12 dev install path (for linking): `C:/Users/admin/AppData/Local/Programs/Python/Python312`.

## Build (Windows)

```bash
cmake -B build -G "Visual Studio 18 2026" -A x64
cmake --build build --config Release
```

> Output: `build/application/Release/Program.exe`
>
> **Build pitfall**: after changing headers, MSBuild incremental won't recompile dependents — `touch` to force; if behavior breaks, `rm -rf build` and reconfigure.

## Deployment

Runtime location: `D:/Game/Minecraft/Vector/` — copy new `Program.exe` with the Python runtime:

```
D:/Game/Minecraft/Vector/
├── Program.exe            ← Py3 (new)
├── python312.dll          ← Python 3.12 DLL (import-lib loaded)
├── python3.dll, vcruntime140*.dll
├── python312/             ← 27MB trimmed standard library
│   ├── Lib/               ← stdlib (no __pycache__/test/tkinter)
│   ├── Lib/site-packages/ ← umsgpack.py, RC4.py, chacha/, raknet/, *.pyi stubs
│   └── DLLs/              ← all .pyd modules
├── source/                ← framework (init.py, proton.py, redirect.py)
├── scripts/               ← plugins (one dir each)
└── Program_py27.exe       ← old Py2 backup
```

## Dependencies (compiled from source, build offline)

| Library | Version | Build |
|---|---|---|
| Python | 3.12.2 | link `python312.lib`; deploy `python312/` beside exe |
| OpenSSL | 3.5.0 | auto-built at configure; falls back to bundled `.lib` |
| jsoncpp | 1.9.6 | `add_subdirectory` |
| libdatachannel | latest | `add_subdirectory` (libjuice, libsrtp, usrsctp) |
| libwebsockets | 4.5 | `add_subdirectory` |
| SLikeNet | custom (RakNet fork) | `add_subdirectory` |
| zlib | 1.3.1 | `add_subdirectory` |

## Config Files

- `mc.cfg` — startup config (room_info for server IP/port, player_info for account/nickname/token, misc for auth server/engine version/SID, skin_info for skin/model paths)
- `client_cfg.json` — client config (`no_launch_mcp`)
- `skin_data.json` — skin data

## CLI Arguments

- **Startup**: `--config`, `--start_nethernet`, `--do_main`, `--start_from_launcher`
- **Account / Auth**: `--UserID`, `--DisplayName`, `--MD5Token`, `--xuid`, `--operator_name`, `--operator_uid`, `--AuthServerUrl`, `--auto_auth_input`
- **Server**: `--ServerIP`, `--ServerPort`, `--NeteaseServerID`, `--HostNethernetId`, `--FromNethernetId`
- **Version**: `--protocol`, `--EngineVersion`, `--PatchVersion`, `--ExpandParams`
- **Skin**: `--skin_image_path`, `--skin_model_path`, `--operator_client_data`
- **Debug / Behavior**: `--logger`, `--disout`, `--script_mcp`, `--no_request_chunk`, `--g79`, `--pause`

## License

Released under the [MIT License](LICENSE).

## Disclaimer

For learning and research purposes only. Use in compliance with the game's terms of service.
