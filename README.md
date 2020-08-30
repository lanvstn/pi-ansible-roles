# Ansible roles for my Raspberry Pi

I've set up my Raspberry Pi using Ansible.
This repository contains some roles for others to re-use.

Some stuff in there may still be a work in progress so carefully read the sections in this README for the roles you wish to use.

## Quickstart

Prerequisites:

* a Raspberry Pi 3 or similar
* a (preferably clean) install of [Raspberry Pi OS Lite (Raspbian)](https://www.raspberrypi.org/downloads/raspberry-pi-os/)
* to be able to connect to it through SSH (connect a screen/keyboard and `systemctl enable ssh`)
  - you should set up public key authentication
* Wifi should be disabled on your Pi for optimal Bluetooth performance
* Ansible on your computer

Create a `hosts.ini` file with the local address of your Pi, like so:

```
[pi]
192.168.0.101
```

In `play.yml`, make sure only the roles you want to run are listed.

**Warning:** The following roles require extra steps:

* spotify-connect

You can now run ansible like so:

```
$ ansible-playbook play.yml
```

## bluetooth-audio

Sets up your Pi as a Bluetooth speaker.

It will be discoverable with the hostname of the device. One problem with this setup remains for now, and that is that you will have to manually trust every new device that connects for the first time.

To do this, inside a shell in your pi:

1. run `bluetoothctl`
1. try to connect using your phone/laptop
1. monitor the output for the address of the device
1. do one of the following:
  - trust the device by entering `trust FF:FF:FF:FF:FF:FF` (with your device address)
  - OR: answer `yes` to the `Authorize service` prompt, but only to the one starting with `0000110d-` (this is A2DP). Sometimes this works better than the trust step depending on the device. But you have to do this on every connection...

I am looking to improve this soon, by adding a custom agent that will auto-answer these prompts.

## spotify-connect

To get this to work you need a `librespot` binary inside the `bin/` folder in the root of this repository. It is not included, you will have to build it yourself.

* [librespot github repo](https://github.com/librespot-org/librespot)
* [cross compilation instructions](https://github.com/librespot-org/librespot/wiki/Cross-compiling)

At the time of writing the raspberry pi instructions did not work anymore but the normal `armhf` target should work fine.

For the lazy:

```sh
# In the librespot repo:
docker build -t librespot-cross -f contrib/Dockerfile .

docker run -v /tmp/librespot-build:/build librespot-cross cargo build --release --target arm-unknown-linux-gnueabihf --no-default-features --features alsa-backend

# In this repo
mkdir -p bin

cp /tmp/librespot-build/arm-unknown-linux-gnueabihf/release/librespot bin/librespot
```
