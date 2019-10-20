# StravaSwift

[![CI Status](http://img.shields.io/travis/mpclarkson/StravaSwift.svg?style=flat)](https://travis-ci.org/mpclarkson/StravaSwift)
[![codebeat badge](https://codebeat.co/badges/d58ef23f-b2c6-45df-83cb-35af96b6980d)](https://codebeat.co/projects/github-com-mpclarkson-stravaswift)
[![Version](https://img.shields.io/cocoapods/v/StravaSwift.svg?style=flat)](http://cocoapods.org/pods/StravaSwift)
[![License](https://img.shields.io/cocoapods/l/StravaSwift.svg?style=flat)](http://cocoapods.org/pods/StravaSwift)
[![Platform](https://img.shields.io/cocoapods/p/StravaSwift.svg?style=flat)](http://cocoapods.org/pods/StravaSwift)

This is a Swift wrapper for the [Strava v3 API](https://strava.github.io/api/).

As this is a passion project, I only work on it when I have time. So, if you are interested in contributing, feel free to submit PRs.

## Example

To run the example project, clone the repo, and run `pod install` from the Example directory first.

## Installation

[StravaSwift](https://github.com/mpclarkson/StravaSwift) is available through [CocoaPods](http://cocoapods.org). To install
it, simply add the following line to your Podfile:

```ruby
pod "StravaSwift"
```

## Quick Start

The full library documentation is available [here](http://cocoadocs.org/docsets/StravaSwift).

* First, you must [register your app](http://labs.strava.com/developers/) with Strava and get an OAuth `client id` and `client secret`.

* Initialize the Strava Client as follows, preferably in your `AppDelegate.swift` to ensure it is configured before you call it:

```swift
let config = StravaConfig(
    clientId: YourStravaClientId,
    clientSecret: YourStravaClientSecret,
    redirectUri: YourRedirectUrl
)

StravaClient.sharedInstance.initWithConfig(config)
```

* Note, by default the OAuth token is only available while the app is running, which means you need to request a new token. You can implement custom token storage and retrieval behaviour by overriding the default token `delegate` in the `StravaConfig` initializer which must implement the `TokenDelegate` protocol.

* Register your redirect URL scheme in your `info.plist` file.

* The authentication will use the Strava app be default if it is installed on the device. If the user does not have Strava installed, it will fallback on `SFAuthenticationSession` or `ASWebAuthenticationSession` depending on the iOS version. If your app is linked on or after iOS 9.0, you must add `strava` in you app’s `info.plist` file. It should be added as an entry fo the array under the `LSApplicationQueriesSchemes` key. Failure to do this will result in a crash when calling `canOpenUrl:`.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>strava</string>
</array>
```

* Implement the following method in your `AppDelegate.swift` to handle the OAuth redirection from Strava:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    let canHandle = strava.handleAuthorizationRedirect(url) { (token, error) in
        // Implement didHandleToken if needed
        // The token is already available to the StravaClient thanks to the TokenDelegate
    }
    if canHandle {
        // Implement willHandleToken if needed
        return true
    } else {
        return false
    }
}
```

* Now you can start requesting resources. 

> The Router implementation is based on this
Alamofire [example](https://github.com/Alamofire/Alamofire#api-parameter-abstraction):

```swift

let strava = StravaClient.sharedInstance

strava.request(Router.Athletes(id: 9999999999)) { (athlete: Athlete?) in
    //do something with the athlete
}

let params = [
    'page' = 2,
    'per_page' = 25
]

strava.request(Router.AthleteActivities(params: params) { (activities: [Activity]?) in
   //do something with the activities
}
```

## Todos

- [ ] 100% API coverage (about 90% now)
- [ ] Documentation
- [ ] Tests
- [ ] Better example app

## Author

Matthew Clarkson, mpclarkson@gmail.com

## License

StravaSwift is available under the MIT license. See the LICENSE file for more info.
