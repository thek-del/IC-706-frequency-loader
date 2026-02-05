# Radio Frequency Loader (IC-706MKIIG)

A lightweight Bash utility designed to streamline the manual programming of **ICOM IC-706MKIIG** memory channels. By leveraging the Hamlib (`rigctl`) library, this script automates the tuning process via CAT protocol, acting as a smart assistant to reduce input errors.

## ⚠️ Important: How it works
This script **does not** write data directly into the radio's permanent memory (EEPROM). Instead, it acts as a **Tuning Assistant**:

1. It automatically sets the **Memory Channel** and the **VFO Frequency** for you.
2. It **stops** and waits for the user.
3. This allows the operator to manually set specific parameters (like CTCSS tones, offsets, or memory names) on the radio's front panel without the risk of mistyping the main frequency.
4. Once you have manually saved the channel on the radio, press **Enter** on your PC to move to the next entry in your CSV.

This hybrid approach significantly reduces manual entry errors while keeping the operator in full control of the radio's specific settings.

## 📋 Prerequisites

* **Operating System**: Linux
* **Hardware**: CI-V CAT Interface (USB-to-Serial) connected to the ICOM IC-706MKIIG.
* **Software**: `hamlib` library installed.
    * To install on Debian/Ubuntu: `sudo apt install libhamlib-utils`

## ⚙️ Configuration

Before running the script, verify the following parameters inside the `.sh` file:

* **MODEL**: Default is set to `3011` (Run `rigctl -l` to see the full list of supported models).
* **DEVICE**: The path to your USB converter (e.g., `/dev/serial/by-id/...`).

## 📄 CSV File Format

The script requires a `.csv` file with a header row. The format is: `Memory,Name,Frequency,Shift,Tone`.

**Example `repeaters.csv`:**
```
Memory  Name   Frequency  Shift  Tone
1       RV-1a  145600000  -600   118.8
2       R5     145625000  -600   
3       RU7    430025000  +1600  
4       RU15   433500000  +1600  88.5
```

**Notes:**

* **Frequency**: Must be expressed in **Hertz** (e.g., 145.600 MHz becomes `145600000`).
* **Informational Fields**: The `Name`, `Shift`, and `Tone` fields **cannot** be set automatically on the radio by this script; they serve as a visual reference for the user to facilitate manual entry.
* **Comments**: Lines starting with `#` will be ignored.

## 🚀 How to use

1.  **Make the script executable**:
    ```bash
    chmod +x rfloader
    ```

2.  **Run the script**:
    You can run the script by simply passing the CSV file, or by using the **Range Option** (`-l`) to process only specific memory slots.

    **Basic usage (all rows):**
    ```bash
    ./rfloader repeaters.csv
    ```

    **Start from a specific memory:**
    ```bash
    ./rfloader -l 70 repeaters.csv
    ```

    **Process a specific range (e.g., from 70 to 75):**
    ```bash
    ./rfloader -l 70 75 repeaters.csv
    ```

    *Note: The script is flexible; the `-l` flag can be placed either before or after the filename.*

3.  **Operation Flow**:
    * The script tunes the radio to the specified memory slot and frequency.
    * If a range is specified, it will skip all channels outside that range and stop automatically once the upper limit is exceeded.
    * It pauses and waits for the **[Enter]** key.
    * During the pause, you can manually set the offset, sub-audio tones, or memory name on the radio's front panel and perform the final save.
    * Press **Enter** on your keyboard to proceed to the next row.

## ⚠️ Troubleshooting

If the radio does not respond:
* Verify that the **CI-V address** in the radio's menu is set to the default (**58h**).
* Ensure the **baud rate** is consistent with your system's serial port settings.
