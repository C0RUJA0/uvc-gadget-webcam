# uvc-gadget-webcam

This project provides all the components required to install and configure the software for a working Raspberry Pi Zero 2 W UVC gadget webcam, whose video output can be used as a source and streamed via RTMP.

## 1. Recommended operating system

Install the following image on the Raspberry Pi Zero 2 W:

- `2023-10-10-raspios-bookworm-arm64-lite.img.xz`

Use the standard Raspberry Pi Imager or an equivalent tool to write the image to the microSD card.

## 2. Required packages (Raspberry Pi)

Update the package lists and install the required dependencies:

```bash
sudo apt update
sudo apt install git ffmpeg v4l2loopback-dkms v4l-utils python3 python-is-python3 build-essential
