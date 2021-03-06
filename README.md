Prince of versions
=================
![Platform](https://img.shields.io/badge/pod-v1.0.0-blue.svg)
![License](https://img.shields.io/cocoapods/l/SemanticVersioning.svg)

Library checks for updates using configuration from some resource.

Features
--------
  * Load update configuration from **network** resource
  * Use predefined parser for parsing update configuration in **JSON format**
  * Make **asynchronous** loading and use **callback** for notifying result
  * Loading and verifying versions happen **outside of UI thread**

----------

### Requirements
- iOS 8.0+
- Xcode 8.0+
- Swift 3.0

### Installation
The easiest way to use Prince of versions in your project is using the CocaPods package manager.

###CocoaPods
See installation instructions for [CocoaPods](http://cocoapods.org) if not already installed

To integrate the library into your Xcode project specify the pod dependency to your `Podfile`:

```ruby
platform :ios, '8.0'
use_frameworks!

pod 'PrinceOfVersions'
```

run pod install

```bash
pod install
```

### JSON file

JSON file in your application has to follow [Semantic Versioning](http://semver.org/) and it has to look like this:

```json
{
	"ios": {
		"minimum_version": "1.2.3",
		"latest_version": {
			"version": "2.4.5",
			"notification_type": "ALWAYS"
		}
	},
	"android": {
		"minimum_version": "1.2.3",
		"latest_version": {
			"version": "2.4.5",
			"notification_type": "ONCE"
		}
	},
	"meta": {
		"key1": "value1",
		"key2": "value2"
	}
}
```

Depending on <code>notification_type</code> property, the user can be notified <code>ONCE</code> or <code>ALWAYS</code>. The library handles this for you, and if notification type is set to <code>ONCE</code>, it will notify you via <code>newUpdate(version: String, isMandatory: Bool, metadata: [String: AnyObject]?)</code> method only once. Every other time the library will return <code>noUpdate</code> for that specific version. 
Key-value pairs under <code>"meta"</code> key are optional metadata of which any amount can be sent accompanying the required fields.


Usage
-------------
Full example application is available [here]().

#### Most common usage - loading from network resource

1. Getting all data

	```Swift
	let url = URL(string: "http://pastebin.com/raw/uBdFKP2t")
        PrinceOfVersions().loadConfiguration(from: url) { (response) in
            switch response.result {
            case .success(let info):
                print("Minimum version: ", info.minimumRequiredVersion)
                print("Installed version: ", info.installedVersion)
                print("Is minimum version satisfied: ", info.isMinimumVersionSatisfied)
                print("Notification type: ", info.notificationType)

                if let latestVersion = info.latestVersion {
                    print("Is minimum version satisfied: ", latestVersion)
                }
            case .failure(let error):
                print(error.localizedDescription)
            }
        }
	```

2. Automatic handling update frequency

	```Swift
	let url = URL(string: "http://pastebin.com/raw/uBdFKP2t")
	PrinceOfVersions().checkForUpdates(from: url,
        newVersion: {
            (latestVersion, isMinimumVersionSatisfied, metadata) in
        },
        noNewVersion: {
            (isMinimumVersionSatisfied, metadata) in
        },
        error: {
            (error) in

        })
	```

### Multiple targets

If your application has multiple targets you might need more than one JSON configuration file. If that is the case, do not forget to set a different URL for each target.

### Contributing

Feedback and code contributions are very much welcome. Just make a pull request with a short description of your changes. By making contributions to this project you give permission for your code to be used under the same [license](https://github.com/infinum/Android-prince-of-versions/blob/dev/LICENCE).
