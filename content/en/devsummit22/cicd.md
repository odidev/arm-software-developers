---
title: "Manage development in a CI/CD workflow"
linkTitle: "CI/CD workflow"
type: docs
toc_hide: true
hide_summary: true
description: >
    Manage development in a CI/CD workflow using GitHub Actions.
---
## Overview

In this section we will learn how to manage ongoing development in an automated CI/CD workflow, using [GitHub Actions](https://github.com/features/actions).

## Pre-requisites

* Arm Virtual Hardware instance with Matter applications built as per [previous section](/devsummit22/build).
* Active GitHub account to host repository

## Detailed Instructions

In the `lighting-app` console, stop the app with `Ctrl+C`.

### Create GitHub self-hosted runner

Browse to the forked Matter repository in your personal GitHub space.\
Navigate to `Settings` > `Actions` > `Runners`, and then click on `New self-hosted runner`.\
Configure for `Linux` host on `ARM64` Architecture (this is the configuration of the Virtual Raspberry Pi 4 instance).\
You will see a set of commands (unique to you) to download and configure the `runner` on the (`lighting-app`) Raspberry Pi 4 instance.

In the `lighting-app` console, return to your home directory:
```console
cd /home/pi
```
Then copy and paste the download and configure commands for the `runner`.\
It is OK to select the default options when prompted during configuration.

After configuration, to keep the `CONSOLE` free, log into the Raspberry Pi 4 via the CLCD screen, and launch the runner from there:
```console
cd actions-runner
./run.sh
```
When launched, you should see a message similar to:
```
TIMESTAMP: Listening for Jobs
```
### Make a code change

Return to the `lighting-app/linux` folder:
```console 
cd connectedhomeip/examples/lighting-app/linux
```
We will change the output message when light is toggled. This is set in `on-off-server.cpp`.\
Open in text editor, such as `nano`:
```console
nano ../../../src/app/clusters/on-off-server/on-off-server.cpp
```
Locate the `Toggle on/off` message (circa line 137), and edit to give a new output message, for example:
```C
    emberAfOnOffClusterPrintln("HELLO WORLD Toggle on/off from %x to %x", currentValue, newValue);
```
Rebuild:
```console
gn gen out/debug
ninja -C out/debug
```
and re-run application:
```console
./out/debug/chip-lighting-app
```
When `chip-lighting-app` is initialized, toggle the light with your `chip-tool` instance:
```console
./out/debug/chip-tool onoff on 0x11 1
./out/debug/chip-tool onoff off 0x11 1
```
Observer your new message in the `chip-lighting-app` log:
```
[TIMESTAMP][INSTANCEID] CHIP:ZCL: HELLO WORLD Toggle on/off from 1 to 0
```
When satisfied, kill `chip-lighting-app` with `Ctrl+C`.

### To do

Create yaml with self-hosted runner instructions

Verify you are using the forked repository
```console
connectedhomeip/examples/lighting-app/linux
```
To automatically kill app after (say) 60s:\
`timeout 60s ./out/debug/chip-lighting-app`

Does the runner need to pull everything again (all that matter setup??), or can it just do a rebuild on the existing project?
```console
cd ../../connectedhomeip/examples/lighting-app/linux
git pull
gn gen out/debug
ninja -C out/debug
timeout 60s ./out/debug/chip-lighting-app
```

## Next Steps

[<-- Return to Workshop Home](/devsummit22/#sections)