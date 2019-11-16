# M17SDKCoreKit

## Intallation
### Prerequisites
To use the M17SDKCoreKit for iOS Swift and Objective-C, you need:
- iOS 10.0 or later as the deployment target.
- Xcode 11 or later.

### CocoaPods
[CocoaPods](https://cocoapods.org/) is a dependency manager for Cocoa projects. For usage and installation instructions, visit their website. To integrate M17SDKCoreKit into your Xcode project using CocoaPods, specify it in your _Podfile_:

    
    pod 'M17SDKCoreKit', '0.1.0-beta.2'
    

### Carthage
[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager that builds your dependencies and provides you with binary frameworks. To integrate M17SDKCoreKit into your Xcode project using Carthage, specify it in your _Cartfile_:

    
    github "17media/M17SDK-ios" "0.1.0-beta.2"
    
	
### Manually
If you prefer not to use any of the aforementioned dependency managers, you can integrate M17SDKCoreKit into your project manually.
1. Download and unzip the [framework](https://github.com/17media/M17SDK-ios/releases).
1. Drag the `.framework` into your application’s Xcode project (tick _Copy items if needed_).
1. On your application targets’ _General_ settings tab, section _Frameworks, Libraries and Embedded Content_, set the framework to _Embedded & Sign_.
![Image](https://github.com/17media/M17SDK-ios/blob/master/embed-framework-into-project.png)

## Usage
### Import
- Import _M17SDKCoreKit_ in _.m_ files to access classes or protocols.

```objective-c
@import M17SDKCoreKit;
```

- Use forward declarations in _.h_ files if you want to reference classes or protocols in an Objective-C interface. For example:

```objective-c
@protocal M17SDKLiveCellLayout;
```

### Classes
#### M17SDKRootCoordinator
In charge of generating and navigating view controllers.

#### M17SDKLiveListConfig
To setup a set of configuration for live list view controller.

#### M17SDKLiveCellLayout
The view has to confirm this protocol to implement your custom live cell layout.

### How to get the default live list view controller

```objective-c
// Create default config.
M17SDKLiveListConfig *config = [M17SDKLiveListConfig defaultConfig];

// New the coordinator.
M17SDKRootCoordinator *root = [[M17SDKRootCoordinator alloc] init];

// Create a new live list view controller by cofig.
UIViewController *vc = [root createLiveListViewControllerWithConfig:config];

// Embed, present ,push or do whatever you want.
[self presentViewController:vc animated:YES completion:nil];
```
    
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
    
3. Implement the _LiveCellLayoutHandler_ block. The block will be called when the collection view dequeues cells and insert the views into the cells. Because cells are reused, you must always generate a new UIView<M17SDKLiveCellLayout> instance in block.
    
```objective-c
config.liveCellLayoutHandler = ^UIView<M17SDKLiveCellLayout> * _Nonnull{
	UIView<M17SDKLiveCellLayout> *view = [DummyView createDummyView];
	return view;
};

UIViewController *vc = [root createLiveListViewControllerWithConfig:config];
```
    
4. Run and see the result.
