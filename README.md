
# PenAttendify Framework Integration Guide

## Table of Contents

- [PenAttendify Framework Integration Guide](#penattendify-framework-integration-guide)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Requirements](#requirements)
  - [Installation](#installation)
    - [Swift Package Manager (SPM)](#swift-package-manager-spm)
  - [Getting Started](#getting-started)
  - [Usage](#usage)
    - [Initialize PenAttendify](#initialize-penattendify)
    - [Start Scanning](#start-scanning)
    - [Stop Scanning](#stop-scanning)
  - [Delegate Methods](#delegate-methods)
    - [didFinishInitializing()](#didfinishinitializing)
    - [didStartScanning()](#didstartscanning)
    - [didFinishScanning(results:)](#didfinishscanningresults)
    - [didFailWithError(error:)](#didfailwitherrorerror)
  - [Error Handling](#error-handling)
  - [Deinitialization](#deinitialization)
  - [FAQs](#faqs)
  - [Contact \& Troubleshooting](#contact--troubleshooting)


## Introduction

PenAttendify is a robust framework enabling your iOS app to engage with Bluetooth-enabled beacons, facilitating proximity-based user location detection. Through PenAttendify, seamless scanning for adjacent beacons becomes achievable, allowing you to identify the closest zone with a degree of confidence indicated by the SDK's detection certainty.

This integration guide will walk you through the steps to integrate the PenAttendify framework into your iOS app. It will also aid you in initiating the utilization of its functionalities.


## Requirements

- iOS 13.0+
- Xcode 12.0+
- Swift 5.0+

## Installation



### Swift Package Manager (SPM)

You can also use Swift Package Manager to integrate PenAttendify into your Xcode project. Follow these steps:

1. Open your Xcode project.
2. Select File > Swift Packages > Add Package Dependency.
3. Enter the URL of the PenAttendify repository: `https://github.com/Penguinin-hub/PenAttendify-iOS-package`
4. Click Next and follow the on-screen instructions.




## Getting Started

Before you start using PenAttendify in your app, you need to perform the following steps:

1. **Obtain API Credentials**: Contact our support team to get your API credentials, including `clientName`, `clientKey`, `baseUrl`, `serviceName`.

2. **Request Location Permissions**: Ensure that your app has the necessary location permissions to scan for beacons. Add the following key to your app's `Info.plist` file:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>We need your location to scan for nearby beacons.</string>
```

## Usage

### Initialize PenAttendify

To use PenAttendify in your app, you must initialize the framework with the obtained API credentials. The initialization process should happen early in your app's lifecycle, preferably in the `AppDelegate`:

```swift
import PenAttendify

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    // Replace the following placeholders with your actual API credentials.
    let configuration = PenAttendifyConfiguration(baseUrl: "your_base_url",
                                                  serviceName: "your_service_name",
                                                  clientName: "your_client_name",
                                                  clientKey: "your_client_key",
                                                  syncEnabled: false)
    
    PIAttendify.initialize(with: configuration)
    
    return true
}
```
The `syncEnabled` property in the `PenAttendifyConfiguration` class determines the data-fetching strategy for the SDK.
- `false`: Prioritizes fetching data from the local database for better performance and offline support, falling back to the API only if the data has not been fetched previously.
- `true`: Always fetches the latest data from the API, ensuring up-to-date information but with higher network usage.

### Start Scanning

After successful initialization, you can start scanning for nearby beacons by calling the `startScan()` method:

```swift
PIAttendify.startScan()
```

### Stop Scanning

To stop the beacon scanning process, use the `stopScan()` method:

```swift
PIAttendify.stopScan()
```

## Delegate Methods

PenAttendify provides delegate methods to receive important events and error notifications. Conform your class to `PenAttendifyDelegate` and set it as the delegate to receive these events:

```swift
class MyClass: PenAttendifyDelegate {
    // ...
}

// Set the delegate
PIAttendify.delegate = self
```

### didFinishInitializing()

This method is called when the PenAttendify framework has finished initializing successfully. You can use this event to proceed with other actions in your app after successful initialization.

```swift
func didFinishInitializing() {
    print("PenAttendify initialized successfully.")
    // Proceed with other actions...
}
```

### didStartScanning()

This method is called when the beacon scanning process starts successfully. You can use this event to show loading indicators or UI elements indicating the scanning process has started.

```swift
func didStartScanning() {
    print("Beacon scanning started.")
    // Show loading indicators or update UI.
}
```

### didFinishScanning(results:)

This method is called when the beacon scanning process finishes with the scan results. It provides an array of tuples containing zone information from scanned beacons. Each tuple contains zone ID, zone title (optional), and zone ratio (a value indicating signal strength or proximity).

```swift
func didFinishScanning(results: [(zoneId: Int, zoneTitle: String?, zoneRatio: Double)]) {
    print("Beacon scanning results received.")
    for result in results {
        print("Zone ID: \(result.zoneId), Zone Title: \(result.zoneTitle ?? "N/A"), Zone Ratio: \(result.zoneRatio)")
    }
    // Process the scanned beacon data.
}
```

### didFailWithError(error:)

This method is called when an error occurs in the PenAttendify framework. The `PenAttendifyError` enum represents the possible errors that can occur.

```swift
func didFailWithError(error: PenAttendifyError) {
    print("An error occurred: \(error)")
    // Handle the error appropriately.
}
```

## Error Handling

The `PenAttendifyError` enum represents the possible errors that can occur in the PenAttendify framework. You should handle these errors in the `didFailWithError` delegate method. Possible errors include:

- `.locationPermissionUnauthorized`: Location permission is not authorized, and the framework requires location access to function properly.
- `.bluetoothTurnedOff`: Bluetooth is turned off on the device, and the framework requires Bluetooth to scan for beacons.
- `.serviceNotInitialized`: The PenAttendify framework is not initialized, and certain operations require proper initialization.

## Deinitialization

To release resources and properly deinitialize the PenAttendify framework, call the `deinitialize()` method:

```swift
PIAttendify.deinitialize()
```



## FAQs


**Q: How can I check if the PenAttendify framework is already initialized?**

A: You can use the `isIsInitialized()` method in the `PIAttendify` class to check the initialization status. This method returns a Boolean value indicating whether PenAttendify has been initialized or not.

```swift
if PIAttendify.isIsInitialized() {
    print("PIAttendify is already initialized.")
} else {
    print("PIAttendify is not yet initialized.")
}
```

Alternatively, you can also use the delegate methods, such as `didFinishInitializing()`, to handle initialization events and be notified when PenAttendify has finished initializing.

```swift
class MyClass: PenAttendifyDelegate {
    // ...
    PIAttendify.delegate = self
}


```

```swift
func didFinishInitializing() {
    print("PIAttendify has finished initializing.")
    // Perform additional actions after successful initialization.
}
```

**Q: Can I use the PenAttendify Framework in a simulator or with the arm64_x86_64-simulator architecture during development?**

A: Yes, for integration and development purposes, the PenAttendify Framework does support simulation and the arm64_x86_64-simulator architecture. You can test and integrate the framework in a simulated environment during the development process.

**Q: Is it recommended to use the PenAttendify Framework on real iOS devices during actual use cases?**

A: Yes, for real-world use cases and to ensure accurate results, it is strongly recommended to deploy and use the PenAttendify Framework on physical iOS devices with Bluetooth capabilities. This will ensure that the framework functions as expected and provides reliable proximity-based user location detection in real live scenarios.

**Q: Can I customize the scan interval or other settings in PenAttendify?**

A: Currently, PenAttendify does not expose direct settings for customization. The framework is designed to work optimally with default settings.

**Q: Can I run multiple instances of PenAttendify in the same app?**

A: PenAttendify is designed to work as a singleton instance in your app. Running multiple instances may lead to unexpected behavior.

**Q: How can I update the configuration after initializing PenAttendify?**

A: Once PenAttendify is initialized, the configuration cannot be directly updated. To change the configuration, you need to deinitialize PenAttendify and then initialize it again with the updated configuration. Make sure to call PIAttendify.deinitialize() before calling PIAttendify.initialize() with the new configuration.

**Q: Can I use PenAttendify with background app refresh enabled?**
No, it does not support background processes.

**Q: When should I use the PIAttendify.deinitialize() method?**

A: You should use the deinitialize() method when you want to release any allocated resources and stop using the PenAttendify framework in your app. It is recommended to call deinitialize() before reinitializing PenAttendify with a new configuration or when your app no longer needs to use the framework.


**Q: How can I receive events from PenAttendify?**

A: To receive events from PenAttendify, you need to conform to the `PenAttendifyDelegate` protocol in your class and set the class as the delegate for PenAttendify by assigning `self` to `PIAttendify.delegate`. Once set, your class will receive notifications for various events triggered by PenAttendify.

```swift
class MyClass: PenAttendifyDelegate {
    // ...
}

PIAttendify.delegate = self
```

**Q: When is the `didFinishInitializing()` delegate method called?**

A: The `didFinishInitializing()` delegate method is called when PenAttendify has successfully finished initializing with the provided configuration. This indicates that PenAttendify is ready to use, and you can proceed with other actions in your app that require the framework's functionality.

```swift
func didFinishInitializing() {
    print("PenAttendify has finished initializing.")
    // Proceed with other actions after successful initialization.
}
```

**Q: How can I detect when PIAttendify starts scanning for beacons?**

A: The `didStartScanning()` delegate method is called when PenAttendify starts scanning for nearby beacons. You can use this event to display loading indicators or update the UI to inform the user that the scanning process has started.

```swift
func didStartScanning() {
    print("Beacon scanning started.")
    // Show loading indicators or update UI to indicate scanning process.
}
```

**Q: How do I receive the scanned beacon data from PIAttendify?**

A: The `didFinishScanning(results:)` delegate method provides the scanned beacon data as an array of tuples. Each tuple contains zone information from scanned beacons, including the zone ID, zone title (optional), and zone ratio (a value indicating signal strength or proximity).

```swift
func didFinishScanning(results: [(zoneId: Int, zoneTitle: String?, zoneRatio: Double)]) {
    print("Beacon scanning results received.")
    for result in results {
        print("Zone ID: \(result.zoneId), Zone Title: \(result.zoneTitle ?? "N/A"), Zone Ratio: \(result.zoneRatio)")
    }
    // Process the scanned beacon data.
}
```

**Q: What happens if I don't set the delegate for PIAttendify?**

A: If you don't set the delegate for PenAttendify, your app will not receive delegate method calls. The delegate is essential for receiving events and data from PenAttendify. Ensure that you set the delegate to a class that conforms to `PenAttendifyDelegate` to handle the framework's events properly.

---


 
## Contact & Troubleshooting

If you encounter any issues or need further assistance, please refer to the [FAQs](#faqs) section or contact our support team at devsupport@penguinin.com.
