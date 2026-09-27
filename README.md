# tornado — Tornado Outbreak (PS3), static recompilation

`BLUS30371`, disc release (Loose Cannon Studios / Konami, 2009). Recompiled with
[ps3recomp](https://github.com/sp00nznet/ps3recomp).

## Status

Boots through the Loose Cannon logo and the Bink intro movies to the title
screen, which renders correctly and takes input. START leaves the title; the
screen after it draws its text black. See [docs/progress.md](docs/progress.md).

## Why this title

Picked from a sweep of all 1,019 decryptable disc EBOOTs for low SPU load
(`ps3games/_triage/disc_sweep/`):

| | code | functions | SPU images in EBOOT | imports |
|---|---|---|---|---|
| Guitar Hero III | 9.7 MB | 28,987 | 5 (615 KB) | 229 |
| **Tornado Outbreak** | 5.3 MB | **17,138** | **1 (5 KB)** | **103** |

The one SPU image is a raw-SPU memcpy engine the PPU drives by mailbox. Wwise
runs two small audio jobs on a SPURS job chain.

## Building

```bash
./tools/relift.sh          # imports.json, functions.json, lifted tree, NID table, SPU image
python build.py            # cmake + ninja under the MSVC environment clang-cl needs
PS3_VFS_ROOT=vfs RSX_LIVE_DRAW=1 PS3_MAIN_STACK_LV2=1 \
    ./build/tornado vfs/PS3_GAME/USRDIR/EBOOT.elf
```

`vfs/` is the disc tree (`PS3_GAME/...`). `game/EBOOT.elf` and
`vfs/PS3_GAME/USRDIR/EBOOT.elf` are the decrypted EBOOT:

```bash
python ../twistedmetal/tools/decrypt_self.py vfs/PS3_GAME/USRDIR/EBOOT.BIN \
    --keys ../GT5P/data/keys -o game/EBOOT.elf
```

`PS3_MAIN_STACK_LV2=1` is required: the game's SPU memcpy picks its path by
comparing the destination with the stack's top nibble (see progress.md).

Headless input: `PAD_SCRIPT="18:0x0008"` presses START at 18 s; frames with
`LD_FRAME_DUMP=<dir>`.

## Legal

No game code, assets or keys here, only analysis and build configuration.
