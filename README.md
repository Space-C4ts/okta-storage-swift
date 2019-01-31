[<img src="https://devforum.okta.com/uploads/oktadev/original/1X/bf54a16b5fda189e4ad2706fb57cbb7a1e5b8deb.png" align="right" width="256px"/>](https://devforum.okta.com/)

# Okta Secure Storage Library

This library is a Swift wrapper around the iOS LocalAuthentication and Security frameworks. The library provides convenient APIs to utilize keychain services by giving your app a mechanism to store small bits of user data in an encrypted database. The keychain is not limited to passwords and tokens. You can store other secrets that the user explicitly cares about, such as credit card information or even short notes.

The storage library includes the following features:
1. Get, set and delete keychain items
2. Store to the keychain behind a biometric factor such as fingerprint or face ID


**Table of Contents**

<!-- TOC depthFrom:2 depthTo:3 -->

- [Usage](#usage)
- [Create instance of OktaSecureStorage class](#create-instance-of-oktasecurestorage-class)
- [Save data to keychain](#save-data-to-keychain)
- [Save data to keychain behind a biometric factor](#save-data-to-keychain-behind-a-biometric-factor)
- [Load data from keychain](#load-data-from-keychain)
- [Delete data from keychain](#delete-data-from-keychain)
- [Error handling](#error-handling)
- [API Reference](#api-reference)
- [How to use this libary in Objective-C project](#how-to-use-this-libary-in-objective-c-project)

<!-- /TOC -->

## Usage

Add `import OktaSecureStorage` to your source code

### Create instance of OktaSecureStorage class

```swift
let oktaStorage = OktaSecureStorage()
```

### Save data to keychain

```swift
let success = oktaStorage.set(data: "password", forKey: "jdoe")
```

### Save data to keychain behind a biometric factor

```swift
let success = oktaStorage.set(data: "password", forKey: "jdoe" accessGroup: nil, behindBiometrics: true)
```

### Load data from keychain

```swift
let password = oktaStorage.get("jdoe")
```

### Delete data from keychain

```swift
let success = oktaStorage.delete("jdoe")
```

### Error handling

To get a specific failure reason use the lastResultCode property containing result code for the last operation. Please see [Keychain Result Codes](https://developer.apple.com/documentation/security/1542001-security_framework_result_codes)

```swift
if oktaStorage.set("password", forKey: "jdoe") {
    // Data successfully saved
} else {
    // Check OSStatus error code
    let error = oktaStorage.lastResultCode
    // Error handling
}
```

## API Reference

### set(data: String, forKey key: String) -> Bool

Stores an item securely in the keychain. Method returns true on success and false on error.

```swift
let success = oktaStorage.set(data: "password", forKey: "jdoe")
```

### set(data: String, forKey key: String, accessGroup: String? = nil) -> Bool

Stores an item securely and additionaly accepts `accessGroup` identifier. Use `accessGroup` to share keychain items between apps. Two or more apps that are in the same group can share keychain items because they share a common keychain access group entitlement. For more details, see [Sharing Access to Keychain Items Among a Collection of Apps](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)

```swift
let success = oktaStorage.set(data: "password", forKey: "jdoe", accessGroup: "com.mycompany.sharedkeychain")
```

### set(data: String, forKey key: String, accessGroup: String? = nil, behindBiometrics: Bool = false) -> Bool

Stores an item securely and additionaly accepts `behindBiometrics` parameter. Set this parameter to `true` if you want to store keychain item behind a biometric factor such as fingerprint or face ID.

```swift
let success = oktaStorage.set(data: "password", forKey: "jdoe" accessGroup: nil, behindBiometrics: true)
```

### get(key: String) -> String?

Retrieves the stored keychain item from the keychain.
> * Note: iOS will show native Touch ID or Face ID message view in case of biometrics enabled storage. It means that function may be blocked and wait for the user's action.

```swift
let password = oktaStorage.get("jdoe")
```

### get(userPrompt prompt: String = "", key: String) -> String?

Retrieves the stored keychain item from the keychain. Additionaly method expects `userPrompt` message for the keychain item stored behind a biometric factor. 
> * Note: iOS shows default system message If `userPrompt` parameter is emty.
> * Note: iOS will show native Touch ID or Face ID message view in case of biometrics enabled storage. It means that function may be blocked and wait for the user's action.

```swift
let password = oktaStorage.get(userPrompt: “Please use Touch ID or Face ID to sign in” key: "jdoe")
```

### delete(key: String) -> Bool

Removes the stored keychain item from the keychain

```swift
let success = oktaStorage.delete("jdoe")
```

### isBiometricsSupported

Checks whether fingerprint enrolled with Touch ID or a face set up with Face ID. This method allows easy checking for such conditions.

```swift
let isBiometricsSupported = storageManager.isBiometricsSupported()
```


## How to use this libary in Objective-C project
1. Include auto generated swift header file into your .m file. Swift header file contains objective-c representation of Okta swift classes. Please note that the name of header file consists of your project name and “-Swift” suffix. For example if your project name is AuthApp, then auto generated header file name will be “AuthApp-Swift.h”
2. Start using programming components available in swift header file

Example:
```objective-c
OktaSecureStorage *storage = [OktaSecureStorage new];
BOOL success = [storage setWithData:@"password" forKey:@"jdoe"];
if (success) {
    NSString *password = [storage getWithKey:@"jdoe"];
    success = [storage deleteWithKey:@"jdoe"];
}
```
