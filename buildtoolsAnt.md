# Introduction #

If you already use Ant, then making use of the modeldata project is simply a matter of adding the buildtools target and taskdef to your build.xml. You must be using Ant 1.7 or greater.


# Details #

  1. Add the modeldata jar to your project's classpath in your build.xml
  1. Add a taskdef for the buildtools jar in your build.xml. You should have a classpath defined that points to your project's classpath and the buildtools jar. Use this in your taskdef to tell it what classpath to use. You may use the "classpath" or "classpathref" attribute. See the Ant documentation for more information. You must specify the resource to use, "buildtools.properties":
```
<taskdef resource="buildtools.properties" classpathref="project.class.path"/>
```
  1. Add the ant target to actually process your annotations.
    * source: This tells the target where your source files are located. You can use normal includes and so forth to define a specific set of files.
    * output: This tells the target where it should save any generated files. This must be an existing directory. Typically, this is the same as your source directory.
    * classpath: This is the classpath required to execute the target. Typically, this is the same classpath as used to define the taskdef.
```
<target name="modeldata">
    <modeldata source="src/java" output="src/java">
        <classpath refid="project.class.path"/>
    </modeldata>
</target>
```