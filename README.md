# How to Install and Use Japanese Keyboard (Hiragana & Katakana) on CachyOS

This guide will help you set up Japanese input methods on CachyOS Linux distribution, allowing you to type in Hiragana, Katakana, and Kanji.

## Prerequisites

- CachyOS system with sudo privileges
- Basic terminal knowledge

## Method 1: Using IBus (Recommended)

### Installation

1. **Install IBus and Japanese language packages:**
```bash
sudo pacman -S ibus ibus-anthy
```

2. **Install Japanese fonts (if not already installed):**
```bash
sudo pacman -S noto-fonts-cjk
```

3. **Configure environment variables:**
Add the following lines to your shell configuration file (`~/.bashrc`, `~/.zshrc`, or `~/.profile`):
```bash
export GTK_IM_MODULE=ibus
export QT_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
```

4. **Apply the changes:**
```bash
source ~/.bashrc  # or your respective shell config file
```

5. **Start IBus daemon:**
```bash
ibus-daemon -drx
```

6. **Configure input methods:**
```bash
ibus-setup
```
   - Go to the "Input Method" tab
   - Click "Add" and select "Japanese - Anthy"
   - Click "OK" to save

### Auto-start IBus (Optional)

To automatically start IBus on system boot, add this to your desktop environment's autostart or add to your shell configuration:
```bash
ibus-daemon -drx &
```

## Method 2: Using Fcitx5 (Alternative)

### Installation

```bash
sudo pacman -S fcitx5 fcitx5-mozc fcitx5-configtool fcitx5-gtk fcitx5-qt
```

### Configuration

Add to your shell configuration file:
```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
```

## How to Use Japanese Input

### Activating Japanese Input

1. **Start the input method daemon** (if not already running):
```bash
ibus-daemon -drx  # for IBus
# or
fcitx5 &  # for Fcitx5
```

2. **Toggle between input methods:**
   - Press `Super + Space` or `Ctrl + Space`
   - Or click the IBus/Fcitx icon in the system tray
   - Look for indicators like "あ" (Japanese mode) or "A" (English mode)

### Typing in Japanese

#### Typing Hiragana:
- Type romaji (Latin characters) and they will automatically convert to hiragana
- Example: Type `konnichiwa` → こんにちは
- **To keep it as hiragana:** Press `Enter` directly (don't press Space)

#### Converting to Kanji:
- Type romaji to get hiragana first
- Press `Space` to convert hiragana to kanji
- Example: Type `nihon` → にほん → press `Space` → 日本
- Use arrow keys (↑/↓) to select different kanji options
- Press `Enter` to confirm your selection

#### Converting to Katakana:
- Type romaji first
- Press `F7` to convert directly to katakana
- Example: Type `kompyuutaa` → press `F7` → コンピューター

### Important Keyboard Shortcuts

| Key | Function |
|-----|----------|
| `F6` | Convert to Hiragana |
| `F7` | Convert to full-width Katakana |
| `F8` | Convert to half-width Katakana |
| `F9` | Convert to full-width Romaji |
| `F10` | Convert to half-width Romaji |
| `Space` | Convert to Kanji (shows conversion candidates) |
| `Enter` | Confirm input without conversion |
| `Esc` | Cancel conversion, return to hiragana |
| `↑/↓` | Navigate through kanji candidates |

### Understanding the Conversion Process

1. **Romaji Input:** Type using English keyboard → `watashi`
2. **Hiragana Display:** Shows as → わたし
3. **Options:**
   - Press `Enter` → Stays as hiragana: わたし
   - Press `Space` → Converts to kanji: 私
   - Press `F7` → Converts to katakana: ワタシ

## Troubleshooting

### Input method not working:
1. Check if the daemon is running:
```bash
ps aux | grep ibus  # for IBus
ps aux | grep fcitx  # for Fcitx5
```

2. Restart the input method:
```bash
killall ibus-daemon && ibus-daemon -drx
# or
killall fcitx5 && fcitx5 &
```

### Environment variables not loaded:
- Logout and login again
- Or restart your desktop session
- Verify variables are set: `echo $GTK_IM_MODULE`

### Fonts not displaying correctly:
```bash
sudo pacman -S noto-fonts-cjk adobe-source-han-sans-jp-fonts
```

## Testing Your Setup

1. Open a text editor (like gedit, kate, or any web browser)
2. Press `Super + Space` to switch to Japanese input
3. Type `arigatou` and you should see ありがとう
4. Press `Space` to see kanji options: 有り難う
5. Press `Enter` to confirm

## Additional Tips

- **Modern applications:** Most modern applications support these input methods automatically
- **Legacy applications:** Some older applications might need additional configuration
- **Gaming:** You might want to disable Japanese input while gaming to avoid accidental switches
- **Multiple languages:** You can add multiple input methods (Korean, Chinese, etc.) using the same process

## Uninstalling

To remove Japanese input methods:

```bash
# For IBus
sudo pacman -R ibus ibus-anthy

# For Fcitx5  
sudo pacman -R fcitx5 fcitx5-mozc fcitx5-configtool fcitx5-gtk fcitx5-qt

# Remove environment variables from your shell config file
```

---

**Note:** After installation and configuration, you may need to restart your system or at least logout/login for all changes to take effect properly.

## Contributing

Feel free to contribute to this guide by submitting issues or pull requests with improvements, corrections, or additional methods.
