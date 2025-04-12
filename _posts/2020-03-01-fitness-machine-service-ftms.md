---
title: FiTness Machine Service (FTMS)
date: '2020-03-01T08:50:00-08:00'
tags: android kotlin ble
---
![BLE](/assets/ble.png)

I recently created an Android app for debugging exercise equipment that advertise under the FiTness Machine Service (FTMS).  The Nautilus Quality Assurance team now uses the app extensively for claims testing of third-party open-standard equipment. As an added benefit I learned a great deal about BLE, Android, and byte decoding while creating the app.

Under BLE (Bluetooth Low-Energy) GATT (General ATTributes Profile) there are "services" which act in the same way as protocols function under normal Bluetooth.  They identify various bit and byte contracts that bluetooth broadcasts must conform to.  The particulars of these contracts are published on the <a href="https://www.bluetooth.com/specifications/gatt/services/">Bluetooth Organization's website</a>.  FTMS is one of these services.  All services including FTMS have nested characteristics.  These characteristics can either be READ properties (static) or NOTIFY properties (stream).  You must register for the latter by writing to the descriptor notification property of the characteristic. Android specifically will not wait for the BLE notification flag to be set after writing to the descriptor.  If you try to read immediately after the write you will get a non-deterministic response.  To avoid this you must write to the descriptor, wait for a notification that the descriptor has been written to, and then begin listening for characteristic changes.

Most BLE characteristics use Byte squishing.   A byte (8 bits) can be represented by two hexes (0-F). This is because each hex represents 4 bits, with 16 possible values.  A characteristic's supported features will be indicated in the first 2 bytes.  This is the "properties" value and is actually a set of true/false bit flags for supported features.  The ByteArray that follows represents the different possible values for each feature from the properties flags. Unsupported features will not be represented, even by 0 byte values.  This allows more features to be sent in a single transmission.

The maximum number of bytes transmitted in a single transmission is determined by the MTU (Max Transmission Unit).  The MTU is established between the client and the server as part of their pairing handshake.  Data that exceeds the number of bytes will need to be sent in a subsequent transmission. The larger the MTU, the longer that it takes to transmit, receive, and decode a single transmission.

Most of the values you receive will be represented by unsigned integers.  This is because by re-purposing the sign bit at the front you can double the magnitude of the possible values.  When a property must be capable of representing negative values however, it will be transmitted using a signed integer.  When you have signed integers, you should evaluate the value at the byte boundary.  This is illustrated in the diagram below for feature X:

![Bits & Bytes](/assets/bitbyte.png)

For the purposes of this example Feature X uses two bytes.  The first is signed, the second is not.  When decoding the ByteArray you must make sure to account for the mixture of signed and unsigned bytes. The code below illustrates how this might be accomplished in Kotlin using Kotlin's experimental unsigned types.  Java, for reference, does not support unsigned types.

```kotlin
@ExperimentalUnsignedTypes
fun convertBytesAndFeaturesToCharacteristics(bytes: ByteArray, flags: List<INDOOR_BIKE_DATA_FLAGS>):Characteristic{
    var currentByteIndex = 2 //First two bytes are used for flags.
    val sb = StringBuilder()
    for (flag in flags){
        val dataEndByteIndex = currentByteIndex + flag.byteSize
        val dataBytes = bytes.copyOfRange(currentByteIndex, dataEndByteIndex)
        currentByteIndex = dataEndByteIndex
        val int = if (!flag.signed) dataBytes.asUByteArray().toInt() else dataBytes.toInt()
        val doubleValue = (int * flag.resolution * 10).roundToInt().toDouble() / 10.0 //Removes rounding errors
        sb.append("${flag.name}: $doubleValue ${flag.units} \n")
    }
    return Characteristic("Indoor Bike Data",sb.toString())
}
```

This function relies on the functions below to convert between Bytearrays:

```kotlin
fun ByteArray.toInt(): Int {
       val numBits = this.size * 8
       return BitSet.valueOf(this).toInt(numBits)
}
```

and BitSets:

```kotlin
fun BitSet.toInt(numBits : Int ): Int {
    var value = 0
    var isNegative = false
    for (i in 0 until this.length()) {
        if (i == numBits - 1) isNegative = this[numBits - 1] // handle two's compliment
        else value += if (this[i]) 1 shl i else 0
    }
    if (isNegative) return value * -1
    return value
}
```

Kotlin specifically also has the experimental unsigned ByteArray.  To convert this to an integer the following custom function is used:

```kotlin
@ExperimentalUnsignedTypes
fun UByteArray.toInt(): Int {
    var result : UInt = 0u
    for (i in this.indices){
        result = result or (this[i].toUInt() shl 8 * i)
    }
    return result.toInt()
}
```

After having implemented the Android/Kotlin solution I'm now somewhat curious how the iOS/Swift solution might be the same or different.  I would like to make a mirror implementation iOS app eventually, but if you've already done so please feel free to post in the comments!
