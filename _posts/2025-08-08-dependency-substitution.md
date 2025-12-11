---
title: Dependency Substitution
date: '2025-08-08T12:00:00-08:00'
tags: gradle android software-engineering
---
![AMS](/assets/elephants.jpg)

I've had to do a fair amount of in-house SDK work over the last month, so I figured this month would be a good opportunity to cover dependency substitution.  When developing your own SDK its generally a good idea to periodically smoke test it within the context of the consuming client app.  Normally dependencies like SDKs are downloaded from a remote artifact repository like Maven using Gradle.  But when you're developing the SDK it can be a real pain in the butt to generate a release binary, upload it to a remote artifact repository, just to turn around and download it again for your local sample client app.  Fortunately the Gradle framework has a couple of different options to sidestep this whole process, significantly increasing your iteration speed.

The first approach is [publishToMavenLocal](https://docs.gradle.org/current/javadoc/org/gradle/api/publish/maven/tasks/PublishToMavenLocal.html). This has been around since Gradle 1.4 and is ideal for testing a library in isolation or as a dependency for a local project prior to release.  [DependencySubstitution](https://docs.gradle.org/current/javadoc/org/gradle/api/artifacts/DependencySubstitution.html) on the other hand was introduced in Gradle 2.5 and is best used for developing and testing inter-dependent modules within a single, larger, comosite build project.  It has the advantage of allowing for seamless switching between the source code of the app and the SDK.

This blog post is specifically about dependency substitution, which I've tended to favor because of the flexibility granted by compiling both source sets simeltaneously.  This allows me to treate both projects as a single app within Android Studio, making refactoring a comparative breeze.

You can enable dependency substitution locally by following the steps below:

1. Git clone the dependency repository as a sibling of your app's repository.  For example, if you wanted to build Coil (an Android image loading library) locally, you'd clone the project from https://github.com/coil-kt/coil.
2. In your `settings.gradle.kts` make sure to use `includeBuild` with a path to your local libary.
3. Within the includeBuild lambda create a `dependencySubstitution` lambda with a list of the specific dependencies you want substituted.  A full example is below:
```
includeBuild("../coil") {
    dependencySubstitution {
        substitute(module("io.coil-kt.coil3:coil-compose")).using(project(":coil-compose"))
    }
}
```
4. If you would like to make the substitution optional, add a flag to your `settings.gradle`, i.e. `if (file("../coil/.composite-include").exists()) {` followed by your `includeBuild` lambda.
5. If you followed step 4 above you would create an empty file named `.composite-include` in the root directory of the project to be included.
6. Build, Run and Debug your app as usual
7. Coil will now show up in Android Studio's Project Explorer and it will be possible to set breakpoints in those libraries.
8. If you encounter compile errors due to missing or extra parameters, make sure that your local version of the library matches as closely as possible to the version your app is using was using. This can be done by checking the version `build.gradle.kts` file in the App's `/app` directory.
9. If you get a no matching variant of project error, its for one of two reasons:
   - A mismatch in the gradle plugin version between your app and the dependency. One or more will need to be updated to match the others.
   - A mismatch in the gradle distributionUrl (in gradle-wrapper.properties) between your app and the dependency. One or more will need to be updated to match the others.
10. If you get a duplicate class error during compilation, one of two things is likely happening:
   - Your local copy of the dependency has a different transient dependency version than your app is using.  You need to change one to match the other.
   - Your local copy of the dependency undergoing some processing that the remote version did not, or vice versa.  Minification with R8 is a good example. 
11. If you followed step 4, to stop pulling Coil into your app build, delete the `.composite-include` file from Coil's repo
12. Make sure to do a gradle sync after creating/deleting `.composite-include` in the Coil repo.

I hope you found this helpful!  Gradle dependency substitution can be a little tricky to setup at first, but once you have it configured I think you'll find it indepensible for local libary development.

Photo by <a href="https://unsplash.com/@photosbybeks?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Photos By Beks</a> on <a href="https://unsplash.com/photos/grey-elephant-on-green-grass-field-during-daytime-QzCfMi1Mbjs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
      