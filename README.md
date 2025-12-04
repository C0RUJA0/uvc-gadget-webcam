# uvc-gadget-webcam

This project provides all the components required to install and configure the software for a Raspberry Pi configured as a USB OTG UVC gadget webcam, whose video output can be used as a source and streamed via RTMP/RTSP.

## 1. Recommended operating system

Install the following image on the Raspberry Pi:

* `2023-10-10-raspios-bookworm-arm64-lite.img.xz`

## 2. Required packages (Raspberry Pi)

Update the package lists and install the required dependencies:

```bash
sudo apt update
sudo apt install git ffmpeg v4l2loopback-dkms v4l-utils python3 python-is-python3 build-essential
```

## 3. Clone and install the project

Clone the original repository and run the installation script:

```bash
git clone https://github.com/Serialbocks/uvc-gadget-webcam.git
cd uvc-gadget-webcam
sudo chmod +x install.sh
sudo ./install.sh
```

After the installation finishes, reboot the Raspberry Pi:

```bash
sudo reboot
```
