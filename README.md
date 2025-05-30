# Keyd - Simple Manual

A beginner-friendly guide to keyd, the key remapping tool for Linux.

## Table of Contents

- [What is Keyd?](#what-is-keyd)
- [Quick Start](#quick-start)
- [Commands Reference](#commands-reference)
- [Configuration Basics](#configuration-basics)
- [Understanding Layers](#understanding-layers)
- [Key Bindings](#key-bindings)
- [Common Actions](#common-actions)
- [Macros Made Simple](#macros-made-simple)
- [Advanced Features](#advanced-features)
- [Practical Examples](#practical-examples)
- [Troubleshooting](#troubleshooting)
- [Tips and Best Practices](#tips-and-best-practices)

---

## What is Keyd?

Keyd is a powerful tool that lets you:
- **Remap keys** - Make any key do something different
- **Create layers** - Turn one key into many different functions
- **Add macros** - Make one key type entire phrases
- **System-wide changes** - Works everywhere (games, terminals, browsers)

Think of it as a way to make your keyboard work exactly how you want it to.

---

## Quick Start

### Installation Check
```bash
# Check if keyd is running
sudo systemctl status keyd

# Start keyd if it's not running
sudo systemctl start keyd
sudo systemctl enable keyd
```

### Your First Config
1. Create a config file: `/etc/keyd/default.conf`
2. Add this basic setup:

```ini
[ids]
*

[main]
capslock = esc
```

3. Reload the config:
```bash
sudo keyd reload
```

Now your Caps Lock key works as Escape!

---

## Commands Reference

### Essential Commands

| Command | What it does | Example |
|---------|--------------|---------|
| `sudo keyd reload` | Apply new config changes | After editing config files |
| `keyd monitor` | See what keys you're pressing | Find key names for config |
| `keyd monitor -t` | Same as above + timing info | Debug timing issues |
| `keyd list-keys` | Show all available key names | When writing configs |
| `keyd check` | Test your config for errors | Before applying changes |

### Advanced Commands

| Command | What it does | Example |
|---------|--------------|---------|
| `keyd bind` | Change keys temporarily | `keyd bind 'a = b'` |
| `keyd bind reset` | Undo temporary changes | Back to original config |
| `keyd listen` | Watch layer changes | For debugging |
| `keyd input "text"` | Type text automatically | For scripts |

### Emergency Reset
If keyd makes your keyboard unusable, press: **Backspace + Escape + Enter** together to force quit keyd.

---

## Configuration Basics

### File Location
Config files go in `/etc/keyd/` and must end with `.conf`

### Basic Structure
Every config file needs this structure:

```ini
[ids]
*

[main]
# Your key mappings go here
```

### What Each Part Does

#### `[ids]` Section
Tells keyd which keyboards to affect:

```ini
[ids]
*                    # All keyboards
```

Or specific keyboards:
```ini
[ids]
1234:5678           # Only keyboard with this ID
```

Or exclude specific keyboards:
```ini
[ids]
*                   # All keyboards...
-1234:5678          # ...except this one
```

#### `[main]` Section
Your basic key mappings:

```ini
[main]
a = b               # 'a' key now types 'b'
capslock = esc      # Caps Lock becomes Escape
tab = ctrl          # Tab becomes Control
```

---

## Understanding Layers

Layers are like having multiple keyboards in one. You can switch between them to make the same keys do different things.

### Simple Layer Example

```ini
[main]
capslock = layer(nav)    # Hold Caps Lock to activate 'nav' layer

[nav]
h = left                 # When nav is active, 'h' = Left Arrow
j = down                 # 'j' = Down Arrow
k = up                   # 'k' = Up Arrow  
l = right                # 'l' = Right Arrow
```

**How it works:**
- Normally, `h` types "h"
- Hold Caps Lock + `h` = Left Arrow
- Release Caps Lock = back to normal

### Layer Types

#### 1. Regular Layers
```ini
[main]
space = layer(numbers)   # Hold Space for numbers

[numbers]
a = 1
s = 2
d = 3
```

#### 2. Toggle Layers
```ini
[main]
f1 = toggle(gaming)      # Press F1 to turn gaming mode on/off

[gaming]
w = up                   # WASD becomes arrow keys
a = left
s = down
d = right
```

#### 3. Oneshot Layers
```ini
[main]
shift = oneshot(shift)   # Tap Shift, affects only the next key

[shift]
a = A                    # Next key after Shift tap is capitalized
```

---

## Key Bindings

### Basic Key Names

| Type | Examples |
|------|----------|
| **Letters** | `a`, `b`, `c`, `z` |
| **Numbers** | `1`, `2`, `3`, `0` |
| **Modifiers** | `shift`, `ctrl`, `alt`, `meta` |
| **Special** | `space`, `enter`, `tab`, `esc` |
| **Arrows** | `up`, `down`, `left`, `right` |
| **Function** | `f1`, `f2`, `f12` |

### Finding Key Names
Use `keyd monitor` and press keys to see their names:

```bash
sudo keyd monitor
# Press keys to see their names
# Press Ctrl+C to exit
```

### Modifier Combinations
```ini
[main]
a = C-c              # Ctrl+C
b = A-tab            # Alt+Tab  
c = S-f10            # Shift+F10
d = C-A-del          # Ctrl+Alt+Delete
```

**Modifier codes:**
- `C` = Control
- `A` = Alt
- `S` = Shift
- `M` = Meta (Windows key)

---

## Common Actions

### layer(name)
Activate a layer while holding the key:
```ini
[main]
capslock = layer(symbols)

[symbols]
a = !
s = @
d = #
```

### toggle(name)
Turn a layer on/off permanently:
```ini
[main]
f1 = toggle(numpad)      # Press F1 to enable/disable numpad

[numpad]
u = 7
i = 8
o = 9
```

### oneshot(name)
Activate layer for just the next key:
```ini
[main]
shift = oneshot(shift)   # Tap shift, next key is shifted

[shift]
a = A
1 = !
```

### overload(layer, action)
Different action for tap vs hold:
```ini
[main]
space = overload(nav, space)  # Hold = nav layer, Tap = space

[nav]
h = left
j = down
k = up
l = right
```

### macro(text)
Type multiple things with one key:
```ini
[main]
f1 = macro(Hello World!)
f2 = macro(C-c C-v)          # Ctrl+C then Ctrl+V
f3 = macro(username@email.com)
```

### clear()
Turn off all active layers:
```ini
[main]
esc = clear()                # Escape cancels all layers
```

### noop
Disable a key completely:
```ini
[main]
capslock = noop              # Caps Lock does nothing
```

---

## Macros Made Simple

Macros let one key do multiple things in sequence.

### Text Macros
```ini
[main]
f1 = macro(Hello World!)
f2 = macro(john.doe@example.com)
f3 = macro(My Name)
```

### Key Combination Macros
```ini
[main]
f4 = macro(C-c)              # Ctrl+C
f5 = macro(C-a C-c)          # Select all, then copy
f6 = macro(A-tab)            # Alt+Tab
```

### Mixed Macros
```ini
[main]
f7 = macro(Hello space C-enter)    # Type "Hello ", then Ctrl+Enter
f8 = macro(C-l https://google.com enter)  # Ctrl+L, type URL, Enter
```

### Macros with Delays
```ini
[main]
f9 = macro(username tab 100ms password enter)  # Wait 100ms between actions
```

---

## Advanced Features

### Composite Layers
Activate when multiple layers are active:
```ini
[main]
leftalt = layer(alt)
leftctrl = layer(ctrl)

[alt]
# Alt layer bindings

[ctrl]  
# Ctrl layer bindings

[alt+ctrl]              # Only active when BOTH alt AND ctrl are held
del = reboot            # Ctrl+Alt+Del = reboot
```

### Layer with Modifiers
Add automatic modifiers to a layer:
```ini
[main]
capslock = layer(capslock)

[capslock:C]            # This layer adds Ctrl to everything
j = down                # Caps+J = Ctrl+Down (not just Down)
k = up                  # Caps+K = Ctrl+Up
```

### Chords
Multiple keys pressed together:
```ini
[main]
j+k = esc               # Press J and K together = Escape
a+s+d = macro(Hello!)   # Press A, S, D together = type "Hello!"
```

### Aliases
Give keys custom names:
```ini
[aliases]
leftmeta = super
rightmeta = super

[main]
super = layer(nav)      # Now both meta keys work
```

---

## Practical Examples

### Example 1: Vim-style Navigation
```ini
[ids]
*

[main]
capslock = layer(nav)

[nav]
h = left
j = down  
k = up
l = right
u = pageup
d = pagedown
```

### Example 2: Gaming Layer
```ini
[main]
f1 = toggle(gaming)

[gaming]
capslock = shift        # Caps Lock becomes Shift for running
tab = esc              # Tab becomes Escape  
1 = f1                 # Number keys become function keys
2 = f2
3 = f3
```

### Example 3: Programmer Shortcuts
```ini
[main]
rightalt = layer(symbols)

[symbols]
a = !                  # Easy access to symbols
s = @
d = #
f = $
g = %
h = ^
j = &
k = *
l = (
; = )
```

### Example 4: Caps Lock Multi-function
```ini
[main]
capslock = overload(nav, esc)  # Hold = navigation, Tap = Escape

[nav]
h = left
j = down
k = up
l = right
space = enter
```

### Example 5: One-handed Mode
```ini
[main]
space = toggle(mirror)

[mirror]
q = p                  # Mirror right side to left
w = o
e = i
r = u
t = y
# Continue for all keys...
```

---

## Troubleshooting

### Common Issues

#### Config Not Working
1. Check file location: `/etc/keyd/*.conf`
2. Check file has `.conf` extension
3. Test config: `sudo keyd check`
4. Reload: `sudo keyd reload`

#### Wrong Key Names
1. Use `keyd monitor` to find correct names
2. Common mistakes:
   - `ctrl` not `control`
   - `alt` not `option`
   - `meta` not `cmd` or `windows`

#### Keyd Not Starting
```bash
# Check status
sudo systemctl status keyd

# Check logs
sudo journalctl -u keyd

# Restart service
sudo systemctl restart keyd
```

#### Keyboard Becomes Unusable
- **Emergency reset**: Press `Backspace + Escape + Enter` together
- Or restart keyd: `sudo systemctl restart keyd`
- Or edit config file from another computer via SSH

### Debug Mode
```bash
# See what keyd is doing
KEYD_DEBUG=2 sudo keyd -m
```

### Testing Configs
```bash
# Check for errors before applying
sudo keyd check /etc/keyd/myconfig.conf

# Test temporarily
sudo keyd bind 'a = b'  # Make 'a' type 'b'
sudo keyd bind reset    # Undo temporary changes
```

---

## Tips and Best Practices

### Starting Simple
1. Begin with simple key swaps: `capslock = esc`
2. Add one layer at a time
3. Test each change before adding more

### Config Organization
```ini
# Use comments to explain sections
[main]
# Navigation layer
capslock = layer(nav)

# Symbol layer  
rightalt = layer(symbols)

# Keep related bindings together
[nav]
# Vim-style movement
h = left
j = down
k = up  
l = right
```

### Backup Your Configs
```bash
# Backup working config
sudo cp /etc/keyd/default.conf /etc/keyd/default.conf.backup

# Restore if needed
sudo cp /etc/keyd/default.conf.backup /etc/keyd/default.conf
sudo keyd reload
```

### Performance Tips
- Avoid complex nested layers
- Use shorter macro sequences  
- Test with `keyd monitor -t` to check timing

### Common Patterns

#### Home Row Modifiers
```ini
[main]
# Make home row keys act as modifiers when held
a = overload(ctrl, a)
s = overload(alt, s)  
d = overload(shift, d)
f = overload(meta, f)
```

#### Space Cadet Shifts
```ini
[main]
leftshift = overload(shift, 9)    # Hold = Shift, Tap = (
rightshift = overload(shift, 0)   # Hold = Shift, Tap = )
```

#### Leader Key Pattern
```ini
[main]
space = layer(leader)

[leader]
f = macro(firefox)        # Space+F = open Firefox
t = macro(terminal)       # Space+T = open terminal  
e = macro(editor)         # Space+E = open editor
```

### Sharing Configs
Use `include` to share common setups:

```ini
# /etc/keyd/common
[main]
capslock = layer(nav)

[nav]
h = left
j = down
k = up
l = right

# /etc/keyd/laptop.conf  
[ids]
1234:5678

include common

# /etc/keyd/desktop.conf
[ids]
5678:9012

include common
```

---

**Remember**: Start simple, test often, and build complexity gradually. Keyd is powerful but can make your keyboard unusable if misconfigured. Always keep the emergency reset sequence in mind: **Backspace + Escape + Enter**.

Happy key mapping! 🎹
