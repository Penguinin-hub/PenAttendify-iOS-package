
# PenAttendify Framework Integration Guide

## Table of Contents

- [PenAttendify Framework Integration Guide](#penattendify-framework-integration-guide)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Requirements](#requirements)
  - [Installation](#installation)
    - [Swift Package Manager (SPM)](#swift-package-manager-spm)
    - [Manual Installation](#manual-installation)
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
  - [Sample Code](#sample-code)
  - [Troubleshooting](#troubleshooting)
  - [FAQs](#faqs)
  - [Contact](#contact)


## Introduction

PenAttendify is a robust framework enabling your iOS app to engage with Bluetooth-enabled beacons, facilitating proximity-based user location detection. Through PenAttendify, seamless scanning for adjacent beacons becomes achievable, allowing you to identify the closest zone with a degree of confidence indicated by the SDK's detection certainty.

This integration guide will walk you through the steps to integrate the PenAttendify framework into your iOS app. It will also aid you in initiating the utilization of its functionalities.


## Requirements

- iOS 12.0+
- Xcode 12.0+
- Swift 5.0+

## Installation



### Swift Package Manager (SPM)

You can also use Swift Package Manager to integrate PenAttendify into your Xcode project. Follow these steps:

1. Open your Xcode project.
2. Select File > Swift Packages > Add Package Dependency.
3. Enter the URL of the PenAttendify repository: `https://github.com/Penguinin-hub/pen-attendify.git`
4. Click Next and follow the on-screen instructions.



### Manual Installation

To integrate PenAttendify into your Xcode project manually, follow these steps:

1. Download the PenAttendify framework from the provided link.

2. Unzip the downloaded file, and you'll find `PenAttendify.framework` file.

3. In Xcode, open your project, and navigate to the project's **General** settings.

4. Drag and drop the `PenAttendify.framework` file into the **Frameworks, Libraries, and Embedded Content** section.

5. Make sure to select the **Copy items if needed** option when adding the framework to your project.

6. In the **Build Settings** tab, locate the **Framework Search Paths** section and add the path to the folder containing the `PenAttendify.framework`.

7. In the **Build Phases** tab, expand the **Embed Frameworks** section and ensure that `PenAttendify.framework` is listed.

8. Build your project to ensure the integration was successful.

Now, you have manually installed the PenAttendify framework in your project, and you can start using its features in your code.

---


## Getting Started

Before you start using PenAttendify in your app, you need to perform the following steps:

1. **Obtain API Credentials**: Contact our support team to get your API credentials, including `clientName`, `clientKey`, `baseUrl`, `serviceName`, `companyName`, and `companyId`.

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
                                                  companyName: "your_company_name",
                                                  companyId: "your_company_id")
    
    PenAttendify.initialize(with: configuration)
    
    return true
}
```

### Start Scanning

After successful initialization, you can start scanning for nearby beacons by calling the `startScan()` method:

```swift
PenAttendify.startScan()
```

### Stop Scanning

To stop the beacon scanning process, use the `stopScan()` method:

```swift
PenAttendify.stopScan()
```

## Delegate Methods

PenAttendify provides delegate methods to receive important events and error notifications. Conform your class to `PenAttendifyDelegate` and set it as the delegate to receive these events:

```swift
class MyClass: PenAttendifyDelegate {
    // ...
}

// Set the delegate
PenAttendify.delegate = self
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
PenAttendify.deinitialize()
```

## Sample Code

For a complete understanding of how to integrate PenAttendify into your app, you can find a sample project [here](https://github.com/example/pen-attendify-demo).

## Troubleshooting

If you encounter any issues or need further assistance, please refer to the [FAQs](#faqs) section or contact our support team at support@penattendify.com.

## FAQs


**Q: How can I check if the PenAttendify framework is already initialized?**

A: You can use the `isIsInitialized()` method in the `PenAttendify` class to check the initialization status. This method returns a Boolean value indicating whether PenAttendify has been initialized or not.

```swift
if PenAttendify.isIsInitialized() {
    print("PenAttendify is already initialized.")
} else {
    print("PenAttendify is not yet initialized.")
}
```

Alternatively, you can also use the delegate methods, such as `didFinishInitializing()`, to handle initialization events and be notified when PenAttendify has finished initializing.

```swift
class MyClass: PenAttendifyDelegate {
    // ...
    PenAttendify.delegate = self
}


```

```swift
func didFinishInitializing() {
    print("PenAttendify has finished initializing.")
    // Perform additional actions after successful initialization.
}
```



**Q: Can I customize the scan interval or other settings in PenAttendify?**

A: Currently, PenAttendify does not expose direct settings for customization. The framework is designed to work optimally with default settings.

**Q: Can I run multiple instances of PenAttendify in the same app?**

A: PenAttendify is designed to work as a singleton instance in your app. Running multiple instances may lead to unexpected behavior.

**Q: How can I update the configuration after initializing PenAttendify?**

A: Once PenAttendify is initialized, the configuration cannot be directly updated. To change the configuration, you need to deinitialize PenAttendify and then initialize it again with the updated configuration. Make sure to call PenAttendify.deinitialize() before calling PenAttendify.initialize() with the new configuration.

**Q: Can I use PenAttendify with background app refresh enabled?**
No, it does not support background processes.

**Q: When should I use the PenAttendify.deinitialize() method?**

A: You should use the deinitialize() method when you want to release any allocated resources and stop using the PenAttendify framework in your app. It is recommended to call deinitialize() before reinitializing PenAttendify with a new configuration or when your app no longer needs to use the framework.


**Q: How can I receive events from PenAttendify?**

A: To receive events from PenAttendify, you need to conform to the `PenAttendifyDelegate` protocol in your class and set the class as the delegate for PenAttendify by assigning `self` to `PenAttendify.delegate`. Once set, your class will receive notifications for various events triggered by PenAttendify.

```swift
class MyClass: PenAttendifyDelegate {
    // ...
}

PenAttendify.delegate = self
```

**Q: When is the `didFinishInitializing()` delegate method called?**

A: The `didFinishInitializing()` delegate method is called when PenAttendify has successfully finished initializing with the provided configuration. This indicates that PenAttendify is ready to use, and you can proceed with other actions in your app that require the framework's functionality.

```swift
func didFinishInitializing() {
    print("PenAttendify has finished initializing.")
    // Proceed with other actions after successful initialization.
}
```

**Q: How can I detect when PenAttendify starts scanning for beacons?**

A: The `didStartScanning()` delegate method is called when PenAttendify starts scanning for nearby beacons. You can use this event to display loading indicators or update the UI to inform the user that the scanning process has started.

```swift
func didStartScanning() {
    print("Beacon scanning started.")
    // Show loading indicators or update UI to indicate scanning process.
}
```

**Q: How do I receive the scanned beacon data from PenAttendify?**

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

**Q: What happens if I don't set the delegate for PenAttendify?**

A: If you don't set the delegate for PenAttendify, your app will not receive delegate method calls. The delegate is essential for receiving events and data from PenAttendify. Ensure that you set the delegate to a class that conforms to `PenAttendifyDelegate` to handle the framework's events properly.

---


## Contact

For any inquiries or assistance, please contact our support team at support@penattendify.com.



---

