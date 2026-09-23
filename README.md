# Japanese input on CachyOS with Fcitx5 and Mozc

Type Japanese in KDE Plasma and other desktop environments on CachyOS. This guide uses Fcitx5 as the input method framework and Mozc as the Japanese input engine.

The instructions cover:

- KDE Plasma on Wayland (recommended path)
- KDE Plasma on X11
- GTK, Qt, X11, and Xwayland applications
- Hiragana, Katakana, and Kanji conversion
- Common problems and diagnostics

> [!NOTE]
> You do not need to change your system language or keyboard layout to type Japanese. Fcitx5 and Mozc convert text entered with a Latin keyboard into kana and Kanji.

## Contents

- [Choose the right setup](#choose-the-right-setup)
- [Install Fcitx5 and Mozc](#install-fcitx5-and-mozc)
- [Configure KDE Plasma on Wayland](#configure-kde-plasma-on-wayland)
- [Add Mozc to Fcitx5](#add-mozc-to-fcitx5)
- [Test Japanese input](#test-japanese-input)
- [Type Hiragana, Katakana, and Kanji](#type-hiragana-katakana-and-kanji)
- [Configure X11 or Xwayland applications](#configure-x11-or-xwayland-applications)
- [Troubleshooting](#troubleshooting)
- [Uninstall](#uninstall)
- [References](#references)

## Choose the right setup

Check whether the current desktop session uses Wayland or X11:

```bash
echo "$XDG_SESSION_TYPE"
```

| Session | Recommended setup |
| --- | --- |
| KDE Plasma Wayland | Let KWin start Fcitx5 from the Virtual Keyboard settings. Do not set global GTK/Qt input-method variables unless an application needs them. |
| KDE Plasma X11 | Start Fcitx5 with desktop autostart and set the GTK, Qt, and XMODIFIERS variables. |
| Other Wayland desktops | Install the same packages, then follow the input-method instructions for your compositor or desktop environment. |

## Install Fcitx5 and Mozc

Update the package database and install Fcitx5, Mozc, the configuration tool, toolkit integration, and Japanese-capable fonts:

```bash
sudo pacman -Syu fcitx5 fcitx5-mozc fcitx5-configtool fcitx5-gtk fcitx5-qt noto-fonts-cjk
```

What these packages provide:

| Package | Purpose |
| --- | --- |
| `fcitx5` | Input method framework |
| `fcitx5-mozc` | Japanese IME based on Mozc |
| `fcitx5-configtool` | Graphical Fcitx5 configuration tool |
| `fcitx5-gtk` | Integration for GTK applications |
| `fcitx5-qt` | Integration for Qt applications |
| `noto-fonts-cjk` | Fonts for Japanese, Chinese, and Korean text |

## Configure KDE Plasma on Wayland

KWin should launch Fcitx5 in a Plasma Wayland session.

1. Open **System Settings**.
2. Go to **Input & Output → Keyboard → Virtual Keyboard**.
3. Select **Fcitx 5**.
4. Apply the change.
5. Log out and log back in.

Do not also launch `fcitx5` from a shell startup file. Starting it twice can produce this warning:

```text
Fcitx should be launched by KWin under KDE Wayland
```

If Fcitx5 disappears after login, return to **Virtual Keyboard** and make sure **Fcitx 5** is still selected. Also check that Plasma's on-screen keyboard support has not been disabled.

## Add Mozc to Fcitx5

Open the configuration tool from the application launcher, or run:

```bash
fcitx5-configtool
```

Then:

1. Select the **Input Method** tab.
2. Disable **Only Show Current Language** if Mozc is not listed.
3. Search for `Mozc`.
4. Add **Mozc** to the active input methods.
5. Keep an English keyboard entry as the first item and Mozc as the second.
6. Click **Apply**.

The default shortcut for switching input methods is usually `Ctrl+Space`. You can change it under **Global Options → Trigger Input Method**.

> [!TIP]
> Plasma's keyboard-layout switcher and Fcitx5's input-method switcher are separate systems. Use the Fcitx5 shortcut to switch between the Latin keyboard and Mozc.

## Test Japanese input

1. Open Kate, Firefox, or another application with a text field.
2. Press `Ctrl+Space` to activate Mozc.
3. Type `arigatou`.
4. Confirm that it appears as `ありがとう`.
5. Press `Space` to display conversion candidates.
6. Select `有り難う`, then press `Enter`.

You can also check Fcitx5 from a terminal:

```bash
fcitx5-remote
```

Common return values:

- `0`: Fcitx5 is not running
- `1`: Fcitx5 is running but the input method is inactive
- `2`: the input method is active

## Type Hiragana, Katakana, and Kanji

Mozc accepts rōmaji typed on a Latin keyboard.

### Hiragana

Type the word and press `Enter` without converting it:

```text
konnichiwa → こんにちは
```

### Kanji

Type the reading, press `Space`, choose a candidate, and press `Enter`:

```text
nihon → にほん → 日本
```

Press `Space` again to show more candidates. Use the arrow keys or number keys to choose one.

### Katakana

Type the reading and press `F7` before confirming it:

```text
konpyuutaa → コンピューター
```

On a laptop, you may need to press `Fn+F7`.

### Conversion keys

| Key | Action while composing text |
| --- | --- |
| `Space` | Convert kana to Kanji or show more candidates |
| `Enter` | Confirm the current text or candidate |
| `Esc` | Cancel the current conversion |
| `F6` | Convert to Hiragana |
| `F7` | Convert to full-width Katakana |
| `F8` | Convert to half-width Katakana |
| `F9` | Convert to full-width Latin characters |
| `F10` | Convert to half-width Latin characters |

## Configure X11 or Xwayland applications

Skip this section if Japanese input already works in all applications.

### KDE Plasma on X11

Create a per-user environment file:

```bash
mkdir -p ~/.config/environment.d
cat > ~/.config/environment.d/90-fcitx5.conf <<'EOF'
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
EOF
```

Then log out and log back in. Fcitx5 normally provides an XDG autostart entry, so it should start with the desktop session.

> [!IMPORTANT]
> The value is `fcitx`, not `fcitx5`.

### Individual applications on Wayland

Native Wayland applications generally work best through Wayland's text-input protocol. Setting `GTK_IM_MODULE` or `QT_IM_MODULE` globally can bypass that path, so only use an override when a specific application does not work.

Test a GTK application with:

```bash
GTK_IM_MODULE=fcitx application-name
```

Test a Qt application with:

```bash
QT_IM_MODULE=fcitx application-name
```

For legacy X11 or Xwayland applications, test:

```bash
XMODIFIERS=@im=fcitx application-name
```

If an override fixes the application, add it to that application's launcher instead of applying it to the entire Wayland session.

## Troubleshooting

### Run the diagnostic tool

Fcitx5 includes a diagnostic command:

```bash
fcitx5-diagnose
```

The report is long, but its warnings usually identify missing addons, environment problems, duplicate processes, or applications that cannot find the input-method module.

Do not apply every suggestion blindly. In a native Wayland session, warnings about unset `GTK_IM_MODULE` or `QT_IM_MODULE` may be harmless.

### Fcitx5 is not running

Check the process:

```bash
pgrep -a fcitx5
```

On KDE Plasma Wayland, reselect **Fcitx 5** under **System Settings → Input & Output → Keyboard → Virtual Keyboard**, then log out and back in.

On X11, start it once for testing:

```bash
fcitx5 -d
```

### Mozc does not appear in the configuration tool

Confirm that the package is installed:

```bash
pacman -Q fcitx5-mozc
```

Then reopen `fcitx5-configtool`, disable **Only Show Current Language**, and search for Mozc again.

### The shortcut does nothing

Open `fcitx5-configtool` and check **Global Options → Trigger Input Method**. Another Plasma shortcut may already use the same key combination.

Check the current state directly:

```bash
fcitx5-remote
fcitx5-remote -t
```

The second command toggles the active input method.

### Japanese works in some applications only

1. Confirm whether the failing application is native Wayland or Xwayland.
2. Make sure `fcitx5-gtk` and `fcitx5-qt` are installed.
3. Test the application with one of the per-application variables described above.
4. Run `fcitx5-diagnose` while the application is installed.

For Chromium-based applications on Wayland, text-input protocol support can vary by version. If the candidate window is misplaced or input does not work, first confirm that `fcitx5-gtk` is installed. Testing the application under Xwayland can also help isolate the issue.

### Japanese characters appear as boxes

Install or reinstall a CJK font, then rebuild the font cache:

```bash
sudo pacman -S noto-fonts-cjk
fc-cache -fv
```

### Duplicate Fcitx5 processes on Plasma Wayland

Check for user-created autostart entries and shell startup commands:

```bash
pgrep -a fcitx5
grep -R "fcitx5" ~/.config/autostart ~/.profile ~/.bash_profile ~/.zprofile 2>/dev/null
```

Remove custom `fcitx5 &` commands when KWin already launches Fcitx5 through the Virtual Keyboard setting.

## Uninstall

Remove the packages installed by this guide:

```bash
sudo pacman -Rns fcitx5-mozc fcitx5-configtool fcitx5-gtk fcitx5-qt
```

Pacman may keep `fcitx5` if another installed package still depends on it. Review the package list before confirming removal.

If you created the X11 environment file from this guide, remove it:

```bash
rm ~/.config/environment.d/90-fcitx5.conf
```

Log out and back in after removing the configuration.

## Why Fcitx5 and Mozc?

Fcitx5 integrates well with Qt-based desktops such as KDE Plasma. Mozc is an actively maintained Japanese conversion engine derived from Google Japanese Input. Anthy is still available, but it has seen little upstream development for many years and is no longer the best default for a new setup.

## References

- [ArchWiki: Fcitx5](https://wiki.archlinux.org/title/Fcitx5)
- [ArchWiki: Japanese localization](https://wiki.archlinux.org/title/Localization/Japanese)
- [Fcitx5 documentation](https://fcitx-im.org/wiki/Fcitx_5)
- [Using Fcitx5 on Wayland](https://fcitx-im.org/wiki/Using_Fcitx_5_on_Wayland)
- [Mozc source repository](https://github.com/google/mozc)

## Contributing

Corrections and desktop-specific notes are welcome. Open an issue or submit a pull request with:

- the desktop environment and version
- whether the session uses Wayland or X11
- the affected application
- relevant output from `fcitx5-diagnose`

## License

This guide is available under the [MIT License](LICENSE).
