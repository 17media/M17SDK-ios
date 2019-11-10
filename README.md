# M17SDKCoreKit.framework

## Intallation
### Prerequisites
To use the M17SDKCoreKit for iOS Swift and Objective-C, you need:
- iOS 10.0 or later as the deployment target.
- Xcode 10 or later.
- For now, M17SDKCoreKit is only able to be installed through _Carthage_. We'll make it support manual itegration and Cocoapods in future.

### Carthage
1. Get Carthage by running `brew install carthage`
1. Create a _Cartfile_ in the same directory where your `.xcodeproj` or `.xcworkspace` is
1. List the
dependencies in the _Cartfile_:

    ```
    github "17media/M17SDKCoreKit.framework" "0.1.0-beta.1"
    github "Alamofire/Alamofire" "5.0.0-rc.3"
    github "Alamofire/AlamofireImage" "4.0.0-beta.6"
    github "SnapKit/SnapKit" ~> 5.0.0
    ```
	
1. Run `carthage update --platform ios`
1. A `Cartfile.resolved` file and a `Carthage` directory will appear in the same directory where your `.xcodeproj` or `.xcworkspace` is
1. Drag the built `.framework` binaries from `Carthage/Build/<platform>` into your application’s Xcode project (untick _Copy items if needed_).
1. On your application targets’ _Build Phases_ settings tab, click the _+_ icon and choose _New Run Script Phase_. Create a Run Script in which you specify your shell (ex: `/bin/sh`), add the following contents to the script area below the shell:

    ```sh
    /usr/local/bin/carthage copy-frameworks
    ```

- Add the paths to the frameworks under “Input Files".

    ```
    $(SRCROOT)/Carthage/Build/iOS/M17SDKCoreKit.framework
    $(SRCROOT)/Carthage/Build/iOS/Alamofire.framework
    $(SRCROOT)/Carthage/Build/iOS/AlamofireImage.framework
    $(SRCROOT)/Carthage/Build/iOS/SnapKit.framework
    ```

- Add the paths to the copied frameworks to the “Output Files”.

    ```
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/M17SDKCoreKit.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/Alamofire.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/AlamofireImage.framework
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/SnapKit.framework
    ```
    
## Usage
### Import
- Declare `@import M17SDKCoreKit;` in _.m_ files to access classes or protocols.
- Use forward declarations in _.h_ files if you want to reference classes or protocols in an Objective-C interface. For example:

    ```
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

    
    // Create default config.
    M17SDKLiveListConfig *config = [M17SDKLiveListConfig defaultConfig];
    
    // New the coordinator.
    M17SDKRootCoordinator *root = [[M17SDKRootCoordinator alloc] init];
    
    // Create a new live list view controller by cofig.
    UIViewController *vc = [root createLiveListViewControllerWithConfig:config];
    
    // Embed, present ,push or do whatever you want.
    [self presentViewController:vc animated:YES completion:nil];
    
### How to setup your custom layout for live cells
1. Subclass a UIView to confirm _M17SDKLiveCellLayout_ protocol.

    `@interface DummyView : UIView <M17SDKLiveCellLayout>`
    
2. The protocol has two properties, one is _coverImageView_, the other is _streamerNameLabel_. Implement each property to bind the data from M17SDK with your custom view. These two properties can be implemented by Storyboard(IBOutlet), Xib(IBOutlet), or code(without IBOutlet).

    ```
    @property (strong, nonatomic) IBOutlet UIImageView *coverImageView;
    @property (strong, nonatomic) IBOutlet UILabel *streamerNameLabel;
    ```
    
3. Implement the _LiveCellLayoutHandler_ block. The block will be called when the collection view dequeues cells and insert the views into the cells. Because cells are reused, you must always generate a new UIView<M17SDKLiveCellLayout> instance in block.
    
    ```
    config.liveCellLayoutHandler = ^UIView<M17SDKLiveCellLayout> * _Nonnull{
        UIView<M17SDKLiveCellLayout> *view = [DummyView createDummyView];
        return view;
    };
    
    UIViewController *vc = [root createLiveListViewControllerWithConfig:config];
    ```
    
4. Run and see the result.
