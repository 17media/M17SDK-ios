# M17SDK

For complete document, welcome to visit our [document page](https://17media.github.io/M17SDK-ios/).

- [Intallation](#intallation)
- [Usage](#usage)
- [Launch SDK](#launch-sdk)
- [Create live list view controller in default cell layout](#create-live-list-view-controller-with-default-cell-layout)
- [Setup your custom layout for live cells](#setup-your-custom-layout-for-live-cells)

## Intallation
### Prerequisites
To use the M17SDK for iOS Swift and Objective-C, you need:
- iOS 10.0 or later as the deployment target.
- Xcode 11 or later.

### CocoaPods
[CocoaPods](https://cocoapods.org/) is a dependency manager for Cocoa projects. For usage and installation instructions, visit their website. To integrate M17SDK into your Xcode project using CocoaPods, specify it in your _Podfile_:

    
    pod 'M17SDK', '0.1.0-rc.6'
    

### Carthage
[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager that builds your dependencies and provides you with binary frameworks. To integrate M17SDK into your Xcode project using Carthage, specify it in your _Cartfile_:

    
    github "17media/M17SDK-ios" "0.1.0-rc.6"
    github "SDWebImage/SDWebImage" ~> 5.0.0
    github "SnapKit/SnapKit" ~> 5.0.0
    github "pubnub/swift" ~> 2.0
    
On your application targets’ Build Phases settings tab, click the + icon and choose New Run Script Phase. Create a Run Script in which you specify your shell (ex: /bin/sh), add the following contents to the script area below the shell:

    /usr/local/bin/carthage copy-frameworks

Add the paths to the frameworks you want to use under “Input Files". For example:

    $(SRCROOT)/Carthage/Build/iOS/M17SDK.framework
    $(SRCROOT)/Carthage/Build/iOS/PubNub.framework
    $(SRCROOT)/Carthage/Build/iOS/SDWebImage.framework
    $(SRCROOT)/Carthage/Build/iOS/SnapKit.framework
    $(SRCROOT)/Carthage/Build/iOS/Ably.framework
    $(SRCROOT)/Carthage/Build/iOS/KSCrashAblyFork.framework
    $(SRCROOT)/Carthage/Build/iOS/SAMKeychain.framework
    $(SRCROOT)/Carthage/Build/iOS/SocketRocketAblyFork.framework
    $(SRCROOT)/Carthage/Build/iOS/ULID.framework
    $(SRCROOT)/Carthage/Build/iOS/msgpack.framework

Add the paths to the copied frameworks to the “Output Files”. For example:

    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/M17SDK.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/PubNub.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/SDWebImage.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/SnapKit.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/Ably.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/KSCrashAblyFork.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/SAMKeychain.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/SocketRocketAblyFork.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/ULID.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/msgpack.framework

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

// Set the user UUID of your platform to SDK if needed.
config.userIdentifier = @"My user UUID";

// Launch the SDK.
[M17SDK launchWithConfiguration:config completion:^(NSError * _Nullable error) {
	if (!error) {
	    NSLog(@"Authorized!");
	} else {
	    NSLog(@"Uh-oh, something is really wrong - %@", error);
	}
}];
```

#### Troubleshooting
- If you need the accurate data report from M17 side, you **MUST** set the _userIdentifier_ in config to bind the 17 App user with the user of your own App.
- **Please make sure that you call others M17 SDK APIs after the launch completion callback returned without any errors,** or you would get unexpected result. For example, you would get _nil_ value when creating _LiveListViewController_.
- If you get the error, please check the error message. Please refer _M17SDKAuthError_ and use _Switch-case_ to handle the error properly.

### Create live list view controller with default cell layout

```objective-c
/**
 There are three types of filter. `regionFilters`, `labelFilters`, `userIdFilters`.
 These functions return all fiters by default. You can check the id and modify the array.
*/
NSArray<M17SDKRegionFilter *> *rfs =  [M17SDKLiveFilterFactory regionFilters];

// You MUST initialize config with one kind of filter.
M17SDKLiveListConfiguration *config = [[M17SDKLiveListConfiguration alloc] initWithRegions:rfs];;

// Create the coordinator.
M17SDKRootCoordinator *root = [[M17SDKRootCoordinator alloc] init];

// Create a new live list view controller by cofig.
UIViewController *vc = [root createLiveListViewControllerWithConfiguration:config];

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
