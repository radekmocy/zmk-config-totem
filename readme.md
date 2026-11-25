<picture>
  <source media="(prefers-color-scheme: dark)" srcset="/docs/images/TOTEM_logo_dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="/docs/images/TOTEM_logo_bright.svg">
  <img alt="TOTEM logo font" src="/docs/images/TOTEM_logo_bright.svg">
</picture>

# ZMK CONFIG FOR THE TOTEM SPLIT KEYBOARD

> **This is a personal fork** of [GEIGEIGEIST/zmk-config-totem](https://github.com/GEIGEIGEIST/zmk-config-totem).
> It keeps the original hardware definition, but updates the build for current ZMK and replaces the keymap
> with a Colemak-DH layout for Polish + English typing and programming on Windows, Linux and macOS.
> See [Changes in this fork](#changes-in-this-fork) below.

[Here](https://github.com/GEIGEIGEIST/totem) you can find the hardware files and build guide.\
[Here](https://github.com/GEIGEIGEIST/qmk-config-totem) you can find the QMK config for the TOTEM.

TOTEM is a 38 key column-staggered split keyboard running [ZMK](https://zmk.dev/) or [QMK](https://docs.qmk.fm/). It's meant to be used with a SEEED XIAO BLE or RP2040.


![TOTEM layout](/docs/images/TOTEM_layout.svg)

*The image above shows the original upstream layout. The layout used in this fork is drawn as ASCII diagrams
above every layer in [config/totem.keymap](config/totem.keymap).*


## CHANGES IN THIS FORK

### Build / ZMK compatibility
- **Board renamed** from `seeeduino_xiao_ble` to `xiao_ble//zmk` (the name used by current ZMK / Zephyr)
  in [build.yaml](build.yaml) and [totem.zmk.yml](config/boards/shields/totem/totem.zmk.yml).
- **ZMK Studio support**: the left (central) half is built with the `studio-rpc-usb-uart` snippet and
  `CONFIG_ZMK_STUDIO=y`, so the keymap can be edited live at [zmk.studio](https://zmk.studio/) over USB.
  A physical layout (`default_layout`) was added to [totem.dtsi](config/boards/shields/totem/totem.dtsi) for this.
  Studio is locked by default; unlock it with the *Studio unlock* key on the ADJ layer.

### Power saving ([config/totem.conf](config/totem.conf))
- Idle state (reduced scanning / BLE activity) after **5 min** without a key press.
- Deep sleep after **10 min** without a key press. Any key wakes that half (the waking key press is not typed);
  each half sleeps and wakes on its own.

### Keymap ([config/totem.keymap](config/totem.keymap))
The upstream keymap (Colemak + NAV/SYM/ADJUST + TVPaint layers) was replaced with:

| Layer | How to reach | Contents |
|---|---|---|
| **BASE** | default | Colemak-DH with home-row mods (GUI / Alt / Ctrl / Shift; O is a plain letter), Caps Word on the outer key |
| **MAC** | ADJ → *Mac* | overlay on BASE: home-row mods become Ctrl / Opt / Cmd / Shift, so shortcuts use the same finger on every OS |
| **NAV** | hold Sticky Shift (left inner thumb) | arrows, Home/End/PgUp/PgDn, Print Screen (Mac: ⌘⇧4 area screenshot), media keys, mods, Undo/Cut/Copy/Paste/Redo |
| **MAC NAV** | Mac mode + hold Sticky Shift | the NAV shortcuts in their macOS (Cmd) versions |
| **SYM** | hold Esc (left outer thumb) | all coding symbols, brackets mirrored on the home row (open left / close right) |
| **NUM** | hold Tab | F1–F12, Esc and Delete on the left hand, numpad on the right hand (0 on the right thumb) |
| **ADJ** | hold Esc + Sticky Shift | Bluetooth profiles 0–4 and clear, USB/BLE output, Studio unlock, Mac / Win-Linux mode, reset, bootloader |

Other details:
- **Thumbs**: Esc/SYM, Tab/NUM, Sticky Shift/NAV | Space, AltGr, Backspace (Shift + Backspace = Delete).
- **Enter** = press Sticky Shift + Space together (combo). Holding the Sticky Shift thumb for NAV and then pressing Space still types Space.
- **Home-row mods**: during the first 280 ms, a mod key only turns into a modifier when a key on the
  *other* hand (or a thumb) is pressed, so fast same-hand rolls stay letters. Held longer, it works
  with any key, so same-hand shortcuts like Ctrl+A still work if you hold the mod a moment first.
- **Custom shifted symbols** on the base layer: `/` → `\`, `,` → `;`, `.` → `:`, `?` → `!`, `'` → `"`.
- **Polish letters**: hold AltGr (right middle thumb) + letter (ą ć ę ł ń ó ś ż, ź = AltGr+X), add Shift for capitals.
- **OS mode** (Mac vs. Win/Linux) is not remembered after power-off; it always starts in Win/Linux.

### Required OS keyboard layout
The keymap assumes a US-based layout with Polish letters on AltGr/Option:

| OS | Keyboard layout |
|---|---|
| Windows | Polish (Programmers) |
| Linux | Polish (xkb: `pl`) |
| macOS | Polish (older macOS: "Polish Pro"), **not** "Polish QWERTZ" |


## HOW TO USE

- fork this repo
- `git clone` your repo, to create a local copy on your PC (you can use the [command line](https://www.atlassian.com/git/tutorials) or [github desktop](https://desktop.github.com/))
- adjust the [config/totem.keymap](config/totem.keymap) file (find all the keycodes on [the zmk docs pages](https://zmk.dev/docs/codes/))
- `git push` your repo to your fork
- on the GitHub page of your fork navigate to "Actions"
- scroll down and unzip the `firmware.zip` archive that contains the latest firmware
- connect the left half of the TOTEM to your PC, press reset twice (or use the *Bootloader* key on the ADJ layer)
- the keyboard should now appear as a mass storage device
- drag'n'drop the `totem_left-xiao_ble…-zmk.uf2` file from the archive onto the storage device
- repeat this process with the right half and the `totem_right-xiao_ble…-zmk.uf2` file.

### Battery & charging
- The halves charge whenever USB is connected, even while asleep, as long as the power switch is **on**
  (the switch disconnects the battery from the charger).
- ZMK estimates the battery level from voltage, so a fully charged battery usually shows about **94–95%**, not 100%.
