# Getting Started: Building Java Projects with Gradle

What you'll build
-----------------

This guide walks you through using Gradle to build a simple Java project. 

What you'll need
----------------

 - About 15 minutes
 - A favorite text editor or IDE
 - [JDK 6][jdk] or later

[jdk]: http://www.oracle.com/technetwork/java/javase/downloads/index.html

How to complete this guide
--------------------------

Like all Spring's [Getting Started guides](/getting-started), you can start from scratch and complete each step, or you can bypass basic setup steps that are already familiar to you. Either way, you end up with working code.

To **start from scratch**, move on to [Set up the project](#scratch).

To **skip the basics**, do the following:

 - [Download][zip] and unzip the source repository for this guide, or clone it using [git](/understanding/git):
`git clone https://github.com/springframework-meta/{@project-name}.git`
 - cd into `{@project-name}/initial`
 - Jump ahead to [Create a resource representation class](#initial).

**When you're finished**, you can check your results against the code in `{@project-name}/complete`.

<a name="scratch"></a>
Set up the project
------------------

First you'll need to setup a Java project for Gradle to build. To keep the focus on Gradle, you should keep the project as simple as possible for now.

### Create the directory structure

In a project directory of your choosing, create the following subdirectory structure; for example, with `mkdir -p src/main/java/hello` on *nix systems:

    └── src
        └── main
            └── java
                └── hello

### Creating Java classes

Within the `src/main/java/hello` directory, you can create any Java classes you want. For simplicity's sake and for consistency with the rest of this guide, we recommend that you create two classes: `HelloWorld.java` and `Greeter.java`.

`src/main/java/hello/HelloWorld.java`
```java
package hello;

import org.joda.time.LocalTime;

public class HelloWorld {
  public static void main(String[] args) {
    LocalTime currentTime = new LocalTime();
    System.out.println("The current local time is: " + currentTime);

    Greeter greeter = new Greeter();
    System.out.println(greeter.sayHello());
  }
}
```

`src/main/java/hello/Greeter.java`
```java
package hello;

public class Greeter {
  public String sayHello() {
    return "Hello world!";
  }
}
```


<a name="initial"></a>
### Installing Gradle

Now that you have a project that is ready to be built with Gradle, the next step is to install Gradle.

Gradle is downloadable as a zip file at http://www.gradle.org/downloads. Only the binaries are required, so look for the link to gradle-_{version}_-bin.zip. (If you want, you can also choose gradle-_{version}_-all.zip to get the sources and documentation as well as the binaries.)

Once you have downloaded the zip file, unzip it to your computer. Then add the _bin_ folder to your path.

To test the Gradle installation, run gradle from the command-line:

```sh
$ gradle
```

If all goes well, you should see a welcome message that looks something like the following:

```sh
:help

Welcome to Gradle 1.5.

To run a build, run gradle <task> ...

To see a list of available tasks, run gradle tasks

To see a list of command-line options, run gradle --help

BUILD SUCCESSFUL

Total time: 1.857 secs 
```

Congratulations! You now have Gradle installed.

Finding Out What Gradle Can Do
------------------------------
Now that Gradle is installed, kick the tires on it and see what it can do. Before you even create a _build.gradle_ file for the project, there's already something you can do with Gradle: Ask it what tasks are available. To do that, issue the following on the command line:

```sh
$ gradle tasks
```

You should see a list of tasks that are available in Gradle. Assuming you run Gradle in a folder where there's not already a _build.gradle_ file, you'll see some very elementary tasks such as this:

```sh
:tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

Help tasks
----------
dependencies - Displays all dependencies declared in root project 'Downloads'.
dependencyInsight - Displays the insight into a specific dependency in root project 'Downloads'.
help - Displays a help message
projects - Displays the sub-projects of root project 'Downloads'.
properties - Displays the properties of root project 'Downloads'.
tasks - Displays the tasks runnable from root project 'Downloads' (some of the displayed tasks may belong to subprojects).
```

Even though these tasks are available, they don't offer much value without a project build configuration. But as you flesh out the build.gradle file, some of these tasks will be more useful. And, the list of tasks will grow as you add plugins to the build.gradle file, so  you'll occasionally want to run the "tasks" task again to see what tasks are available.

Speaking of adding plugins, next you'll need to add a plugin that enables basic Java build functionality.

Building Java Code
------------------
Starting simple, create a very basic build.gradle file that has only one line in it:

```groovy
apply plugin: 'java'
```

This single line in the build configuration brings a significant amount of power. If you the "tasks" task again, you'll see some new tasks added to the list, including tasks for building the project, creating JavaDoc, and running tests.

The "build" task is probably one of the tasks you'll use most often with Gradle. This task compiles, tests, and assembles the code into a JAR file. You can run it like this:

```sh
$ gradle build
```

After a few seconds, you should see the words "BUILD SUCCESSFUL", indicating that the build has completed. 

To see the results of the build effort, take a look in the _build_ folder. Therein you'll find several directories, including these three notable folders:

* _classes_ - The project's compiled .class files
* _reports_ - Any reports produced by the build (such as test reports)
* _libs_ - The assembled project libraries (usually JAR and/or WAR files)

In the _classes_ folder, you'll find the .class files resulting from compiling the Java code. Specifically, you should find _HelloWorld.class_ and _Greeter.class_.

At this point, the project doesn't have any library dependencies, so there's nothing in the *dependency_cache* folder.

The _reports_ folder should contain a report of running unit tests on the project. Since the project doesn't yet have any unit tests, that report will be uninteresting.

The _libs_ folder should contain a JAR file that is named after the project's folder. If you cloned the project from GitHub, then the JAR file is likely named _start.jar_. That's probably not what you'd want it named, though.

Declaring Dependencies
----------------------

The simple Hello World sample is completely self-contained and does not depend on any additional libraries. Most applications, however, depend on external libraries to handle common and/or complex functionality.

For example, suppose that in addition to saying "Hello World!", you want the application to print the current date and time. While you could use the date and time facilities in the native Java libraries, you can make things more interesting by using the Joda Time libraries.

First, change HelloWorld.java to look like this:

```java
package hello;

import org.joda.time.LocalTime;

public class HelloWorld {
  public static void main(String[] args) {
    LocalTime currentTime = new LocalTime();
    System.out.println("The current local time is: " + currentTime);

    Greeter greeter = new Greeter();
    System.out.println(greeter.sayHello());
  }
}
```

Here `HelloWorld` uses Joda Time's `LocalTime` class to get and print the current time. 

If you were to run `gradle build` to build the project now, the build would fail because you've not declared Joda Time as a compile dependency in the build. You can fix that by adding the following lines to _build.gradle_:

```groovy
repositories { mavenCentral() }
dependencies {
  compile "joda-time:joda-time:2.2"
}
```

The first line here indicates that the build should resolve its dependencies from the Maven Central repository. Gradle leans heavily on many conventions and facilities established by the Maven build tool, including the option of using Maven Central as a source of library dependencies.

Within the `dependencies` block, you declare a single dependency for Joda Time. Specifically, you're asking for (reading right to left) version 2.2 of the _joda-time_ library, in the _joda-time_ group. 

Another thing to note about this dependency is that it is a `compile` dependency, indicating that it should be available during compile-time (and if you were building a WAR file, included in the _/WEB-INF/libs_ folder of the WAR). Other notable types of dependencies include:

* `providedCompile` - Dependencies that are required for compiling the project code, but that will be provided at runtime by a container running the code (e.g., the Java Servlet API).
* `testCompile` - Dependencies used for compiling and running tests, but not required for building or running the project's runtime code.

Now if you run `gradle build`, Gradle should resolve the Joda Time dependency from the Maven Central repository and the build will be successful.

Here's the completed `build.gradle` file:

`build.gradle`
```gradle
apply plugin: 'java'

repositories { mavenCentral() }
dependencies {
  compile "joda-time:joda-time:2.2"
}
```

Next Steps
==========
Congratulations! You have now created a very simple, yet effective Gradle build file for building Java projects.
