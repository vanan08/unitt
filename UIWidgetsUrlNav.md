![http://unitt.googlecode.com/svn/wiki/urlNavigator.png](http://unitt.googlecode.com/svn/wiki/urlNavigator.png)

# Introduction #
The Url Navigation Controller class is meant to give you a navigation controller that can create and push UIViewControllers on itself from a specified URL. There is a persistent variant that also allows you to save the state of your application views and restore them on launch.


# Details #

Using the UrlNavigationController is very straightforward. The controller is uses a UrlManager to resolve a URL into an instance of the correct UIViewController that is pushed onto the stack by the UrlNavigationController.

## Classes of Interest: ##
  * **UrlHandler:** A UrlHandler is responsible for determining if they can use the specified URL. If they can, they will construct an instance of the appropriate UIViewController (conforms to the UIViewControllerHasUrl protocol) and set the state on it in such a way that the currentUrl property returns the specified URL. A default handler is provided that simply attempts to match the urlPrefix to the beginning of the specified URL. See the PrefixUrlHandler class for more information.
  * **UrlManager:** A UrlManager has an ordered list of UrlHandlers. Each of these handlers are queried to see if they can handle the specified URL. The first one that can handle the specified URL is used.
  * **UrlNavigationController:** Extends NavigationController to allow URLs to be pushed onto the stack as UIViewControllers.
  * **PersistentUrlNavigationController:** Extends UrlNavigationController to provide a mechanism for persisting the active URLs and loading them later.
  * **UIViewControllerHasUrl:** Protocol used to designated UIViewControllers that can represent their model data or internal state as a URL. If given a URL, they are also able to load their state or model data from that URL.


# Sample Code #

## Using the Url Navigator ##
For our sample, we are going to create the screen above. Create an iOS application with a navigation controller. This application will mainly consist of a simple view whose model has only one value, a number. The UI contains only one button. When pressed this button will push a new view on the stack with a model value one higher. We will use a sample url of "test://number/numbervalue". For example, a view whose model data is the number 5, would have a URL of "test://number/5".  Pressing its button, would push a controller onto the stack with a model value of 6.

Let's create the button controller. Make sure it conforms to the !UIViewControllerHasUrl protocol and don't forget to hookup your actions and outlets...

**NumberedButtonController.h**
```
@property (assign) int number; //our model value
@property (retain) IBOutlet UIButton* button; //the button used to launch a new url
@property (nonatomic,readonly) UrlNavigationController* urlNavigationController; //convenience method to handle nils and cast the navigationController to a UrlNavigationController

- (IBAction) onPush: (UIButton*) aButton; //action used to push a new url onto the navigation controller
```

**NumberedButtonController.m**
```

@synthesize number;
@synthesize button;

- (NSURL*) currentUrl
{
    return [NSURL URLWithString:[NSString stringWithFormat:@"test://number/%i", self.number + 1]];
}

- (void) setCurrentUrl:(NSURL *)aCurrentUrl
{
    NSString* numberString = [[aCurrentUrl absoluteString] substringFromIndex:[@"test://number/" length]];
    self.number = [numberString intValue];
}

- (UrlNavigationController*) urlNavigationController
{    
    if (self.navigationController && [self.navigationController isKindOfClass:[UrlNavigationController class]]) 
    {
        return (UrlNavigationController*) self.navigationController;
    }
    
    return nil;
}

- (IBAction) onPush: (UIButton*) aButton
{
    if (self.urlNavigationController)
    {
        [self.urlNavigationController pushUrl:[NSURL URLWithString:[NSString stringWithFormat:@"test://number/%i", self.number + 1]] animated:YES];
    }
}

#pragma mark - View lifecycle

- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    self.button.titleLabel.text = [NSString stringWithFormat:@"%i", self.number + 1];
    self.title = [NSString stringWithFormat:@"%i", self.number];
}

- (void)viewDidUnload
{
    [super viewDidUnload];
    // Release any retained subviews of the main view.
    self.button = nil;
}
```

Now let's configure our url navigation controller. If you are loading the controller from a nib, make sure to change its class to UrlNavigationController. Crack open your application delegate and add a method to configure our url management...
```
- (void) configureUrls
{
    //create url handlers
    id<UrlHandler> handler = [[PrefixUrlHandler alloc] initWithControllerClass:[NumberedButtonController class] nibName:@"NumberedButtonController" nibBundle:nil urlPrefix:[NSURL URLWithString:@"test://number/"]];
    
    //create url manager & add url handlers
    UrlManager* manager = [[[UrlManager alloc] init] autorelease];
    [manager.urlHandlers addObject:handler];
    
    //get nav controller & set our url manager on it
    PersistentUrlNavigationController* controller = (PersistentUrlNavigationController*) self.navigationController;
    controller.urlManager = manager;
}
```

Now let's change our application to use our button controller and its url...
```
- (BOOL) application:(UIApplication *) application didFinishLaunchingWithOptions:(NSDictionary *) launchOptions
{
    // Override point for customization after application launch.
    // Add the navigation controller's view to the window and display.
    self.window.rootViewController = self.navigationController;

    //configure the urls
    [self configureUrls];

    //add our button controller
    UrlNavigationController* urlNav = (UrlNavigationController*) self.navigationController;
    [urlNav pushUrl:[NSURL URLWithString:@"test://number/0" animated:NO];

    [self.window makeKeyAndVisible];
    return YES;
}
```

## Adding Persistence ##
Now let's make this persistent, so every time we close and reopen the application, our current state is preserved. Doing this is very simple. First, we change our navigation controller class to be PersistentUrlNavigationController. Then we simply need to handle loading and saving. However, we don't want to blindly add a button controller if the application will do it automatically as part of loading the previous session. Thankfully, there is an easy way to determine whether this has been done or not. So let's change our application loading code to remove this call...
```
- (BOOL) application:(UIApplication *) application didFinishLaunchingWithOptions:(NSDictionary *) launchOptions
{
    // Override point for customization after application launch.
    // Add the navigation controller's view to the window and display.
    self.window.rootViewController = self.navigationController;

    //configure the urls
    [self configureUrls];

    [self.window makeKeyAndVisible];
    return YES;
}
```

Now we can add the load code to our configureUrls method and let it handle everything...
```
- (void) configureUrls
{
    //create url handlers
    id<UrlHandler> handler = [[PrefixUrlHandler alloc] initWithControllerClass:[NumberedButtonController class] nibName:@"NumberedButtonController" nibBundle:nil urlPrefix:[NSURL URLWithString:@"test://number/"]];
    
    //create url manager & add url handlers
    UrlManager* manager = [[[UrlManager alloc] init] autorelease];
    [manager.urlHandlers addObject:handler];
    
    //get nav controller & set our url manager on it
    PersistentUrlNavigationController* controller = (PersistentUrlNavigationController*) self.navigationController;
    controller.urlManager = manager;

    //load our previous state
    if (![controller loadActiveUrls:NO])
    {
        //we did not load any previous controllers, add the button controller
        UrlNavigationController* urlNav = (UrlNavigationController*) self.navigationController;
        [urlNav pushUrl:[NSURL URLWithString:@"test://number/0" animated:NO];
    }
}
```

The only thing missing is to automatically save our views on termination.
```
- (void) applicationWillTerminate:(UIApplication *) application
{
    //save our controller stack & state
    PersistentUrlNavigationController* controller = (PersistentUrlNavigationController*) self.navigationController;
    [controller saveActiveUrls];
}
```
There are many places to handle persisting your state. You probably want to check out applicationDidEnterBackground:. This method is only being used to demonstrate one way of saving your data. It is not meant as a prescription for ideal data/state management.