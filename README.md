# M17SDK

For complete document, welcome to visit our [document page](https://17media.github.io/M17SDK-ios/).

- [Installation](#installation)
- [Configuring](#configuring)
- [Usage](#usage)

## Installation
### Prerequisites
To use the M17SDK for iOS Swift and Objective-C, you need:
- iOS 10.0 or later as the deployment target.
- Xcode 11 or later.

### CocoaPods
[CocoaPods](https://cocoapods.org/) is a dependency manager for Cocoa projects. For usage and installation instructions, visit their website. To integrate M17SDK into your Xcode project using CocoaPods, specify it in your _Podfile_:

    pod 'M17SDK', '~> 1.3.0'
    
### Carthage
[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager that builds your dependencies and provides you with binary frameworks. To integrate M17SDK into your Xcode project using Carthage, specify it in your _Cartfile_:

    github "17media/M17SDK-ios" ~> 1.3.0
    github "17media/WSMTS" "1.0.1"
    github "17media/ijkplayer" "1.2.7"
    github "SDWebImage/SDWebImage" ~> 5.0.0
    github "SDWebImage/SDWebImageWebPCoder"
    github "pubnub/swift" ~> 2.0
    github "ably/ably-ios" ~> 1.1.15
    
On your application targets’ Build Phases settings tab, click the + icon and select _New Run Script Phase_. Create a Run Script in which you specify your shell (ex: /bin/sh), add the following contents to the script area below the shell:

    /usr/local/bin/carthage copy-frameworks

Add the paths to the frameworks you want to use under “Input Files". For example:

    $(SRCROOT)/Carthage/Build/iOS/M17SDK.framework
    $(SRCROOT)/Carthage/Build/iOS/PubNub.framework
    $(SRCROOT)/Carthage/Build/iOS/SDWebImage.framework
    $(SRCROOT)/Carthage/Build/iOS/Ably.framework
    $(SRCROOT)/Carthage/Build/iOS/KSCrashAblyFork.framework
    $(SRCROOT)/Carthage/Build/iOS/SAMKeychain.framework
    $(SRCROOT)/Carthage/Build/iOS/SocketRocketAblyFork.framework
    $(SRCROOT)/Carthage/Build/iOS/ULID.framework
    $(SRCROOT)/Carthage/Build/iOS/msgpack.framework
    $(SRCROOT)/Carthage/Build/iOS/SDWebImageWebPCoder.framework
    $(SRCROOT)/Carthage/Build/iOS/libwebp.framework

Add the paths to the copied frameworks to the “Output Files”. For example:

    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/M17SDK.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/PubNub.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/SDWebImage.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/Ably.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/KSCrashAblyFork.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/SAMKeychain.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/SocketRocketAblyFork.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/ULID.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/msgpack.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/SDWebImageWebPCoder.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/libwebp.framework

## Configuring
Insert the following properties into your _Info.plist_ file.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>medai17</string>
</array>
```

To make live room support background play, on your application targets’ Signing & Capabilities tab, click the +Capability icon and select _Background Modes_. Tick _Audio, AirPlay, and Picture in Picture_ mode.

![image](https://github.com/17media/M17SDK-ios/blob/master/Enable-background-mode.png)

## Usage
### Import
- Import _M17SDK_ in _.m_ files to access classes or protocols.

```objective-c
@import M17SDK;
```

- Use forward declarations in _.h_ files if you want to reference classes or protocols in an Objective-C interface. For example:

```objective-c
@protocol M17SDKLiveCellLayout;
```

### Launch SDK

```objective-c
// Create config by licenses key.
M17SDKConfiguration *config = [[M17SDKConfiguration alloc] initWithLicenseKey:@"jYfYR8jmh5AQTlCciBv2"];

// Launch the SDK.
[M17SDK launchWithConfiguration:config completion:^(NSError * _Nullable error) {
	if (!error) {
	    NSLog(@"Authorized!");
	} else {
	    NSLog(@"Uh-oh, something is really wrong - %@", error);
	}
}];
```

### External User ID
An external user ID is used to associate with M17 account. There's some features require users binding their account to M17 account beforehand. In this case, you have to set your user id as your external user id. If the external user id haven't been set, the app will prompt an error message if the feature requires users binding 17 account.


#### How to set it
Just simply call `updateExternalUserID(_:)`, a static method of `M17SDK` class.

In practical, you can specify your user id to our SDK in two common cases,
1. Update external user id after setting up M17SDK.
2. Update external user id once your user logged in and has a user id.

Example code
```objective-c
M17SDKConfiguration *config = [[M17SDKConfiguration alloc] initWithLicenseKey:kM17SDKLicenseKey];
[M17SDK launchWithConfiguration:config completion:^(NSError * _Nullable error) {
    /// ...
}];
[M17SDK updateExternalUserID:@"${THIRD_PARTY_APP_USER_ID}"];
```


#### Troubleshooting
- If you need accurate data report from M17 side, you **MUST** update the `externalUserID` by calling `M17SDK.updateExternalUserID(:_)` to associated your user identifier to 17 App user.
- **Please make sure that you call others M17 SDK APIs after the launch completion callback returned without any errors,** or you would get unexpected result. For example, you would get _nil_ value when creating _LiveListViewController_.
- If you get the error, please check the error message. Please refer _M17SDKAuthError_ and use _Switch-case_ to handle the error properly.

### Create live list view controller with default cell layout and listen live list contentOffset changed

```objective-c
/**
 There are three types of filter. `regionFilters`, `labelFilters`, `userIdFilters`.
 These functions return all fiters by default. You can check the id and modify the array.
*/
NSArray<M17SDKRegionFilter *> *regionFilters =  [M17SDKLiveFilterFactory regionFilters];
NSArray<M17SDKUserIdFilter *> *userIdFilters = [M17SDKLiveFilterFactory userIdFilters];

// You MUST initialize config with filters.
M17SDKLiveListConfiguration *config = [[M17SDKLiveListConfiguration alloc] initWithRegions:regionFilters
                                                                                        labels:nil
                                                                                       userIds:userIdFilters];

// Create the coordinator.
M17SDKRootCoordinator *root = [[M17SDKRootCoordinator alloc] init];

// Create a new live list view controller by cofig.
M17SDKLiveListViewController *vc = [root createLiveListViewControllerWithConfiguration:config];

// Embed, present ,push or do whatever you want.
[self presentViewController:vc animated:YES completion:nil];
```

#### Troubleshooting
- **Before calling _createLiveListViewControllerWithConfiguration_, please make sure the _M17SDK_ has been launched successfully, or you would get _nil_ value.**
    
### Setup your custom layout for live cells
1. Subclass a UIView to confirm _M17SDKLiveCellLayout_ protocol.

```objective-c
@interface DummyView : UIView <M17SDKLiveCellLayout>
```
    
2. The protocol has two properties, one is _coverImageView_, the other is _streamerNameLabel_. Implement each property to bind the data from M17SDK with your custom view. These two properties can be implemented by Storyboard(IBOutlet), Xib(IBOutlet), or code(without IBOutlet).

```objective-c
@property (strong, nonatomic) IBOutlet UIImageView *coverImageView;
@property (strong, nonatomic) IBOutlet UILabel *streamerNameLabel;
```
    
3. Implement the _LiveCellLayoutHandler_ block. **The block will be called when the collection view dequeues cells. Because each cell is different instance, you MUST always create a new _UIView\<M17SDKLiveCellLayout>_ instance in block**. Please DONOT create a view outside the block and reuse it in the block.
    
```objective-c
config.liveCellLayoutHandler = ^UIView<M17SDKLiveCellLayout> * _Nonnull{
	UIView<M17SDKLiveCellLayout> *view = [DummyView createDummyView];
	return view;
};

UIViewController *vc = [root createLiveListViewControllerWithConfig:config];
```

### Observe live list scroll properties
1. Make object which you want to handle live list contentOffset changed conform LiveListViewControllerDelegate
```objective-c
@interface ViewController ()<M17SDKLiveListViewControllerDelegate>
```

2. Implement LiveListViewControllerDelegate method
```objective-c
- (void)liveListViewController:(M17SDKLiveListViewController *)viewController didScrollTo:(CGPoint)contentOffset
- (void)liveListViewController:(M17SDKLiveListViewController *)viewController contentSizeDidChange:(CGSize)contentSize
```

3. Assign delegate after init LiveListViewController
```
M17SDKLiveListViewController *vc = [root createLiveListViewControllerWithConfiguration:config];

vc.delegate = delegateObject
```

### Sign Out
Call this method when log user out
```
[M17SDK signOut];
```
