# publish-sample

## Overview

You can configure Gradle to publish packages to the GitHub Packages Gradle registry and to use packages stored on GitHub
Packages as dependencies in a Java project.

## Resources

[Github doc : Working with the Gradle registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-gradle-registry#authenticating-to-github-packages)

## Setup

### To publish

Add the following to your `build.gradle.kts`. Replace `OWNER` and `REPOSITORY` with the owner and repository name of the
repository that contains your project.

```kts
plugins {
    `maven-publish`
}
publishing {
    repositories {
        maven {
            name = "GitHubPackages"
            url = uri("https://maven.pkg.github.com/OWNER/REPOSITORY")
            // url = uri("https://maven.pkg.github.com/tom-sartori/publish-sample")
            credentials {
                username = project.findProperty("gpr.user") as String? ?: System.getenv("USERNAME")
                password = project.findProperty("gpr.key") as String? ?: System.getenv("TOKEN")
            }
        }
    }
    publications {
        register<MavenPublication>("gpr") {
            from(components["java"])
        }
    }
}
```

Add the following to your `gradle.properties` file. Replace `USERNAME` with your GitHub username and `TOKEN` with a
personal access token (classic). The token must have the `write:packages` scope and could also have
the `delete:packages` (and `read:packages`) scope.

```properties
gpr.user=USERNAME
gpr.key=TOKEN
```

Then, run the following command to publish the package:

```bash
./gradlew publish
```

### To use

Add the following to your `build.gradle.kts`. Replace `OWNER` and `REPOSITORY` with the owner and repository name of the
repository that contains your project. Replace `GROUP_ID`, `ARTIFACT_ID` and `VERSION` with the values corresponding to
your package.

```kts
repositories {
    maven {
        url = uri("https://maven.pkg.github.com/OWNER/REPOSITORY")
        // url = uri("https://maven.pkg.github.com/tom-sartori/publish-sample")
        credentials {
            username = project.findProperty("gpr.user") as String? ?: System.getenv("USERNAME")
            password = project.findProperty("gpr.key") as String? ?: System.getenv("TOKEN")
        }
    }
}

dependencies {
    implementation("GROUP_ID:ARTIFACT_ID:VERSION")
    // implementation("org.example:publish-sample:1.0-SNAPSHOT")
}
```

Add the following to your `gradle.properties` file. Replace `USERNAME` with your GitHub username and `TOKEN` with a
personal access token (classic). The token must have the `read:packages` scope. 

```properties
gpr.user=USERNAME
gpr.key=TOKEN
```
