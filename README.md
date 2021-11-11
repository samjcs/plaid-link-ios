# Plaid Link for iOS [![version][link-sdk-version]][link-sdk-url]

📚 Detailed instructions on how to integrate with Plaid Link for iOS can be found in our main documentation at [plaid.com/docs/link/ios][link-ios-docs].

📱 This repository contains sample applications in [Objective-C](LinkDemo-ObjC) and [Swift](LinkDemo-Swift) (requires Xcode 10.2) that demonstrate integration and use of Plaid Link for iOS.

###### Sample custom Link configuration in Swift

```swift
func presentPlaidLinkWithCustomConfiguration() {
        // <!-- SMARTDOWN_PRESENT_CUSTOM -->
        // With custom configuration

        let linkConfiguration = PLKConfiguration(linkToken: "<#GENERATED_LINK_TOKEN#>")

        // optional parameters
        linkConfiguration.oauthRedirectUri = URL(string: "YOUR_WHITELISTED_REDIRECT_URI")
        linkConfiguration.oauthNonce = "YOUR_OAUTHNONCE1"

        let linkViewDelegate = self

        let linkViewController = PLKPlaidLinkViewController(linkToken: "<#GENERATED_LINK_TOKEN#>", configuration: linkConfiguration, delegate: linkViewDelegate)
         if (UI_USER_INTERFACE_IDIOM() == .pad) {
             linkViewController.modalPresentationStyle = .formSheet
         }
        present(linkViewController, animated: true)
        // <!-- SMARTDOWN_PRESENT_CUSTOM -->
    }
```

## About the LinkDemo Xcode projects

Plaid Link can be used for different use-cases and the sample applications demonstrate how to use Plaid Link for iOS for each use-case.
For clarity between the different use cases each use case specific example showing how to integrate Plaid Link for iOS is implemented in a Swift extension and Objective-C category.

Before building and running the sample application replace any Xcode placeholder strings (like `<#GENERATED_LINK_TOKEN#>`) in code with the appropriate value so that Plaid Link is configured properly. For convenience the Xcode placeholder strings are marked as compile-time warnings.

Then select your desired use-case in [`ViewController.didTapButton`](/search?q=didTapButton+extension%3Am+extension%3Aswift&unscoped_q=didTapButton+extension%3Am+extension%3Aswift) and build and run the demo application to experience the particular Link flow for yourself.

🤖 Throughout the source code there are HTML-like comments such as <code>&lt;!-- SMARTDOWN_PRESENT_CUSTOM --&gt;</code>, they are used to update the code examples in the [documentation][link-ios-docs] from the sample applicationsʼ code ensuring that the examples are up-to-date and working as intended.

[link-ios-docs]: https://plaid.com/docs/link/ios
[link-sdk-version]: https://img.shields.io/cocoapods/v/Plaid
[link-sdk-url]: https://cocoapods.org/pods/Plaid

## LinkKit v1 reference guide

**Warning: this guide is legacy documentation for LinkKit v1 only**. This guide is not kept up-to-date and serves to provide legacy documentation for users on old versions of LinkKit. For the latest Plaid documentation, see [plaid.com/docs](https://plaid.com/docs).

#### Plaid Link for iOS Quickstart

1. Integrate the `LinkKit.framework` into your application using CocoaPods, Carthage, or manually.
2. Add a custom Run Script build phase containing this script to run after the Embed Frameworks build phase. This prepares `LinkKit.framework` for distribution through the App Store. Failing to do so will very likely get your application rejected during App Store submission or review!
3. Import LinkKit
4. Adopt the `PLKPlaidLinkViewDelegate` protocol
5. Implement the delegate methods
6. Create a `link_token` from your server
7. Present the `PLKPlaidLinkViewController`

#### Integration

Plaid Link for iOS is an embedabble framework that is bundled and distributed within your application. There are several ways to obtain the necessary files and keep them up-to-date, we recommend using CocoaPods or Carthage. Regardless of what you choose, submitting a new version of your application with the updated `LinkKit.framework` to the App Store is required.


##### CocoaPods

To integrate `LinkKit.framework` using CocoaPods:

1. Add `pod 'Plaid'` to your project's Podfile

2. Run the following command to download and install the Plaid CocoaPods containing `LinkKit.framework`

`pod install`

3. Add a custom Run Script build phase (we recommend naming it Prepare for Distribution ) with the script below, which prepares the LinkKit.framework for distribution through the App Store. Failing to do so will very likely get your application rejected during App Store submission or review!

To update to newer releases in the future run the following command

```
pod update Plaid
```

##### Carthage
To integrate LinkKit.framework using Carthage:

1. Add binary "https://raw.githubusercontent.com/plaid/plaid-link-ios/master/LinkKit.json" to your project's Cartfile

2. Run the following command to download the latest version of LinkKit.framework

```
carthage update
```

3. Follow the manual instructions below

##### Manual

Get the latest version of LinkKit.framework and embed it into your application. If you chose to integrate using Carthage and have followed the steps above, `LinkKit.framework` has already been downloaded to `Carthage/Build/iOS/LinkKit.framework`.

Embed `LinkKit.framework` into your application, for example by dragging and dropping the file onto the Embed Frameworks build phase as shown below.

![embed.jpg](/images/embed.jpg)

Depending on the location of the `LinkKit.framework` on the filesystem you may need to change the Framework Search Paths build setting to avoid the error: `fatal error: 'LinkKit/LinkKit.h' file not found`, see image below.

The LinkDemo Xcode projects have it set to `FRAMEWORK_SEARCH_PATHS = $(PROJECT_DIR)/../` for example, since the `LinkKit.framework` file is shared between them and is kept in the directory that also contains the demo project directories.

![framework-search-paths](/images/framework_search_paths.jpg)

Finally, add a Run Script build phase (we recommend naming it Prepare for Distribution) with the script below. Be sure to run this build phase *after* the **Embed Frameworks** build phase (or **[CP] Embed Pods Frameworks** build phase when integrating using CocoaPods), otherwise `LinkKit.framework` will not be properly prepared for distribution and your application will likely be rejected during App Store submission or review.

![add-run-script-run-build-phase](/images/add_run_script_run_build_phase.jpg)


![edit-run-build-script](/images/edit_run_script_build_phase.jpg)

###### Prepare for distribution script

The script below removes any non-iOS device code from the framework which is included to support running LinkKit in the Simulator but may not be distributed via the App Store. Failing to do so will very likely get your application rejected during App Store submission or review!

Change the `${PROJECT_DIR}/LinkKit.framework` path in the example below according to your setup, and be sure to quote the filepaths when they contain whitespace.

```
  LINK_ROOT=${PODS_ROOT:+$PODS_ROOT/Plaid/ios}
  cp "${LINK_ROOT:-$PROJECT_DIR}"/LinkKit.framework/prepare_for_distribution.sh "${CODESIGNING_FOLDER_PATH}"/Frameworks/LinkKit.framework/prepare_for_distribution.sh
  "${CODESIGNING_FOLDER_PATH}"/Frameworks/LinkKit.framework/prepare_for_distribution.sh
```

#### Configuration
To enable support for third-party password managers in Plaid Link for iOS add `org-appextension-feature-password-management` to the `LSApplicationQueriesSchemes` array in the applicationʼs `Info.plist` (see LinkDemo example and the Information Property List Key Reference ).

The way to configure Plaid Link for iOS is by creating a custom instance of `PLKConfiguration` and passing it to the `PLKPlaidLinkViewController`.


#### Optional items
| ITEM             | DESCRIPTION                                                                                                                                       |
|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| oauthStateId     | An oauthStateId is required to support OAuth authentication flows when re-launching Link using one or more European country codes.                |
| oauthRedirectUri | An oauthRedirectUri is required to support OAuth authentication flows when launching Link and using one or more European country codes.           |
| oauthNonce       | An oauthNonce is required to support OAuth authentication flows when launching or re-launching Link and using one or more European country codes. |

#### Deprecated items
The following configuration items have been deprecated and will be removed in a future release.

| ITEM             | DESCRIPTION                                                                                                                                       |
|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| selectAccount     | Whether the user should select a specific account after successfully linking their bank account (default: NO ). |


Note: Use the Select Account view from the Dashboard to control whether or not your Link integration uses the Select Account view

#### Implementation
Plaid Link for iOS presents itself through the `PLKPlaidLinkViewController` class.

The steps to implement Plaid Link for iOS in your application are:

1. Import `LinkKit`
2. Adopt the `PLKPlaidLinkViewDelegate` protocol
3. Implement the delegate methods
4. Present the `PLKPlaidLinkViewController`

##### Import LinkKit
To use Plaid Link for iOS import `LinkKit` and its symbols into your code.

```
#import <LinkKit/LinkKit.h>
```

##### Adopt the PLKPlaidLinkViewDelegate protocol

In the LinkDemo applications this is handled by the main view controller object.

```
extension ViewController : PLKPlaidLinkViewDelegate
@interface ViewController (PLKPlaidLinkViewDelegate) <PLKPlaidLinkViewDelegate>
@end
```

##### Implement the delegate methods

The `PLKPlaidLinkViewDelegate` methods are the main communication back channel to your application for Plaid Link for iOS.

###### onSuccess

This delegate method is called when the user successfully linked their bank account with Plaid. Use this to upload and store the `publicToken` with your service for use with the Plaid API.

```
func linkViewController(_ linkViewController: PLKPlaidLinkViewController, didSucceedWithPublicToken publicToken: String, metadata: [String : Any]?) {
    dismiss(animated: true) {
        // Handle success, e.g. by storing publicToken with your service
        NSLog("Successfully linked account!\npublicToken: (publicToken)\nmetadata: (metadata ?? [:])")
        self.handleSuccessWithToken(publicToken, metadata: metadata)
    }
}
```

###### onExit

This delegate method is called when the user exited from Plaid Link or when an error occurred. In case of an error you may want to display error related information to user and have them retry linking their account.

```
func linkViewController(_ linkViewController: PLKPlaidLinkViewController, didExitWithError error: Error?, metadata: [String : Any]?) {
    dismiss(animated: true) {
        if let error = error {
            NSLog("Failed to link account due to: (error.localizedDescription)\nmetadata: (metadata ?? [:])")
            self.handleError(error, metadata: metadata)
        }
        else {
            NSLog("Plaid link exited with metadata: (metadata ?? [:])")
            self.handleExitWithMetadata(metadata)
        }
    }
}
```

###### onEvent
This optional delegate method is called when certain events in the Plaid Link flow have occurred, for example when the user selected an instutition. This enables your application to gain further insight into what is going on as the user goes through the Plaid Link flow. For details about the events see the link-web onEvent documentation.

```
func linkViewController(_ linkViewController: PLKPlaidLinkViewController, didHandleEvent event: String, metadata: [String : Any]?) {
    NSLog("Link event: (event)\nmetadata: (metadata ?? [:])")
}
```
The metadata contains the following keys, note that values can be `null`. All key values are strings unless otherwise specified.

|KEY                               |DESCRIPTION                                                                                                                                                                                                |
|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|kPLKMetadataAccountIdKey           |Identifier for the selected account                                                                                                                                                                        |
|kPLKMetadataAccountKey             |Dictionary.  Contains the keys `kPLKMetadataIdKey`, `kPLKMetadataNameKey`, `kPLKMetadataTypeKey`, `kPLKMetadataSubtypeKey`, and `kPLKMetadataMaskKey`. Only applicable when the `selectAccount` property is set to `true`|
|kPLKMetadataAccountsKey            |Dictionary.  Contains the keys `kPLKMetadataIdKey`, `kPLKMetadataNameKey`, `kPLKMetadataTypeKey`, `kPLKMetadataSubtypeKey`, and `kPLKMetadataMaskKey`. Only applicable when the `selectAccount` property is set to `true`|
|kPLKMetadataIdKey                  |Identifier for the selected account                                                                                                                                                                        |
|kPLKMetadataNameKey                |Name of the institution or selected account                                                                                                                                                                |
|kPLKMetadataTypeKey                |Type of the institution or selected account                                                                                                                                                                |
|kPLKMetadataSubtypeKey             |Subtype of the selected account                                                                                                                                                                            |
|kPLKMetadataMaskKey                |Mask of the selected account                                                                                                                                                                               |
|kPLKMetadataInstitutionKey         |Dictionary. Contains the keys `kPLKMetadataNameKey` and `kPLKMetadataInstitutionTypeKey`                                                                                                                       |
|kPLKMetadataStatusKey              |Indicates the point at which the user exited the Link flow (see explanation of possible values later in this document ).                                                                                                          |
|kPLKMetadataRequestIdKey           |An internal identifier that helps us track your request in our system, please include it in every support request                                                                                          |
|kPLKMetadataPlaidApiRequestIdKey   |An internal identifier that helps us track your request in our system, please include it in every support request                                                                                          |
|kPLKAPIv1MetadataInstitutionTypeKey|Identifier for the institution reported in the event callback metadata when using the legacy API                                                                                                           |
|kPLKMetadataInstitution_TypeKey    |Deprecated in favor of `kPLKAPIv1MetadataInstitutionTypeKey`                                                                                                                                                 |
|kPLKMetadataInstitutionTypeKey     |Deprecated in favor of `kPLKMetadataTypeKey`                                                                                                                                                                 |


In addition to the above the metadata contains the following keys for onEvent delegate method, note that values can be `null`. All key values are strings.

|CONSTANT                           |DESCRIPTION                                                                                                                                                                                                |
|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|kPLKMetadataInstitutionSearchQueryKey|The query used to search for institutions.                                                                                                                                                                 |
|kPLKMetadataInstitutionIdKey       |The ID of the selected institution.                                                                                                                                                                        |
|kPLKMetadataInstitutionNameKey     |The name of the selected institution.                                                                                                                                                                      |
|kPLKMetadataLinkRequestIdKey       |The request ID for the last request made by Link. This can be shared with Plaid Support to expedite investigation.                                                                                         |
|kPLKMetadataLinkSessionIdKey       |The link_session_id is a unique identifier for a single session of Link. It is always available and will stay constant throughout the flow.                                                                |
|kPLKMetadataErrorTypeKey           |The error type that the user encountered.                                                                                                                                                                  |
|kPLKMetadataErrorCodeKey           |The error code that the user encountered.                                                                                                                                                                  |
|kPLKMetadataErrorMessageKey        |The error message that the user encountered.                                                                                                                                                               |
|kPLKMetadataMFATypeKey             |If set, the user has encountered one of the following MFA types: code, device, questions, selections.                                                                                                      |
|kPLKMetadataViewNameKey            |The name of the view that is being transitioned to.                                                                                                                                                        |
|kPLKMetadataTimestampKey           |An ISO 8601 representation of when the event occurred.                                                                                                                                                     |
|kPLKMetadataExitStatusKey          |The status key indicates the point at which the user exited the Link flow.                                                                                                                                 |

Possible values for the `kPLKMetadataStatusKey` metadata:

|CONSTANT                           |DESCRIPTION                                                                                                                                                                                                |
|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|kPLKStatusConnected                |User completed the Link flow                                                                                                                                                                               |
|kPLKStatusRequiresQuestions        |User prompted to answer security question(s)                                                                                                                                                               |
|kPLKStatusRequiresSelections       |User prompted to answer multiple choice question(s)                                                                                                                                                        |
|kPLKStatusRequiresCode             |User prompted to provide a one-time passcode.                                                                                                                                                              |
|kPLKStatusChooseDevice             |User prompted to select a device at which to receive a one-time passcode.                                                                                                                                  |
|kPLKStatusRequiresCredentials      |User prompted to provide credentials for the selected financial institution or has not yet selected a financial institution                                                                                |
|kPLKStatusRequiresRecaptcha        |User prompted to verify they are human via reCAPTCHA                                                                                                                                                       |
|kPLKStatusInstitutionNotFound      |User exited the Link flow after unsuccessfully (no results returned) searching for a financial institution                                                                                                 |

#### Presentation

##### Presenting PLKPlaidLinkViewController 
 
Starting the Plaid Link for iOS experience is as simple as creating a new `link_token` and using it to initialize `PLKConfiguration` and `PLKPlaidLinkViewController`.

To create an instance of `PLKConfiguration`, use `initWithLinkToken:` where the only parameter is your generated `link_token`. You can then instantiate `PLKPlaidLinkViewController` with `initWithLinkToken:configuration:delegate:` where you will pass in your `link_token`, `PLKConfiguration` object, and delegate that you just created. The only step left is to present your `PLKPlaidLinkViewController`.

Present `PLKPlaidLinkViewController` with `link_token` and configuration

```
// With custom configuration
let linkConfiguration = PLKConfiguration(linkToken: "<GENERATED_LINK_TOKEN>")
let linkViewDeletgate = self
let linkViewController = PLKPlaidLinkViewController(
  linkToken: "<GENERATED_LINK_TOKEN>",
  configuration: linkConfiguration,
  delegate: linkViewDelegate)

if (UI_USER_INTERFACE_IDIOM() == .pad) {
    linkViewController.modalPresentationStyle = .formSheet;
}

present(linkViewController, animated: true)
```

If your integration is still using a `public_key` to initialize Link, view our [migration guide](https://plaid.com/docs/link/link-token-migration-guide) to upgrade to `link_tokens`. 


#### Update mode
Initializing Link iOS in update mode is no different than initializing Link iOS normally. The only difference is that you are using a `link_token` that is specifically configured for update mode.

[Learn more about how to create a `link_token` for update mode](https://plaid.com/docs/link/update-mode).


#### OAuth Support
To support OAuth, you will need to create an instance of `PLKConfiguration` and set `oauthRedirectUri` and `oauthNonce` on the configuration when initially launching Link.

Your `oauthRedirectUri` must be configured through Plaid's developer dashboard and should be configured as a universal link using an Apple App Association File. Custom URI schemes are not supported; a proper universal link must be used. For more details about this, see the Allowed redirect URIs section on our main documentation page.

For security, the `oauthNonce` must be uniquely generated for each login and at least 16 characters in length. We suggest using a UUID.

##### Initialize Link with oauthNonce and oauthRedirectUri

```
// With custom configuration
let linkConfiguration = PLKConfiguration(linkToken: "<GENERATED_LINK_TOKEN>")
linkConfiguration.oauthRedirectUri = "<YOUR_OAUTH_REDIRECT_URI>"
let oauthNonce = UUID().uuidString
linkConfiguration.oauthNonce = oauthNonce
let linkViewDelegate = self
let linkViewController = PLKPlaidLinkViewController(
  linkToken: "<GENERATED_LINK_TOKEN>"
  configuration: linkConfiguration,
  delegate: linkViewDelegate)
if (UI_USER_INTERFACE_IDIOM() == .pad) {
    linkViewController.modalPresentationStyle = .formSheet;
}
present(linkViewController, animated: true)
```

When re-initializing Link to complete the authentication flow, the `oauthNonce` from the original Link initialization must be passed into the `PLKConfiguration` and the returned `oauthStateId` must be used to initialize the `PLKPlaidLinkViewController`. You should use `PLKOAuthStateIdFromURL` to get the correct `oauthStateId` to use.

##### Re-initialize Link with oauthNonce and oauthStateId

```
// With custom configuration
let oauthStateId = PLKOAuthStateIdFromURL(url: "<YOUR_OAUTH_RESPONSE_URL>")
let linkConfiguration = PLKConfiguration(linkToken: "<GENERATED_LINK_TOKEN>")
linkConfiguration.oauthNonce = "<YOUR_OAUTH_NONCE>"
let linkViewDelegate = self
let linkViewController = PLKPlaidLinkViewController(
  linkToken: "<GENERATED_LINK_TOKEN>"
  oauthStateId: oauthStateId
  configuration: linkConfiguration
  delegate: linkViewDelegate)
if (UI_USER_INTERFACE_IDIOM() == .pad) {
    linkViewController.modalPresentationStyle = .formSheet;
}
present(linkViewController, animated: true)
For more details about this full flow and requirements around these additional fields, please visit the OAuth authentication flows section on our main documentation page.
```

#### Customization
The preferred and recommended method to customize LinkKit is to use customization feature in the dashboard, Plaid Link for iOS will automatically use the values you provide there.

#### Troubleshooting
When things work differently as expected LinkKit will use the value of the `PLKPLAIDLINK_DIAGNOSTICS` environment variable and enable different logging to the console. Supported values for `PLKPLAIDLINK_DIAGNOSTICS` and their corresponding log level are:

|VALUE                              |LOGLEVEL                           |
|-----------------------------------|-----------------------------------|
|0                                  |None                               |
|1                                  |Error                              |
|2                                  |Warning                            |
|3                                  |Info                               |
|4                                  |Debug                              |


Environment Variables can be added in the Arguments tab of the Run phase in the Scheme Editor (Product ► Scheme ► Edit Scheme ⌘< ).

![edit-scheme](/images/edit_scheme.jpg)

![edit-envvar](/images/edit_envvar.jpg)


## Acknowledgements

<details><summary>Portions of this software may utilize the following copyrighted material, the use of which is hereby acknowledged.</summary>

<!-- ACKNOWLEDGEMENTS -->

### i18next

The MIT License (MIT)

Copyright (c) 2013-2015 PrePlay, Inc.

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

### OCHamcrest

OCHamcrest by Jon Reid, https://qualitycoding.org/
Copyright 2017 hamcrest.org
All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

Neither the name of Hamcrest nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

(BSD License)

### OCMockito

OCMockito by Jon Reid, https://qualitycoding.org/
Copyright 2017 Jonathan M. Reid

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

(MIT License)

-----

TPDWeakProxy:

The MIT License (MIT)

Copyright © 2013 Tetherpad

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

### onepassword-app-extension

Copyright (c) 2014 AgileBits Inc.

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
### SimulatorStatusMagiciOS

The MIT License (MIT)

Copyright (c) 2014 Shiny Development

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

</details>
