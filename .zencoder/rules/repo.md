---
description: Repository Information Overview
alwaysApply: true
---

# Cordova Plugin: Age Verification

## Summary
Cross-platform Cordova plugin providing privacy-preserving age verification using Apple's DeclaredAgeRange API (iOS 26+) and Google Play Age Signals API (Android). Enables compliance with US state age verification laws (Texas SB2420, Utah SB287, Louisiana Act 440) without collecting exact birthdates.

## Structure
```
cordova-plugin-age-verification/
├── src/
│   ├── ios/                              # iOS Swift implementation
│   │   ├── AgeVerificationIOS.swift      # Main iOS bridge
│   │   └── AgeVerification-Bridging-Header.h
│   └── android/                          # Android Java implementation
│       └── AgeVerificationAndroid.java   # Main Android bridge
├── www/
│   └── AgeVerification.js                # JavaScript bridge/plugin interface
├── types/
│   └── index.d.ts                        # TypeScript definitions
├── plugin.xml                            # Cordova plugin configuration
├── package.json                          # npm metadata
└── README.md                             # Full documentation
```

## Language & Runtime
**Primary Language**: JavaScript (Plugin Interface)  
**iOS Implementation**: Swift 5.0  
**Android Implementation**: Java (API 23+)  
**iOS Minimum**: iOS 26.0, Xcode 26 Beta  
**Android Minimum**: Android API 23 (Android 6.0)  
**Cordova iOS**: >= 7.0.0  
**Cordova Android**: >= 10.0.0  
**Build System**: Cordova CLI  
**Package Manager**: npm

## Dependencies
**Runtime Plugin Dependencies**:
- `cordova-plugin-add-swift-support` (^2.0.2) - Swift 5.0 support for iOS

**Platform-Specific SDKs**:
- iOS: DeclaredAgeRange framework (system framework, weak-linked)
- Android: Google Play Age Signals API (com.google.android.play:age-signals:0.0.2)

**No npm package dependencies** - plugin uses native APIs directly via Cordova exec bridge.

## Installation & Build

**Add Plugin**:
```bash
cordova plugin add ./cordova-plugin-age-verification
cordova plugin add https://github.com/intractallc/cordova-plugin-age-verification.git
```

**iOS Setup** (after plugin installation):
1. Enable DeclaredAgeRange capability in Xcode (Signing & Capabilities)
2. Set deployment target to iOS 26.0 in `config.xml`
3. Ensure entitlement files include DeclaredAgeRange permission

**Android Setup**:
1. App must be distributed via Google Play Store
2. Minimum SDK version: 23
3. Internet permission is auto-configured

## Main Entry Points

**JavaScript Bridge**: `www/AgeVerification.js`
- Exposes global `AgeVerification` object via Cordova exec bridge
- Handles callbacks for cross-platform method invocation

**iOS Implementation**: `src/ios/AgeVerificationIOS.swift`
- Implements DeclaredAgeRange API integration
- Handles framework availability checks
- Manages parental control detection

**Android Implementation**: `src/android/AgeVerificationAndroid.java`
- Implements Google Play Age Signals API integration
- Package: `com.anthropic.ageverification.AgeVerificationAndroid`
- Handles supervised account detection

## API Methods
1. **isAvailable()** - Check if age verification is available on device
2. **requestAgeRange(ageGates)** - Request user's age range with 1-3 age thresholds
3. **isUserAboveAge(minimumAge)** - Convenience method for minimum age check
4. **getPlatformInfo()** - Get platform version and API availability info
5. **checkAgeSignals()** - Android-specific method for full age signals data

**Constants**:
- `AGE_GATES`: KIDS (13), TEENS (16), ADULTS (18), ALCOHOL_US (21)
- `STANDARD_GATES`: FULL, ADULT_ONLY, TEEN_ADULT, US_STATE_COMPLIANCE
- `USER_STATUS`: VERIFIED, SUPERVISED, SUPERVISED_APPROVAL_PENDING, etc.

## TypeScript Definitions
Complete TypeScript declarations in `types/index.d.ts` including:
- `AgeRangeResult` interface
- `AgeCheckResult` interface
- `PlatformInfo` union type (iOS + Android variants)
- `AgeSignalsResult` interface (Android)
- `ErrorResult` interface with all error codes

## Error Handling
**Common Error Codes**: unsupported, invalid_arguments, invalid_request, not_available, unknown

**Android-Specific**: api_not_available, play_store_not_found, network_error, play_services_not_found, play_store_outdated, play_services_outdated, transient_error, app_not_owned, internal_error

All errors include `error` code, `message`, and optional `retryable` flag (Android).

## Usage Pattern
```javascript
// Check availability first
AgeVerification.isAvailable(success, error);

// Request age range with custom gates
AgeVerification.requestAgeRange([13, 16, 18], success, error);

// Or simple age check
AgeVerification.isUserAboveAge(18, success, error);
```

## Compliance & Features
- **Privacy-Preserving**: No exact birthdate collection
- **Multi-Gate Support**: Up to 3 age thresholds configurable
- **Parental Control Detection**: iOS parental controls visibility
- **Source Tracking**: Identifies age source (self-declared, guardian-declared, verified, supervised)
- **US State Compliance**: Supports Texas SB2420, Utah SB287, Louisiana Act 440
