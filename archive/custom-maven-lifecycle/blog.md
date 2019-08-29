Creating a custom lifecycle in Maven is pretty straightforward, although there doesn't seem to be many examples available online. This post details how to create a custom lifecycle - i.e. not one of the [default three Maven lifecycles](http://maven.apache.org/ref/3.1.1/maven-core/lifecycles.html) - and optionally set  default goals for the phases.

The complete source code for the examples below can pulled from the [https://github.com/nextmetaphor/maven](https://github.com/nextmetaphor/maven) repository.

### Create a Plugin
To create your own custom lifecycle you need to create a Maven plugin which only requires two files: `pom.xml` and `components.xml`

#### Setting up the Directory Structure
The diagram below shows where these files should be located. Note that this directory structure also contains the `CustomLifecyclePluginPhase1Mojo.java` mojo; this is not actually required for the custom lifecycle, and is simply used to validate when a particular phase is called later on.

![Plugin Directory Structure](https://nextmetaphor.files.wordpress.com/2016/10/d0518-directorystructure.png)

#### Configuring the pom.xml File
The `pom.xml` file is very straightforward, we are simply creating a maven-plugin.
```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0" 
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
        http://maven.apache.org/maven-v4_0_0.xsd">
      <modelVersion>4.0.0</modelVersion>
    
      <groupId>com.nextmetaphor.plugin</groupId>
      <artifactId>CustomLifecyclePlugin</artifactId>
      <version>1.0-SNAPSHOT</version>
      <packaging>maven-plugin</packaging>
      <name>Custom Lifecycle Plugin</name>
    
      <dependencies>
        <dependency>
          <groupId>org.apache.maven</groupId>
          <artifactId>maven-plugin-api</artifactId>
          <version>2.0</version>
        </dependency>
      </dependencies>
    </project>
```
#### Configuring the components.xml File
Configuring the `components.xml` file is also straightforward, the interesting fields are explained in more detail below:
```xml
    <component-set>
      <components>
        <component>
          <role>org.apache.maven.lifecycle.Lifecycle</role>
          <role-hint>customLifecyclePlugin_PackagingType</role-hint>
          <implementation>org.apache.maven.lifecycle.Lifecycle</implementation>
          <configuration>
            <id>customLifecyclePlugin_LifecycleID</id>
            <phases>
              <phase>phase1</phase>
              <phase>phase2</phase>
              <phase>phase3</phase>
            </phases>
            <default-phases>
              <phase1>com.nextmetaphor.plugin:CustomLifecyclePlugin:phase1Goal</phase1>
            </default-phases>
          </configuration>
        </component>
      </components>
    </component-set>
```    
For this particular example, the actual value of `role-hint` doesn’t appear to be used, although it looks to be a mandatory field and therefore cannot be removed. More useful is the `id` field, this is the name of the custom lifecycle being created, to complement the existing Maven lifecycles of `clean`, `default` and `site`.

The various `phase` elements detail the phases for the lifecycle in the order they will run. Finally, any phase that should default to a particular plugin goal should be defined in the `default-phases` section. In the example above, the first phase of the custom lifecycle – `phase1` – will by default call the `phase1Goal` on the `CustomLifecyclePlugin` in the `com.nextmetaphor.plugin` group.

And that’s all there is to it! Simply `mvn install` this plugin and we can use the new custom lifecycle from another project.

### Create A Project To Use The Custom Lifecycle
This demonstration project consists solely of a simple `pom.xml` file.

#### Configuring the pom.xml File
Here the custom lifecycle is introduced by the plugin reference. Note that the `extensions` property must be set to `true` for the custom lifecycle to become available to us.
```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
        http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
    
      <groupId>com.nextmetaphor.project</groupId>
      <artifactId>CustomLifecycleProject</artifactId>
      <version>1.0-SNAPSHOT</version>
      <packaging>jar</packaging>
      <name>Custom Lifecycle Project</name>
    
      <build>
        <plugins>
          <plugin>
              <groupId>com.nextmetaphor.plugin</groupId>
              <artifactId>CustomLifecyclePlugin</artifactId>
              <version>1.0-SNAPSHOT</version>
              <extensions>true</extensions>
          </plugin>
        </plugins>
      </build> 
    </project>
```
#### Confirming the Custom Lifecycle is Available
Invoke the `clean` phase on the project with verbose debug on using `mvn clean -X`. This should complete successfully. Scroll back through the logs until the available lifecycles are listed:
```
    [INFO] ------------------------------------------------------------------------
    [INFO] Building Custom Lifecycle Project 1.0-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    [DEBUG] Lifecycle default -> [validate, initialize, generate-sources,
            process-sources, generate-resources, process-resources, compile,
            process-classes, generate-test-sources, process-test-sources, 
            generate-test-resources, process-test-resources, test-compile, 
            process-test-classes, test, prepare-package, package, 
            pre-integration-test, integration-test, post-integration-test, 
            verify, install, deploy]
    [DEBUG] Lifecycle clean -> [pre-clean, clean, post-clean]
    [DEBUG] Lifecycle site -> [pre-site, site, post-site, site-deploy]
    [DEBUG] Lifecycle customLifecyclePlugin_LifecycleID -> [phase1, 
            phase2, phase3]
```

#### Invoking the Custom Lifecycle
As a final test we’ll invoke the `phase3` phase which should invoke the preceding phases: `phase1` and `phase2`. Only `phase1` has been set up with a default goal, in this case to call a mojo which will simply output some text.
```
    [CustomLifecycleProject]$ mvn phase3
    [INFO] Scanning for projects...
    [INFO]                                                                         
    [INFO] ------------------------------------------------------------------------
    [INFO] Building Custom Lifecycle Project 1.0-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    [INFO] 
    [INFO] --- 
           CustomLifecyclePlugin:1.0-SNAPSHOT:phase1Goal(default-phase1Goal)
           @ CustomLifecycleProject ---
    [INFO] Doing Phase 1 stuff. Oh yeah baby.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 0.755s
    [INFO] Finished at: Tue Jan 28 01:00:58 GMT 2014
    [INFO] Final Memory: 5M/141M
    [INFO] ------------------------------------------------------------------------
```