# Adding Gradle Dependencies

## Learning Goals

- Define dependency scope in terms using a **Configuration**.
- Add dependencies to a Gradle project.

## Introduction

Every Gradle dependency applies to a specific scope. Some should be used for 
compiling source code, while others only need to be available at runtime.
Scope is represented by a **Configuration**, which indicates the
dependency classpath.  There are two classpaths for a Java project:

- **Compile classpath** – dependencies required for the JDK to be able to compile Java code into .class files
- **Runtime classpath** – dependencies  required to  run the compiled Java code

When we configure a Gradle dependency, we specify the classpath availability.
We have three options:

- **compileOnly** – put the dependency on the compile classpath only.
- **runtimeOnly** – put the dependency on the runtime classpath only.
- **implementation** – put the dependency on both classpaths.

For tests we also have three options:

- **testCompileOnly** – put the test dependency on the compile classpath only.
- **testRuntimeOnly** – put the test dependency on the runtime classpath only.
- **testImplementation** – put the test dependency on both classpaths.

## Junit Dependency

We will step through an example involving the Junit Dependency.

IntelliJ automatically added the Junit dependency in `gradle.build`
when we created the new Gradle project:

```groovy
plugins {
    id 'java'
}

group 'org.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.8.1'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.8.1'
}

test {
    useJUnitPlatform()
}
```

Let's a new method `greet()` to the `Main` class:

```java
package org.example;

public class Main {

    public static String greet(String name) {
        return "Hello, " + name + "!";
    }

    public static void main(String[] args) {
        System.out.println("Hello!");
    }
}
```

We need to generate a new Junit test class `MainTest` and add the
test method:

```java
package org.example;

import static org.junit.jupiter.api.Assertions.*;

class MainTest {

    @Test
    void greet() {
        String expected = "Hello, John!";
        String actual = Main.greet("John");
        assertEquals(expected, actual);
    }
}
```

1. Run the `MainTest` class and confirm the unit test passes.
2. Notice the tasks *compileTestJava*, *testClasses*, and *test* are executed, along with the java compilation tasks.

![run test](https://curriculum-content.s3.amazonaws.com/6002/gradle-dependencies/testtasks.png)


## Apache Commons Lang Dependency

Now we will add a dependency for the **Apache Commons Lang** library.
This must be done manually since IntelliJ does not add the dependency by default
when creating a new project.

Update the `greet()` method to call `StringUtils.abbreviate` as shown:

```java
package org.example;

import org.apache.commons.lang3.StringUtils;

public class Main {

   public static String greet(String name) {
      String shortName = StringUtils.abbreviate(name, 10);
      return "Hello, " +  shortName  + "!";
   }

   public static void main(String[] args) {
      System.out.println("Hello!");
   }
}
```

The code won't compile because the `StringUtils` class can't be not found.

We will add a dependency to `build.gradle` to tell Gradle to get the library:

1. Open `build.gradle`
2. Right-click to open the context menu (or alt-insert on Windows or command-N on Mac).
3. Select Generate.
4. Select Add Dependency.
5. Type "Apache Commons Lang" in the search box, select the "Apache Commons Lang" library, then click "Add".
   
The `build.gradle` file is updated to include a dependency
for the Apache Commons Lang library.  The **implementation**
configuration tells Gradle to add it to both the compile and runtime
classpaths:

```groovy
dependencies {
   implementation 'org.apache.commons:commons-lang3:3.12.0'
   testImplementation 'org.junit.jupiter:junit-jupiter-api:5.8.1'
   testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.8.1'
}
```
  
**VERY IMPORTANT**: After adding the dependency, press the "Load Gradle Changes" icon that appears in `build.gradle`:

![reload gradle](https://curriculum-content.s3.amazonaws.com/6002/gradle-dependencies/add-dependency.png)

Confirm the Gradle Tool Window displays the new dependency:

![apache dependency](https://curriculum-content.s3.amazonaws.com/6002/gradle-dependencies/gradletooldependency.png)

### Testing the new dependency

`MainTest` should be updated with a new method to check that
`Main.greet()` abbreviates names over 7 characters in length:

```java
    @org.junit.jupiter.api.Test
    void greetAbbreviate() {
        String expected = "Hello, John Wi...!";
        String actual = Main.greet("John Williams Smith, Jr.");
        assertEquals(expected, actual);
    }
```

We can now compile and run the `MainTest` junit test.


## Conclusion

We’ve learned how to add dependencies to our Gradle project.

## Resources

[Gradle Dependencies](https://docs.gradle.org/current/userguide/declaring_dependencies.html)      
[Apache Commons Lang](https://commons.apache.org/proper/commons-lang/)   
