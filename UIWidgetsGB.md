![http://unitt.googlecode.com/svn/wiki/glossyButtons.png](http://unitt.googlecode.com/svn/wiki/glossyButtons.png)

# Introduction #

The Glossy Button gives you two different appearances. A simple glossy button and a gel glossy button.

# Details #

Using the buttons are simple. They can be created and placed in your code, or simply drag a UIButton onto your nib and change the class to GlossyButton. Either way, once your view is loaded, you will need to set the following properties:
  * **gradientColor:** Choose a base color for your gradient. If you make it too light, the highlight will get lost or washed out. This may take some experimenting to get right.
  * **useGelAppearance:** True if you want the gel appearance, otherwise false for the simple gloss

# Sample Code #

The screen above was made using a nib with 8 buttons and assigning outlets to each. The following code is all that was needed to configure the buttons.
```
- (void)viewDidLoad
{
    [super viewDidLoad];
    
    //apply simple properties
    redSimple.useGelAppearance = NO;
    redSimple.gradientColor = [UIColor colorWithRed:128/255.0 green:0 blue:0 alpha:1.0];
    greenSimple.useGelAppearance = NO;
    greenSimple.gradientColor = [UIColor colorWithRed:0 green:128/255.0 blue:0 alpha:1.0];
    blueSimple.useGelAppearance = NO;
    blueSimple.gradientColor = [UIColor colorWithRed:0 green:0 blue:128/255.0 alpha:1.0];
    purpleSimple.useGelAppearance = NO;
    purpleSimple.gradientColor = [UIColor colorWithRed:128/255.0 green:0 blue:128/255.0 alpha:1.0];
    
    //apply gel properties
    redGel.useGelAppearance = YES;
    redGel.gradientColor = [UIColor colorWithRed:128/255.0 green:0 blue:0 alpha:1.0];
    greenGel.useGelAppearance = YES;
    greenGel.gradientColor = [UIColor colorWithRed:0 green:128/255.0 blue:0 alpha:1.0];
    blueGel.useGelAppearance = YES;
    blueGel.gradientColor = [UIColor colorWithRed:0 green:0 blue:128/255.0 alpha:1.0];
    purpleGel.useGelAppearance = YES;
    purpleGel.gradientColor = [UIColor colorWithRed:128/255.0 green:0 blue:128/255.0 alpha:1.0];
}
```