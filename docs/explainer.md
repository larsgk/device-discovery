# Device Discovery API

NOTE: THIS IS WORK IN PROGRESS (draft)

## Authors:

- [Lars Knudsen](https://github.com/larsgk)
- [Author 2]
- [etc.]

## Introduction

The Generic Sensor API spec (https://w3c.github.io/sensors/) and
derivaties (e.g. https://www.w3.org/TR/orientation-sensor/) provide
a great foundation for unified interaction with device sensors.
In addition, Web USB, Web Bluetooth and Serial API enable communication
with external devices attached to the host system, relying on user
interaction and custom driver code embedded in the application.

However, there is a need for a generic solution for actuator support,
device discovery, pre-provisioning and driver registration for this
domain of features to be valuable—in particular—for
enterprise solutions.

## Use Cases

### Engineer/field worker running diagnostics
Inspection of factory floors, engine rooms, etc. will benefit from
field workers to be able to quickly obtain readings from sensors in the
vicinity. Connection can be wired or wireless depending on the setup.
Assuming the diagnostics software is a PWA (being offline will be a common case),
the engineer should be able to discover and interact with trusted
devices without the need to go through the current flow of provisioning
for every single sensor.

TBD areas to explore: Enterprise Chrome or Extensions (pre-provision),
(trusted) driver registration, NFC

### Vehicle monitoring
When connecting a phone to a vehicle (most common: car) via BLE or USB,
any web app utilizing sensor data (geolocation, ambient light, temperature, etc.)
should be able to tap into the extra sensors available.

It should be possible to register and prioritize (e.g. 'new default')
these sensors to be discoverable as first class Generic Sensors for the app.

TBD areas to explore: NFC (tap to provision on/off)

### Home automation
Given that BLE Mesh implementations for home automation is moving, now it would
only be natural if a web app connecting to a node in the house would be able to
discover and interact with the sensors and actuators available.  Allowing
dynamic registration/deregistration in the Generic Sensor (and Actuator) context
will provide app developers a generalized and portable approach to interacting
with home automation units (thermostats, lights, environmental readings, etc.).

## API: Driver definition

```js
class MyDeviceDriver extends GenericDeviceDriver {
    static get discovery() {
        return [
            // NOTE: The device could support multiple protocols
            //       or virtual devices using the same protocol
            {
                type: 'usb',
                identifier: {
                    vendorId: 0x0000,
                    productId: 0x0000
                }
            },
            {
                type: 'bluetooth',
                identifier: {
                    services: ['ef680100-9b35-4933-9b10-52ffa9740042']
                }
            }
        ]
    }

    // TODO: Device factory
    // TODO: hardware abstraction layer (e.g. device <-> GenericSensor)
}
```

## API: Driver Registration
The drivers function as the Hardware Abstraction Layer (HAL) between wired/wireless
devices and standardized generic device JS APIs. They provide unique discovery
identifiers (supporting WebUSB, WebBluetooth and Serial cases), protocol handlers
and exposure to the application.

Registration:

```js
navigator.devices.registerDriver('driver-name', MyDeviceDriver);

```

## Device provisioning
For enterprise solutions, devices will be matched against pre-provisioned signatures.

For consumer solutions, the simplest solution will be provisioning using e.g. NFC.

## API: Device Discovery
Pre-provisioned devices with driver support will be discoverable to the app with:

```js
navigator.devices.addEventListener('connect', evt => handleConnectDevice);
navigator.devices.addEventListener('disconnect', evt => handleDisconnectDevice);
```

## Devices
Initially, sensors will be supported, following the
[Generic Sensor](https://www.w3.org/TR/generic-sensor/) API.
Actuators also need to be supported, but that will require work done on
generic actuators, which should be covered in a separate proposal/spec.

...
## [API 1]

[For each related element of the proposed solution - be it an additional JS method, a new object, a new element, a new concept etc., create a section which briefly describes it.]

```js
// Provide example code - not IDL - demonstrating the design of the feature.

// If this API can be used on its own to address a user need,
// link it back to one of the scenarios in the goals section.

// If you need to show how to get the feature set up
// (initialized, or using permissions, etc.), include that too.
```

[Where necessary, provide links to longer explanations of the relevant pre-existing concepts and API.
If there is no suitable external documentation, you might like to provide supplementary information as an appendix in this document, and provide an internal link where appropriate.]

[If this is already specced, link to the relevant section of the spec.]

[If spec work is in progress, link to the PR or draft of the spec.]

## [API 2]

[etc.]

## Key scenarios

[If there is a suite of interacting APIs, show how they work together to solve the key scenarios described.]

### Field sensor diagnostics

Common for this type of scenario:
 * The user has pre-installed a PWA for sensor inspection
 * The need for offine support will be common
 * Trusted sensor drivers are preloaded and ready
 * The user moves around in the field => not all sensors will be available at all times

[Description of the end-user scenario]

```js
// Sample code demonstrating how to use these APIs to address that scenario.
```

### Scenario 2

[etc.]

## Detailed design discussion

### Device connection vs. WebUSB, WebBluetooth and Serial
Devices that are covered by device drivers and discovery should not
be listed/be accessible using the low level WebUSB, WebBluetooth or Serial APIs
to prevent parallel access. This needs to be done to avoid exclusivity problems.
However, it should be possible to access the corresponding lower level device
instance (USB, BLE, Serial) inside the discoverable device object.

## Considered alternatives
The existing APIs support the above mentioned scenarios except for
discovery and pre-provisioning.

It will currently be possible to do polyfills to support the cases
if accepting the need for user interaction per device.

### WebUSB devices
WebUSB enabled devices would be possible to connect and register
following the patterns described in the proposal, using polyfills
and only requiring initial provisioning, which could be considered
acceptable in many scenarios.  However, it would be desirable
to avoid user interaction for acceptance for industry solutions.

### WebBluetooth devices
Bluetooth Smart devices currently require user interaction to
discover and connect for every connection.  This will not be an acceptable
solution for enterprise solutions.  However, it would be possible
to make polyfills as a temporary solution that will require
user interaction.

## Stakeholder Feedback / Opposition

[Implementors and other stakeholders may already have publicly stated positions on this work. If you can, list them here with links to evidence as appropriate.]

- [Implementor A] : Positive
- [Stakeholder B] : No signals
- [Implementor C] : Negative

[If appropriate, explain the reasons given by other implementors for their concerns.]

## References & acknowledgements

[Your design will change and be informed by many people; acknowledge them in an ongoing way! It helps build community and, as we only get by through the contributions of many, is only fair.]

[Unless you have a specific reason not to, these should be in alphabetical order.]

Many thanks for valuable feedback and advice from:

- [Kenneth Christiansen](https://github.com/kenchris)
- [Person 2]
- [etc.]
