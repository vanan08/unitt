# Introduction #

The modeldata project is used to make generating DTOs or UI model objects for GXT a simple build process rather than a manual one. All GXT model objects must implement the ModelData interface. There is an annotation in GXT that provides a wrapper to do this for you. However, this wrapper requires a lot of casting and fetching if you want to use your object in a type safe manner. It also makes it more difficult to make use of a lot of the refactoring, encapsulation, and reference checking tools that most modern IDEs provide.

This project allows you to simply mark your DTO or model class with a single annotation and let the build process generate the required source code for you. You are always using an instance of your object and never have to worry if you have missed references or become frustrated in using the more advanced features of your IDE.


# Usage #

You will need to add the modeldata-x.x.x.jar to your project's classpath. Once this is complete you can begin. Let's take a sample class:
```
public class UserInfo
{
    private String name;
    private String id;

    public String getName()
    {
        return name;
    }

    public void setName(String aName)
    {
        name = aName;
    }

    public String getId()
    {
        return id;
    }
}
```

In order to change this class so it can automatically implement ModelData, you need to do the following:
  * Declare the class as abstract
  * Add the appropriate implements clause (I typically add Serializable at this time as well)
  * Add the @ModelDataBean annotation

Your new class should look like this:
```
@ModelDataBean
public abstract class UserInfo implements ModelData, Serializable
{
    private String name;
    private String id;

    public String getName()
    {
        return name;
    }

    public void setName(String aName)
    {
        name = aName;
    }

    public String getId()
    {
        return id;
    }
}
```


**Output**

When your build process runs it should automatically detect this and create a corresponding class whose name ends in "Bean", UserInfoBean. This class will be in a "bean" subpackage. Here is the output from our class:
```
package bean;

import...

@SuppressWarnings("serial")
public class UserInfoBean extends UserInfo
{
    @SuppressWarnings("unchecked")
    public <X> X get(String aProperty)
    {
        if (aProperty != null)
        {
            // try to return value
            if ("name".equals(aProperty))
            {
                return (X) getName();
            }
            else if ("id".equals(aProperty))
            {
                return (X) getId();
            }
        }
        // could not find value - just return null
        return null;
    }

    public Map<String, Object> getProperties()
    {
        Map<String, Object> properties = new HashMap<String, Object>();
        for (String property : getPropertyNames())
        {
            properties.put(property, get(property));
        }
        return properties;
    }

    public Collection<String> getPropertyNames()
    {
        Collection<String> names = new ArrayList<String>();
        names.add("name");
        names.add("id");
        return names;
    }

    @SuppressWarnings("unchecked")
    public <X> X remove(String aProperty)
    {
        return (X) set(aProperty, null);
    }

    @SuppressWarnings("unchecked")
    public <X> X set(String aProperty, X aValue)
    {
        if (aProperty != null)
        {
            if ("name".equals(aProperty))
            {
                X old = (X) get(aProperty);
                if (aValue instanceof java.lang.String)
                {
                    setName((java.lang.String) aValue);
                }
                return old;
            }
        }
        throw new UnsupportedOperationException("This model is read only.");
    }
}
```


**Using a Factory**

It can be advantageous to create a factory method on this base class so your code never directly calls the generated bean class. This should make it easier to find anywhere in your project that the actual dto/model object is being used. I typically do this using a static "create" method:
```
@ModelDataBean
public abstract class UserInfo implements ModelData, Serializable
{
    public static UserInfo create()
    {
        return new UserInfoBean();
    }
...
```


# Building #

There are three ways to process your annotation:
  * [Annotation Processor](buildtoolsApt.md) using Sun's apt tool.
  * [Maven Plugin](buildtoolsMaven.md)
  * [Ant Target](buildtoolsAnt.md)