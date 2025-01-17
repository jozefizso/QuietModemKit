QuietModemKit
==============

This is the iOS framework for https://github.com/quiet/quiet

With this library, you can send data through sound.

Live demo: https://quiet.github.io/quiet-js/

Other platforms:
* JavaScript: https://github.com/quiet/quiet-js
* Android: https://github.com/quiet/org.quietmodem.Quiet

Why sound? Isn't that outdated?
---------------
If you are old enough, you may remember using dial-up modems to connect to the internet. In a sense, this package brings that back. While it's true that this is somewhat of a retro approach, consider the advantages of using sound.

* Highly cross-platform. Any device with speakers and a microphone and sufficient computational power can use this medium to communicate.

* No pairing. Unlike Bluetooth, sound can be used instantly without the need to pair devices. This reduces the friction and improves the user experience.

* Embeddable content. Similar to a QR code, short packets of data can be encoded into streaming or recorded audio and can then be later decoded by this package.

What does it sound like?
---------------
The answer to this depends on which operating mode you choose. Quiet provides audible and near-ultrasonic modes. Audible modes sound something like a puff of air. The near-ultrasonic modes run at 17+kHz and are virtually inaudible to adults. Either mode can operate at relatively low volumes as long as there isn't too much background noise.

How fast does it go?
---------------
Quiet's provided audible mode transfers at approximately 7kbps. In cases where two devices are connected over a cable (via 3.5mm jack) it can run in cable mode, which transfers at approximately 64kbps.

Building
--------------
CMake is required to build QuietModemKit. If you aren't sure if you have CMake installed, run `brew install cmake`.

To build the project, use Xcode 12.4 on macOS 10.15.

```sh
xcodebuild -configuration Debug build
```

Using QuietModemKit in application
--------------

Use Carthage to integrat QuietModemKit with your project.  
Add `github "jozefizso/QuietModemKit"` to your `Cartfile` and run `carthage update`.

Example
--------------
Transmitter:
```objc
#import <QuietModemKit/QuietModemKit.h>

int main(int argc, char * argv[]) {
    QMTransmitterConfig *txConf = [[QMTransmitterConfig alloc] initWithKey:@"ultrasonic-experimental"];

    QMFrameTransmitter *tx = [[QMFrameTransmitter alloc] initWithConfig:txConf];

    NSString *frame_str = @"Hello, World!";
    NSData *frame = [frame_str dataUsingEncoding:NSUTF8StringEncoding];
    [tx send:frame];

    CFRunLoopRun();

    [tx close];

    return 0;
}

```

Receiver:
```objc
#import <QuietModemKit/QuietModemKit.h>

static QMFrameReceiver *rx;

void (^recv_callback)(NSData*) = ^(NSData *frame){
    printf("%s\n", [frame bytes]);
};

void (^request_callback)(BOOL) = ^(BOOL granted){
    QMReceiverConfig *rxConf = [[QMReceiverConfig alloc] initWithKey:@"ultrasonic-experimental"];
    rx = [[QMFrameReceiver alloc] initWithConfig:rxConf];
    [rx setReceiveCallback:recv_callback];
};

int main(int argc, char * argv[]) {
    [[AVAudioSession sharedInstance] requestRecordPermission:request_callback];

    CFRunLoopRun();

    if (rx != nil) {
        [rx close];
    }

    return 0;
}
```

Note that we ask for the Record permission. This is required to use the receiver.

Profiles
--------------
The modem can be used audibly or via ultrasonic sound. `QMTransmitterConfig` and `QMReceiverConfig` are used to select modem configuration. For a full list of valid keys, refer to the top-level keys of [quiet-profiles.json](https://github.com/quiet/QuietModemKit/blob/master/quiet-profiles.json).

License
--------------
3-Claused BSD, plus third-party licenses (mix of MIT and BSD, see licenses/)
