![http://unitt.googlecode.com/svn/wiki/homeLandscape.png](http://unitt.googlecode.com/svn/wiki/homeLandscape.png)

# Introduction #

The Home View Controller class is meant to give you a scrollable, rotatable view of a large icon grid to open subviews. This is typically placed inside a navigation view controller. It is very customizable and provides a toolbar, if so desired. You can specify an image, label, and UIViewController individually for each item (our example above uses the same image for all items)

# Setup Your Home View Controller #

  1. We are going to use a simple window project and manually add a UINavigationController object to it. Change your application:didFinishLaunchingWithOptions method to the following:
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // Override point for customization after application launch.
    UINavigationController* navigation = [[UINavigationController alloc] init];
    navigation.navigationBar.barStyle = UIBarStyleBlack;
    navigation.navigationBar.translucent = NO;
    navigation.navigationBar.tintColor = [UIColor colorWithRed:0 green:0 blue:128/255.0 alpha:1.0];
    
    //add nav view to window
    [self.window addSubview:navigation.view];
    [self.window makeKeyAndVisible];
    return YES;
}
```
  1. Now let's add our HomeViewController, remember to import HomeViewController.h
```
    //create our hvc & add to nav
    HomeViewController* home = [self getHomeViewController];
    [navigation pushViewController:home animated:NO];

    //add nav view to window
    //...
```
  1. Add our getHomeViewController method
```

- (HomeViewController*) getHomeViewController
{   
    HomeViewController* hvc = [[[HomeViewController alloc] init] autorelease];
    
    //setup the hvc
    hvc.title = @"Sample Home";
    hvc.startColor = [UIColor colorWithRed: 38/255.0 green: 47/255.0 blue: 160/255.0 alpha: 1.0];
    hvc.endColor = [UIColor colorWithRed: 20/255.0 green: 25/255.0 blue: 86/255.0 alpha: 1.0];
    hvc.margin = CGSizeMake(11, 11); //minimum of 11x11 margins
    hvc.itemSize = CGSizeMake(92, 116); //items will be 92 wide and 116 tall
    hvc.toolbarHeight = 32;
    hvc.useToolbar = YES;
    
    return hvc;
}
```

# Add Items to the Home Screen #

Let's add some code to our getHomeViewController method to add the items for us
```
    //add items to home screen using the button.png in our project as each icon
    [hvc addItem:@"button1" controller:[[UIViewController alloc] init] icon:[UIImage imageNamed:@"button"] label:@"Button #1"];
    [hvc addItem:@"button2" controller:[[UIViewController alloc] init] icon:[UIImage imageNamed:@"button"] label:@"Button #2"];
    [hvc addItem:@"button3" controller:[[UIViewController alloc] init] icon:[UIImage imageNamed:@"button"] label:@"Button #3"];
    [hvc addItem:@"button4" controller:[[UIViewController alloc] init] icon:[UIImage imageNamed:@"button"] label:@"Button #4"];
    [hvc addItem:@"button5" controller:[[UIViewController alloc] init] icon:[UIImage imageNamed:@"button"] label:@"Button #5"];
    [hvc addItem:@"button6" controller:[[UIViewController alloc] init] icon:[UIImage imageNamed:@"button"] label:@"Button #6"];
    [hvc addItem:@"button7" controller:[[UIViewController alloc] init] icon:[UIImage imageNamed:@"button"] label:@"Button #7"];
```

# Add Items to the Home Screen's Toolbar #

Let's add some code to our getHomeViewController method to add a notification item to the toolbar
```
    //setup the toolbar
    hvc.toolbarHeight = 32;
    hvc.useToolbar = YES;
    
    //create a toolbar item and add to view
    UIBarButtonItem* notificationView = [[[UIBarButtonItem alloc] initWithTitle:@"Notifications" style:UIBarButtonItemStylePlain target:nil action:nil] autorelease];
    UIBarButtonItem *flexibleSpace = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemFlexibleSpace target:nil action:nil];                                                                                                                         
    NSArray* tbItems = [NSArray arrayWithObjects:flexibleSpace, notificationView, flexibleSpace, nil];
    hvc.toolbarItems = tbItems;
```

# Add URL Items to the Home Screen #

If you are using a UrlNavigationController in your project, you can have the items on the home screen open URLs instead. The HomeViewController will check to make sure that your navigation controller is a, or inherits from, a UrlNavigationController before using a URL. However, to do so, simply change your code that adds items to the home screen to look something like this...
```
    //add items to home screen using the button.png in our project as each icon
    [hvc addItem:@"button1" url:[NSURL URLWithString:@"myapp://myurl/button/1"] icon:[UIImage imageNamed:@"button"] label:@"Button #1"];
    [hvc addItem:@"button2" url:[NSURL URLWithString:@"myapp://myurl/button/2"] icon:[UIImage imageNamed:@"button"] label:@"Button #2"];
    [hvc addItem:@"button3" url:[NSURL URLWithString:@"myapp://myurl/button/3"] icon:[UIImage imageNamed:@"button"] label:@"Button #3"];
    [hvc addItem:@"button4" url:[NSURL URLWithString:@"myapp://myurl/button/4"] icon:[UIImage imageNamed:@"button"] label:@"Button #4"];
    [hvc addItem:@"button5" url:[NSURL URLWithString:@"myapp://myurl/button/5"] icon:[UIImage imageNamed:@"button"] label:@"Button #5"];
    [hvc addItem:@"button6" url:[NSURL URLWithString:@"myapp://myurl/button/6"] icon:[UIImage imageNamed:@"button"] label:@"Button #6"];
    [hvc addItem:@"button7" url:[NSURL URLWithString:@"myapp://myurl/button/7"] icon:[UIImage imageNamed:@"button"] label:@"Button #7"];
```
For more information on UrlNavigationController, see [Using the Url Navigation Controller](UIWidgetsUrlNav.md).

# Customizing Your Home View Controller #

There are factory methods in each of the various view classes that you can extend to customize or replace the default UIViews used to construct each of these items. It should be as simple as extending HomeViewController and implementing your own factory methods.