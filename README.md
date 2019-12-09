# M17SDK

## Intallation
### Prerequisites
To use the M17SDK for iOS Swift and Objective-C, you need:
- iOS 10.0 or later as the deployment target.
- Xcode 11 or later.

### CocoaPods
[CocoaPods](https://cocoapods.org/) is a dependency manager for Cocoa projects. For usage and installation instructions, visit their website. To integrate M17SDK into your Xcode project using CocoaPods, specify it in your _Podfile_:

    
    pod 'M17SDK', '0.1.0-beta.5'
    

### Carthage
[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager that builds your dependencies and provides you with binary frameworks. To integrate M17SDK into your Xcode project using Carthage, specify it in your _Cartfile_:

    
    github "17media/M17SDK-ios" "0.1.0-beta.5"
    
	
### Manually
If you prefer not to use any of the aforementioned dependency managers, you can integrate M17SDK into your project manually.
1. Download and unzip the [framework](https://github.com/17media/M17SDK-ios/releases).
1. Drag the `.framework` into your application’s Xcode project (tick _Copy items if needed_).
1. On your application targets’ _General_ settings tab, section _Frameworks, Libraries and Embedded Content_, set the framework to _Embedded & Sign_.
![Image](https://github.com/17media/M17SDK-ios/blob/master/embed-framework-into-your-project.png)

## Usage
### Import
- Import _M17SDK_ in _.m_ files to access classes or protocols.

```objective-c
@import M17SDK;
```

- Use forward declarations in _.h_ files if you want to reference classes or protocols in an Objective-C interface. For example:

```objective-c
@protocal M17SDKLiveCellLayout;
```

### Classes, Enums and Protocols
#### M17SDK
In charge of validating license key and launch the SDK.

#### M17SDKConfiguration
To setup a set of configurations for _M17SDK_ launcher.

### M17SDKAuthError
Error codes of SDK authorization error.

#### M17SDKRootCoordinator
In charge of generating and navigating view controllers.

#### M17SDKLiveListConfiguration
To setup a set of configurations for live list view controller.

#### M17SDKLiveCellLayout
This protocol defines the abstract interfaces of the live cell layout. Confirm this protocol to customize your own live cells.

### Launch the SDK

```objective-c
// Create config by licenses key.
M17SDKConfiguration *config = [[M17SDKConfiguration alloc] initWithLicenseKey:@"jYfYR8jmh5AQTlCciBv2"];

// Set the user UUID of your platform to SDK if needed.
config.userIdentifier = @"My user UUID";

// Launch the SDK.
[M17SDK lauchWithConfiguration:config completion:^(NSError * _Nullable error) {
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

### Create the live list view controller in default cell layout

```objective-c
// Create default config.
M17SDKLiveListConfiguration *config = [M17SDKLiveListConfiguration defaultConfig];

// Create the coordinator.
M17SDKRootCoordinator *root = [[M17SDKRootCoordinator alloc] init];

// Create a new live list view controller by cofig.
UIViewController *vc = [root createLiveListViewControllerWithConfiguration:config];

// Embed, present ,push or do whatever you want.
[self presentViewController:vc animated:YES completion:nil];
```

#### Troubleshooting
- **Before calling _createLiveListViewControllerWithConfiguration_, please make sure the _M17SDK_ has been launched successfully, or you would get _nil_ value.**
    
### How to setup your custom layout for live cells
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
