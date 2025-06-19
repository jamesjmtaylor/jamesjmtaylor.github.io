---
title: AAR pt 13 (Bluetooth)
date: '2019-06-01T08:32:00-07:00'
tags: ble android aar
---
![Bluetooth icon](/assets/bluetooth-icon.png)

If you have not had a chance to read the first entry in the series for context, [you can do so here](/after-action-review-aar/).

Since I've been at Nautilus I've had to work with Bluetooth and the Internet of Things more closely than I have in any other point in my career.  To make sure that I could speak at least somewhat cogently about the topics I've done a fair amount of study into both.  This post focuses on my learnings about Bluetooth specifically.

* Bluetooth is analogous to a wired connection.  Only one smartphone can be actively connected to a Bluetooth device at a time.
* A Bluetooth profile is a standardized type of device communication.  Keyboard, mouse, and speakers are all examples of standard Bluetooth profiles.  There are 28 profiles in total.  You can opt to not use a standard Bluetooth profile in developing your device, but that means that only your applications will be able to interface with that device.
* Until Android P you could only connect to one device per Bluetooth profile.  Even with Android P you can only stream data to one connected device per profile.
* The android BLE interface uses error codes in its ScanCallback API to report common errors.  Codes include 1 (scan already started) 2 (app not registered) 3 (feature unsupported) and 4 (internal error).
* In Android you can only scan for Bluetooth LE devices or scan for Classic Bluetooth devices. You cannot scan for both Bluetooth LE and classic devices at the same time.
* You can debug raw Bluetooth scanning in Android studio with the command "adb shell dumpsys -l" to see Bluetooth services and then execute "adb shell dumpsys bluetooth-manager" to see all the available debugging information.
* All interactions with Bluetooth devices are through Android's BluetoothAdapter class.  BluetoothProfiles also expose Headset, A2DP, and HDP.
* Bluetooth has the following advantages over wifi:

1. It emits weak signals.  This means that it consumes an order of magnitude less energy and has significantly less interference.
2. Bluetooth signals, once paired, use frequency hopping to reduce interference even further.  This also indirectly makes Bluetooth connections more secure.  In the Army we used frequency hop on our HF radios for roughly the same purpose; it increased jamming resistance and made broadcasts more difficult to intercept.
3. Bluetooth can create self-healing mesh networks that adapt to the loss of a single device.
4. Setup is extremely simple compared to wifi, usually consisting of a single button press.

* Bluetooth connections are configured into master-slave relationships.  A master can have up to 7 slaves.  Roles can also be reversed at will and a master can simultaneously be a slave to another device.
* BT5 is the latest iteration of bluetooth (2019).  BT4 was the first generation that qualified as BLE (Bluetooth Low Energy).
* In Linux, the canonical implementation of the Bluetooth protocol stack is BlueZ
* With Android Things apps are launched automatically on startup to immerse your users in the app experience. Devices expose only one app to users, instead of multiple like with mobile devices. This is ideal for a Bluetooth IoT device.  
* A System On a Module (SOM) is a board-level circuit that integrates a system function into a single module. A module is an independent component that can be used as part of a more complex system. Unlike a single-board computer, a SOM serves a special function.  A System On a Chip (SOC) is just a subclass of a SOM.
* iOS manages Bluetooth with the CoreBluetooth library.  It provides interactions between peripherals and centrals, enabling reception of UUIDs, RSSI, Transmission power, and characteristics.
* The GATT profile is the general specification for sending and receiving short pieces of data known as "attributes" over a BLE link. All current Low Energy application profiles are based on GATT.
* Connecting to a Bluetooth device should be done by an Android Activity.  Once the connection is established however you should use a Service to retain the Bluetooth GATT connection.  You can manage the service object through binding and unbinding as well as a persistent foreground notification.
* If you use a bound service pattern you can call your service methods directly for results.  The alternative is to use an intent broadcaster pattern to communicate between the activity and the service.
* According to wikipedia "The Logical Link Control and Adaptation Layer Protocol (L2CAP) is layered over the Baseband Protocol and resides in the data link layer. L2CAP provides connection-oriented and connectionless data services to upper layer protocols with protocol multiplexing capability, segmentation and reassembly operation, and group abstractions. L2CAP permits higher level protocols and applications to transmit and receive L2CAP data packets up to 64 kilobytes in length.  Those higher level protocols include SDP (Protocol used to find other Bluetooth devices within the communication range, also responsible for detecting the function of devices in range), RFCOMM (Protocol used to emulate serial port connections) and TCS (Telephony control protocol).  The Bluetooth profiles use those protocols as their means of transmission." What that means is that all L2CAP does is reassemble the packets. Connection oriented means pairing devices.  Connectionless means packets that are just periodically broadcast.  Connectionless is used exclusively for BLE beacons that just broadcast their UUID and maybe a url."

Image obtained with permission from https://www.deviantart.com/martz90
