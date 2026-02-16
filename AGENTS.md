# AGENTS.md - Sofle ZMK Driver

This repository contains ZMK firmware configuration for the Sofle V2 keyboard with Nice!Nano microcontroller and Nice!View displays.

## Build Commands

### Local Building
This project uses ZMK's west build system. To build locally:

```bash
# Initialize west (first time only)
west init -l config/
west update

# Build the firmware
west build -b nice_nano_v2 -s sofle_left nice_view_adapter nice_view
west build -b nice_nano_v2 -s sofle_right nice_view_adapter nice_view

# Build settings reset (for recovery)
west build -b nice_nano_v2 -s settings_reset
```

### GitHub Actions (CI/CD)
- **Build workflow**: `.github/workflows/build.yml` - Runs on push, pull_request, and manual dispatch
- **Keymap drawer**: `.github/workflows/keymap_drawer.yaml` - Generates SVG keymap visualizations

To trigger builds manually:
1. Go to GitHub Actions
2. Select the workflow
3. Click "Run workflow"

There are no lint or test commands for this project as it's pure configuration (keymap) files.

## Project Structure

```
sofle-zmk-driver/
├── config/                 # ZMK configuration files
│   ├── sofle.keymap      # Keyboard keymap (main file to edit)
│   ├── sofle.conf        # ZMK configuration (Kconfig)
│   └── west.yml          # West manifest
├── boards/               # Shield definitions
├── build.yaml            # GitHub Actions build matrix
└── keymap-drawer/        # Generated keymap SVG
```

## Code Style Guidelines

### Keymap File Conventions

**File: `config/sofle.keymap`**

1. **Indentation**: Use 4 spaces for indentation in keymap files

2. **Layer Structure**: Each layer should have:
   - A comment describing the layer purpose (optional but recommended)
   - `bindings` section with key assignments
   - `sensor-bindings` section for encoders/keys

3. **Key Codes**: Use ZMK key codes (e.g., `KC.A`, `KC.LSFT`, `KC.TAB`)
   - Reference: https://zmk.dev/docs/codes

4. **Naming Conventions**:
   - Layers: `name = <layer_name>;` (e.g., `name = base;`)
   - Use descriptive names: `base`, `lower`, `raise`, `adjust`, `symbols`

5. **Formatting Example**:
   ```
   name = base;
   bindings = <
       KC.TAB  KC.Q   KC.W   KC.E   KC.R   KC.T    KC.Y   KC.U   KC.I   KC.O   KC.P   KC.BSPC
       KC.ESC  KC.A   KC.S   KC.D   KC.F   KC.G    KC.H   KC.J   KC.K   KC.L   KC.SCLN KC.QUOT
       KC.LSFT KC.Z   KC.X   KC.C   KC.V   KC.B    KC.N   KC.M   KC.COM KC.DOT KC.SLSH KC.RSFT
               KC.LGUI KC.LALT KC.LCTL LT(_LOWER, KC.SPC) LT(_RAISE, KC.ENT)
   >;
   ```

6. **Transitions**: Use `LT(layer, key)` for layer-tap, `MT(mod, key)` for mod-tap

7. **Comments**: Use `//` for single-line comments in keymaps

### Configuration File Conventions

**File: `config/sofle.conf`**

1. **Kconfig syntax**: Follow ZMK Kconfig format
2. **Enable features**: Use `CONFIG_<FEATURE>=y` format
3. **Settings**: Use `CONFIG_<SETTING>=<value>` format

Example conventions:
```dts
CONFIG_ZMK_KEYBOARD_NAME="Sofle"
CONFIG_ZMK_SPLIT_BLE=y
CONFIG_DISPLAY=y
```

### Git Workflow

1. Make changes in a feature branch
2. Test locally with `west build` if possible
3. Push to trigger GitHub Actions build
4. Download artifacts (`.uf2` files) from Actions
5. Flash to keyboard

### Important Notes

- The build matrix is defined in `build.yaml`
- The build uses ZMK v0.3 (see `config/west.yml`)
- Changes to `config/*.keymap` trigger keymap drawer workflow
- UF2 files are named: `sofle_left-nice_nano_v2-zmk.uf2`, `sofle_right-nice_nano_v2-zmk.uf2`

## Key Resources

- ZMK Documentation: https://zmk.dev/docs
- ZMK Key Codes: https://zmk.dev/docs/codes
- Keymap Editor: https://nickcoutsos.github.io/keymap-editor
- ZMK GitHub: https://github.com/zmkfirmware/zmk
