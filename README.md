# ESP32-C3 DFPlayer Smart Doorbell

A Home Assistant integrated doorbell system using ESP32-C3 and DFPlayer Mini to play custom MP3 sounds triggered by wireless buttons or automations.

![ESPHome](https://img.shields.io/badge/ESPHome-000000?style=for-the-badge&logo=esphome&logoColor=white)
![Home Assistant](https://img.shields.io/badge/Home%20Assistant-41BDF5?style=for-the-badge&logo=home-assistant&logoColor=white)
![ESP32](https://img.shields.io/badge/ESP32-E7352C?style=for-the-badge&logo=espressif&logoColor=white)

## 🛠️ Hardware Requirements

| Component | Specification | Quantity | Notes |
|-----------|--------------|----------|-------|
| ESP32-C3 Super Mini | ESP32-C3 development board | 1 | Any ESP32-C3 board works |
| DFPlayer Mini | MP3 player module | 1 | [Datasheet](https://wiki.dfrobot.com/DFPlayer_Mini_SKU_DFR0299) |
| MicroSD Card | ≤32GB, FAT32 | 1 | Must be formatted as FAT32 |
| Speaker | 3W passive, 4-8Ω | 1 | Or use headphones for testing |
| Power Supply | 5V 1A | 1 | 5V 2A recommended for higher volume |
| Jumper Wires | Male-to-Female | 4 | For connections |

### Where to Buy
- **ESP32-C3 Super Mini:** AliExpress, Amazon (~$2-4)
- **DFPlayer Mini:** AliExpress, Amazon (~$1-3)
- **MicroSD Card:** Any electronics store (8-32GB)
- **Speaker:** Old computer speakers, salvaged speakers, or new 3W 4Ω speakers (~$1-2)

## 📐 Wiring Diagram

```
ESP32-C3 Super Mini      DFPlayer Mini
====================     =============
GPIO20 (TX)      ───→    RX
GPIO21 (RX)      ←───    TX
5V               ───→    VCC
GND              ───→    GND

DFPlayer Mini            Speaker
=============            =======
SPK_1            ───→    + (positive)
SPK_2            ───→    - (negative)
```

### Important Notes
- ⚠️ **TX goes to RX, RX goes to TX** (crossed connection)
- ⚠️ Use **5V** pin for DFPlayer VCC (not 3.3V)
- ⚠️ Connect **all grounds** together (common ground)
- ℹ️ For testing, you can use headphones instead of a speaker

## 💾 SD Card Setup

### 1. Format SD Card
- **File System:** FAT32 (not exFAT or NTFS)
- **Allocation Unit Size:** Default (or 32KB)
- **Capacity:** Use ≤32GB cards (DFPlayer doesn't support >32GB well)

### 2. Create Folder Structure
```
SD Card Root/
└── mp3/
    ├── 0001.mp3
    ├── 0002.mp3
    ├── 0003.mp3
    ├── 0004.mp3
    ├── 0005.mp3
    ├── 0006.mp3
    ├── 0007.mp3
    └── 0008.mp3
```

### 3. File Naming Rules
- ✅ Files **must** be in a folder called `mp3` at the root of SD card
- ✅ File names **must** have 4 digits: `0001.mp3`, `0002.mp3`, etc.
- ✅ You can add text after the number: `0001-doorbell.mp3`, `0002_chime.mp3`
- ✅ Files are referenced by number only (1, 2, 3...)

### 4. Recommended MP3 Settings
- **Format:** MP3 (not AAC, FLAC, WAV, or other formats)
- **Bitrate:** 128 kbps or lower (64-128 kbps ideal)
- **Sample Rate:** 44.1 kHz or 48 kHz
- **Channels:** Mono or Stereo (both work)

### 5. Finding Doorbell Sounds
Free doorbell sound sources:
- [Freesound.org](https://freesound.org/) - Search "doorbell"
- [Zapsplat.com](https://www.zapsplat.com/) - Free sound effects
- YouTube (convert to MP3, respect copyright)
- Create your own recordings

## 🚀 Installation

### Prerequisites
- [ESPHome](https://esphome.io/guides/installing_esphome.html) installed
- [Home Assistant](https://www.home-assistant.io/installation/) running
- USB cable (data capable, not charge-only)

### Step 1: Clone Repository
```bash
git clone https://github.com/yourusername/esp32-dfplayer-doorbell.git
cd esp32-dfplayer-doorbell
```

### Step 2: Configure WiFi
Create `secrets.yaml` in your ESPHome directory:
```yaml
wifi_ssid: "YourWiFiSSID"
wifi_password: "YourWiFiPassword"
```

### Step 3: Generate API Key
Generate a secure encryption key:
```bash
openssl rand -base64 32
```

Copy the output and paste it in the configuration file where it says `<insert your key here>`.

### Step 4: Flash ESP32-C3

**First time flash (USB required):**
```bash
esphome run dfplayerdevice.yaml
```

**Subsequent updates (OTA):**
```bash
esphome run dfplayerdevice.yaml --device dfplayerdevice.local
```

### Step 5: Prepare Hardware
1. Wire ESP32-C3 to DFPlayer as shown in wiring diagram
2. Format SD card as FAT32
3. Create `mp3` folder on SD card
4. Copy your MP3 files (0001.mp3, 0002.mp3, etc.)
5. Insert SD card into DFPlayer
6. Connect speaker to DFPlayer SPK_1 and SPK_2
7. Power on (via USB or 5V supply)

### Step 6: Add to Home Assistant
1. Go to **Settings → Devices & Services**
2. ESPHome integration should auto-discover the device
3. Click **Configure** and enter the encryption key
4. Device will appear with all entities

## 🎮 Usage

### Available Entities in Home Assistant

**Buttons (Press to play instantly):**
- `button.play_sound_1` - Plays 0001.mp3
- `button.play_sound_2` - Plays 0002.mp3
- `button.play_sound_3` - Plays 0003.mp3
- `button.play_sound_4` - Plays 0004.mp3
- `button.play_sound_5` - Plays 0005.mp3
- `button.play_sound_6` - Plays 0006.mp3
- `button.play_sound_7` - Plays 0007.mp3
- `button.play_sound_8` - Plays 0008.mp3
- `button.stop_playback` - Stops current playback

**Controls:**
- `number.volume` - Volume slider (0-30)

**Services:**
- `esphome.dfplayerdevice_play_mp3` - Play any file by number
- `esphome.dfplayerdevice_set_volume` - Set volume programmatically
- `esphome.dfplayerdevice_stop_playback` - Stop playback

### Quick Test

1. Open Home Assistant
2. Go to **Developer Tools → Services**
3. Select service: `button.press`
4. Select target: `button.play_sound_1`
5. Click **Call Service**

If you hear sound → **Success!** 🎉

## 🔔 Automation Examples

### Basic Wireless Button

```yaml
automation:
  - alias: "Doorbell Ring"
    trigger:
      - platform: state
        entity_id: binary_sensor.wireless_doorbell_button
        to: "on"
    action:
      - service: button.press
        target:
          entity_id: button.play_sound_1
```

### Multiple Doors - Different Sounds

```yaml
automation:
  # Front door - Sound 1
  - alias: "Front Door Doorbell"
    trigger:
      - platform: state
        entity_id: binary_sensor.front_door_button
        to: "on"
    action:
      - service: button.press
        target:
          entity_id: button.play_sound_1

  # Back door - Sound 2
  - alias: "Back Door Doorbell"
    trigger:
      - platform: state
        entity_id: binary_sensor.back_door_button
        to: "on"
    action:
      - service: button.press
        target:
          entity_id: button.play_sound_2

  # Gate - Sound 3
  - alias: "Gate Doorbell"
    trigger:
      - platform: state
        entity_id: binary_sensor.gate_button
        to: "on"
    action:
      - service: button.press
        target:
          entity_id: button.play_sound_3
```

### Time-Based Volume Control

```yaml
automation:
  - alias: "Doorbell - Quiet at Night"
    trigger:
      - platform: state
        entity_id: binary_sensor.doorbell_button
        to: "on"
    action:
      - choose:
          # Night time (10 PM - 7 AM) - Quiet
          - conditions:
              - condition: time
                after: "22:00:00"
                before: "07:00:00"
            sequence:
              - service: esphome.dfplayerdevice_set_volume
                data:
                  volume: 12
              - service: button.press
                target:
                  entity_id: button.play_sound_2
        # Day time - Normal volume
        default:
          - service: esphome.dfplayerdevice_set_volume
            data:
              volume: 25
          - service: button.press
            target:
              entity_id: button.play_sound_1
```

### With Mobile Notification

```yaml
automation:
  - alias: "Doorbell with Notification"
    trigger:
      - platform: state
        entity_id: binary_sensor.doorbell_button
        to: "on"
    action:
      - parallel:
          # Play doorbell sound
          - service: button.press
            target:
              entity_id: button.play_sound_1
          
          # Send notification to phone
          - service: notify.mobile_app_your_phone
            data:
              title: "🔔 Doorbell"
              message: "Someone is at the front door!"
              data:
                ttl: 0
                priority: high
```

### Flash Lights When Doorbell Rings

```yaml
automation:
  - alias: "Doorbell - Flash Lights"
    trigger:
      - platform: state
        entity_id: binary_sensor.doorbell_button
        to: "on"
    action:
      - parallel:
          # Play sound
          - service: button.press
            target:
              entity_id: button.play_sound_1
          
          # Flash entrance light
          - service: light.turn_on
            target:
              entity_id: light.entrance
            data:
              flash: short
```

### Play Random Sound

```yaml
automation:
  - alias: "Doorbell - Random Sound"
    trigger:
      - platform: state
        entity_id: binary_sensor.doorbell_button
        to: "on"
    action:
      - service: esphome.dfplayerdevice_play_mp3
        data:
          file_number: "{{ range(1, 9) | random }}"
```

### Disable Doorbell When Away

```yaml
automation:
  - alias: "Doorbell - Only When Home"
    trigger:
      - platform: state
        entity_id: binary_sensor.doorbell_button
        to: "on"
    condition:
      - condition: state
        entity_id: person.home_owner
        state: "home"
    action:
      - service: button.press
        target:
          entity_id: button.play_sound_1
```

### Play Specific MP3 by Number

```yaml
automation:
  - alias: "Play Custom Sound"
    trigger:
      - platform: state
        entity_id: input_button.special_doorbell
        to: "on"
    action:
      - service: esphome.dfplayerdevice_play_mp3
        data:
          file_number: 5  # Plays 0005.mp3
```

## 🔧 Troubleshooting

### No Sound Playing

**Check these first:**
1. ✅ SD card formatted as FAT32 (not exFAT)
2. ✅ Files in `/mp3/` folder (not root directory)
3. ✅ File names: `0001.mp3`, `0002.mp3` (exactly 4 digits)
4. ✅ Volume set to 20+ (try maximum: 30)
5. ✅ Speaker connected to SPK_1 and SPK_2
6. ✅ Power supply: 5V 1A minimum
7. ✅ Wiring: TX→RX, RX→TX (crossed!)

**Check logs:**
```bash
esphome logs dfplayerdevice.yaml
```

Look for:
- `Playing file X in mp3 folder` ✅ Good
- `Received unknown cmd` ℹ️ Normal, ignore
- No UART messages ❌ Check wiring

### Can't Flash ESP32-C3

**Solutions:**
1. Hold **BOOT** button while connecting USB
2. Try different USB cable (data cable, not charge-only)
3. Install [CP210x](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers) or [CH340](http://www.wch-ic.com/downloads/CH341SER_EXE.html) drivers
4. Use different USB port (preferably USB 2.0)
5. Check Device Manager (Windows) or `dmesg` (Linux) for device

### DFPlayer Not Responding

**Check:**
1. SD card properly inserted
2. SD card ≤32GB
3. 5V power (not 3.3V)
4. TX/RX not swapped

**Try:**
1. Re-format SD card (FAT32)
2. Try different SD card
3. Test with different MP3 files
4. Check for bent pins on DFPlayer

### Wireless Button Not Triggering

**Verify:**
1. Button entity exists in Home Assistant
2. Automation is enabled (check toggle switch)
3. Button entity changes state when pressed
4. Correct entity ID in automation

**Debug:**
1. Go to **Developer Tools → States**
2. Find your button entity
3. Watch state change when button pressed
4. Check **Settings → Automations → Your Automation → Traces**

### Sound Cuts Out or Distorted

**Possible causes:**
1. Weak power supply - Use 5V 2A adapter
2. Bad SD card - Try different card
3. Corrupted MP3 files - Re-download/convert
4. Speaker impedance too low - Use 4-8Ω speaker
5. Volume too high - Reduce to 20-25

### Web Interface Not Loading

**Check:**
1. Device connected to WiFi (check router)
2. Correct IP address
3. Port 80 not blocked by firewall
4. Try: `http://dfplayerdevice.local`

## 📊 Technical Specifications

### Performance
- **Latency:** <100ms from trigger to sound
- **Power Consumption:** ~150mA idle, ~300mA playing
- **WiFi Range:** Typical ESP32 range (~50m)
- **Boot Time:** ~5 seconds
- **Max Files:** 9999 MP3 files supported

### Audio Quality
- **Output:** 3W maximum via speaker amplifier
- **Frequency Response:** 20Hz-20kHz
- **DAC:** 24-bit

### Compatibility
- **ESPHome:** 2024.2.0+
- **Home Assistant:** 2024.1.0+
- **ESP32-C3:** Arduino framework

## ⚠️ Known Limitations

### White Noise
The DFPlayer Mini produces a low-level hum/white noise when powered on. This is a hardware limitation of the amplifier circuit.

**Impact:**
- Barely audible from 1+ meter away
- Only noticeable in very quiet rooms
- Does not affect playback quality
- Does not damage hardware

**Workaround:** Most users simply accept it. Alternative: Use better quality audio module (e.g., DFPlayer Pro).

### SD Card Compatibility
- Maximum 32GB supported
- Must be FAT32 (not exFAT)
- Some SD cards may not work (try different brand)
- Older/slower cards work better than high-speed cards

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [ESPHome](https://esphome.io/) - Amazing ESP32 framework
- [Home Assistant](https://www.home-assistant.io/) - Best home automation platform
- DFPlayer Mini [Datasheet](https://wiki.dfrobot.com/DFPlayer_Mini_SKU_DFR0299)
- ESP32-C3 Community

## 📧 Support

- **Issues:** [GitHub Issues](https://github.com/yourusername/esp32-dfplayer-doorbell/issues)
- **Discussions:** [GitHub Discussions](https://github.com/yourusername/esp32-dfplayer-doorbell/discussions)
- **Home Assistant Community:** [Community Forum](https://community.home-assistant.io/)

## 🌟 Star History

If you find this project useful, please consider giving it a star! ⭐

---

**Made with ❤️ for the Home Assistant Community**
