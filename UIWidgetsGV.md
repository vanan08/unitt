![http://unitt.googlecode.com/svn/wiki/gradientView.png](http://unitt.googlecode.com/svn/wiki/gradientView.png)

# Introduction #
The Gradient View gives a simple vertical gradient as a background for your view. The start and end colors and alpha values are customizable.

# Details #

Using the gradient view is simple. It can be created and placed in your code, your custom views can inherit from it, or it can applied to any base view in your nib by simply changing the class to GradientView. Either way, once your view is loaded, you will need to set the following properties:
  * **startColor:** Start color for the gradient.
  * **endColor:** End color for the gradient.

# Sample Code #

The screen above was made using a nib with one view with the class of GradientView. The following code is all that was needed to configure this view.
```
- (void)viewDidLoad
{
    [super viewDidLoad];
    
    //apply view properties
    ((GradientView*) self.view).startColor = [UIColor colorWithRed: 38/255.0 green: 47/255.0 blue: 160/255.0 alpha: 1.0];
    ((GradientView*) self.view).endColor = [UIColor colorWithRed: 20/255.0 green: 25/255.0 blue: 86/255.0 alpha: 1.0];
}
```