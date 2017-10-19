# Import Facebook ThirdLogin

## CocoaPods

    pod 'FacebookCore'
    pod 'FacebookLogin'
    pod 'FacebookShare'

## Carthage

    github "facebook/facebook-sdk-swift"

## Import
    Bolts.framework
    FBSDKCoreKit.framework
    FBSDKLoginKit.framework
    FBSDKPlacesKit.framework
    FBSDKShareKit.framework
    FacebookLogin.framework
    FacebookCore.framework
    FacebookShare.framework

## If you don't use CocoaPods or Carthage
you have to download [IOS SDK](https://origincache.facebook.com/developers/resources/?id=facebook-ios-sdk-current.zip) to Import

then go **Build Settings** to add framework path to **Framework Search Paths**

## Set FB Account to Developer
[to become developer](https://developers.facebook.com/?advanced_app_create=true)

Login Facebook to countinue

## Set project to Facbook Apps
[to set project](https://developers.facebook.com/apps/)

- 1.add apps
- 2.set name(whatever)
- 3.choose platform
- 4.step too step.....

## Set Info.plist
```
<key>LSApplicationQueriesSchemes</key>
<array>
	<string>fbapi</string>
	<string>fb-messenger-api</string>
	<string>fbauth2</string>
	<string>fbshareextension</string>
</array>
<key>CFBundleURLTypes</key>
<array>
	<dict>
		<key>CFBundleURLSchemes</key>
		<array>
        <string>fb{你Facebook Developer那個應用程式的ID}</string>
		</array>
	</dict>
</array>
<key>FacebookAppID</key>
<string>{你Facebook Developer那個應用程式的ID}</string>
<key>FacebookDisplayName</key>
<string>{你Facebook Developer那個應用程式的name}</string>
```

### AppDelegate.swift
``` swift
    import FBSDKCoreKit

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        
        return true
    }
    
    func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
        return FBSDKApplicationDelegate.sharedInstance().application(application, open: url, sourceApplication: sourceApplication, annotation: annotation)
    }
    func applicationDidBecomeActive(_ application: UIApplication) {
        // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.
        FBSDKAppEvents.activateApp()
    }
```

### ViewController.swift
```swift
    import FBSDKLoginKit
    class ViewController: UIViewController,FBSDKLoginButtonDelegate {
        func loginButton(_ loginButton: FBSDKLoginButton!, didCompleteWith result: FBSDKLoginManagerLoginResult!, error: Error!) {
        print("attempt to fetch profile......")
        
        let parameters = ["fields": "email, first_name, last_name, picture.type(large)"]
        
        FBSDKGraphRequest(graphPath: "me", parameters: parameters).start(completionHandler: {
            connection, result, error -> Void in
            
            if error != nil {
                print("登入失敗")
                print("longinerror =\(error)")
            } else {
                
                if let resultNew = result as? [String:Any]{
                    
                    print("成功登入")
                    
                    let email = resultNew["email"]  as! String
                    print(email)
                    
                    let firstName = resultNew["first_name"] as! String
                    print(firstName)
                    
                    let lastName = resultNew["last_name"] as! String
                    print(lastName)
                    
                    if let picture = resultNew["picture"] as? NSDictionary,
                        let data = picture["data"] as? NSDictionary,
                        let url = data["url"] as? String {
                        print(url) //臉書大頭貼的url, 再放入imageView內秀出來
                    }
                }
            }
        })
        print("Y")
    }
    
    func loginButtonDidLogOut(_ loginButton: FBSDKLoginButton!) {
        print("N")
    }
    func loginButtonWillLogin(_ loginButton: FBSDKLoginButton!) -> Bool {
        return true
    }
    override func viewDidLoad() {
        super.viewDidLoad()
        let bt = FBSDKLoginButton()
        bt.center = self.view.center
        bt.readPermissions = ["public_profile", "email", "user_friends"]
        bt.delegate = self
        self.view.addSubview(bt)
        // Do any additional setup after loading the view, typically from a nib.
    }
```
