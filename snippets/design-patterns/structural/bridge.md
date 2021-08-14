```ts
// The "abstraction" defines the interface for the "control"
// part of the two class hierarchies. It maintains a reference
// to an object of the "implementation" hierarchy and delegates
// all of the real work to this object.
class RemoteControl {
    protected field device: Device

    constructor RemoteControl(device: Device) {
        this.device = device
    }

    method togglePower() {
        if (device.isEnabled()) then
            device.disable()
        else
            device.enable()
    }

    method volumeDown() {
        device.setVolume(device.getVolume() - 10)
    }

    method volumeUp() {
        device.setVolume(device.getVolume() + 10)
    }

    method channelDown() {
        device.setChannel(device.getChannel() - 1)
    }

    method channelUp() {
        device.setChannel(device.getChannel() + 1)
    }
}


// You can extend classes from the abstraction hierarchy
// independently from device classes.
class AdvancedRemoteControl extends RemoteControl {
    method mute() {
        device.setVolume(0)
    }
}

// The "implementation" interface declares methods common to all
// concrete implementation classes. It doesn't have to match the
// abstraction's interface. In fact, the two interfaces can be
// entirely different. Typically the implementation interface
// provides only primitive operations, while the abstraction
// defines higher-level operations based on those primitives.
interface Device {
    method isEnabled()
    method enable()
    method disable()
    method getVolume()
    method setVolume(percent)
    method getChannel()
    method setChannel(channel)
}


// All devices follow the same interface.
class Tv implements Device {
    // ...
}

class Radio implements Device {
    // ...
}


// Somewhere in client code.
tv = new Tv()
remote = new RemoteControl(tv)
remote.togglePower()

radio = new Radio()
remote = new AdvancedRemoteControl(radio)
```