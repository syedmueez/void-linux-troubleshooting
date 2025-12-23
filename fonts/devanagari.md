````markdown
# Void Linux Devanagari / Indic Fonts Fix

This guide helps fix the issue of **Devanagari (Hindi / Marathi / Sanskrit) fonts not showing** on Void Linux. It includes step-by-step troubleshooting, font installation, and configuration to make your system render Indic scripts correctly, similar to Windows or Manjaro.

---

## Problem

On a fresh Void Linux installation:

- English works fine.  
- Urdu/Arabic may work depending on installed fonts.  
- **Devanagari (Hindi / Marathi / Sanskrit)** appears as □□□□, ?, or broken symbols.  
- This affects:  
  - Terminal  
  - Browser  
  - Editors  
  - System UI  

---

## Root Cause

Void Linux does **not include Indic fonts by default**.  

- Packages like `noto-fonts` or `lohit-devanagari` are **not available as a single meta-package**.  
- Windows and Manjaro automatically provide these fonts.  
- Void requires manual font installation and configuration.

---

## Step 1 — Verify Locale

Check your current locale:

```bash
locale
````

Expected output:

```
LANG=en_US.UTF-8
LC_ALL should be empty or UTF-8
```

If not set, add the following to `~/.bashrc` or `~/.profile`:

```bash
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

Then source the file or log out/in.

---

## Step 2 — Find Correct Font Packages

Search for available Noto fonts in Void:

```bash
xbps-query -Rs noto
```

**Note:**

* Devanagari fonts are **NOT** in `noto-fonts-cjk`.
* Devanagari fonts **ARE** in `noto-fonts-ttf-extra`.

---

## Step 3 — Install Required Fonts

Install all necessary fonts for Devanagari and other scripts:

```bash
sudo xbps-install -S \
  noto-fonts-ttf \
  noto-fonts-ttf-extra \
  noto-fonts-emoji \
  dejavu-fonts-ttf
```

These packages cover most scripts and emojis to ensure proper rendering system-wide.

---

## Step 4 — Rebuild Font Cache

After installation, rebuild the font cache:

```bash
fc-cache -rfv
```

---

## Step 5 — Verify Devanagari Fonts

Check if Devanagari fonts are installed:

```bash
fc-list | grep -i devanagari
```

Expected output includes entries like:

```
/usr/share/fonts/noto/NotoSansDevanagari-Regular.ttf
```

---

## Step 6 — Force Font Fallback for Browsers & Terminal

Some applications may still not render Devanagari correctly. Force a fallback by creating a font configuration file:

```bash
mkdir -p ~/.config/fontconfig

cat > ~/.config/fontconfig/fonts.conf << 'EOF'
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>

  <match>
    <test name="lang" compare="contains">
      <string>hi</string>
    </test>
    <edit name="family" mode="prepend">
      <string>Noto Sans Devanagari</string>
    </edit>
  </match>

  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>Noto Sans</family>
      <family>Noto Sans Devanagari</family>
    </prefer>
  </alias>

</fontconfig>
EOF
```

---

## Step 7 — Rebuild Cache Again

After creating `fonts.conf`, rebuild the font cache again:

```bash
fc-cache -rfv
```

---

## Step 8 — Test

Test Devanagari rendering in terminal:

```bash
echo "English हिन्दी اردو 😀"
```

Test in browser:

* Open a website with Hindi/Devanagari content.
* Example: हिन्दी भाषा परीक्षण

You should now see correct rendering.

---

## Step 9 — Logout / Login

No reboot is needed. A **logout/login** ensures all applications pick up the new fonts.

---

## Result

After completing the steps:

* **Devanagari fonts work system-wide**: terminal, browser, editors, UI.
* System stability and font rendering similar to **Windows or Manjaro**.
* UTF-8 and Unicode scripts display properly.

---

### Notes

* This guide is **Void-specific**. Other distributions may include Indic fonts automatically.
* Use `fc-list` to verify fonts at any time.
* Keep `noto-fonts-ttf-extra` updated for latest script support.

---

**End of guide**

```
