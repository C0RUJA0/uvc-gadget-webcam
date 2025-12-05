# uvc-gadget-webcam

This project provides everything needed to install all the software necessary for a working Raspberry Pi UVC gadget webcam that can be used as a video source and streamed through RTMP.
These instructions have been adapted and tested on the Raspberry Pi Zero 2 W (Bookworm, arm64 lite).

## Adjustments and installation (README summary)

### 1. Recommended operating system

Install this image on the Raspberry Pi Zero 2 W:

`2023-10-10-raspios-bookworm-arm64-lite.img.xz`

### 2. Required packages (Raspberry Pi)

Update and install dependencies:

```bash
sudo apt update
sudo apt install \
  git \
  ffmpeg \
  v4l2loopback-dkms \
  v4l-utils \
  python3 \
  python-is-python3 \
  build-essential
```

### 3. Clone and install the project

Clone the original repository:

```bash
git clone https://github.com/Serialbocks/uvc-gadget-webcam.git
cd uvc-gadget-webcam
sudo chmod +x install.sh
sudo ./install.sh
```

Reboot the Raspberry Pi after installation:

```bash
sudo reboot
```

## Optional: change the camera name shown on the host (Windows/macOS)

By default, the UVC gadget function is created with a default name stored in the file `function_name` inside the UVC function directory. Many hosts (including Windows) use this value as the camera name (for example, `UVC Camera`).
To force a custom name (for example, `PiSight`), adjust the script that creates the UVC gadget.

### 4.1 Edit the `rpi-uvc-gadget.sh` script

On the Raspberry Pi:

```bash
cd /opt/uvc-gadget-webcam
sudo nano rpi-uvc-gadget.sh
```

Locate the `create_uvc()` function. You should see a block similar to:

```sh
create_uvc () {
  # Example usage:
  # create_uvc config/c.1 uvc.0
  CONFIG=$1
  FUNCTION=$2

  echo "Initializing v4l2loopback"
  modprobe v4l2loopback devices=1 video_nr=1 max_buffers=32 exclusive_caps=1 card_label="VirtualCam #0"
  /opt/uvc-gadget-webcam/v4l2loopback-ctl set-caps /dev/video1 "YUYV:640x360@30"

  echo " Creating UVC gadget functionality : $FUNCTION"
  mkdir functions/$FUNCTION
  # other configuration steps...
```

Immediately after the line:

```sh
mkdir functions/$FUNCTION
```

add the line that writes the desired function name:

```sh
  echo " Creating UVC gadget functionality : $FUNCTION"
  mkdir functions/$FUNCTION
  echo -n "PiSight" > functions/$FUNCTION/function_name
```

If you prefer another name, replace `"PiSight"` with the desired text (avoid accents and special characters).

Save and close the file.

### 4.2 Recreate the gadget to apply the change

Stop the service and the gadget:

```bash
sudo systemctl stop uvc-gadget-webcam.service
sudo /opt/uvc-gadget-webcam/rpi-uvc-gadget.sh stop 2>/dev/null || true
```

Confirm that the gadget directory was removed:

```bash
ls /sys/kernel/config/usb_gadget
```

If nothing is listed, the gadget was removed successfully.

Create the gadget again and restart the service:

```bash
sudo /opt/uvc-gadget-webcam/rpi-uvc-gadget.sh start
sudo systemctl start uvc-gadget-webcam.service
```

### 4.3 Verify the function name on the Pi

```bash
G=/sys/kernel/config/usb_gadget
cd "$G"/g1/functions/uvc.0 2>/dev/null || cd "$G"/*/functions/uvc.0

echo "function_name:"
cat function_name
```

Expected output:

```text
function_name:
PiSight
```

### 4.4 Test on the host computer

Disconnect and reconnect the USB cable between the Raspberry Pi and the host computer.

On Windows, open Device Manager and check under “Cameras”: the device should now appear with the name defined in `function_name` (for example, `PiSight`), instead of the generic `UVC Camera`.
On macOS, the new name should also appear in applications that list video devices.
