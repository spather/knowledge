---
tags: gradle android groovy
---
# Gradle Notes

* [The Problem with Gradle](https://www.bruceeckel.com/2021/01/02/the-problem-with-gradle/) was where my investigation began.
* This [medium article](https://medium.com/@wasyl/understanding-android-gradle-build-files-e4b45b73cc4c) did the most to demystify things for me
Groovy closures can be bound to different *delegate objects*
Groovy code in build.gradle files runs against `Project` objects.
A gradle build consists of one or more projects, which contain tasks.
In gradle files, ext is shorthand for project.ext and defines extra properties for the project object
It's a convention for the root project to be mostly empty and just contain global config or orchestration.

This page has info about figuring out which repo a dependency is from: https://discuss.gradle.org/t/how-to-know-which-repository-a-dependency-is-from/6689

When trying to run custom tasks, I ran into an error about `Unsupported class file major version 60`. Based on [this issue](https://github.com/gradle/gradle/issues/13629) it seems like Gradle 6 (which I'm on) does not support Java 16, which seems to be the default on my OS. Googling showed that it does support Java 15. Based on prior history, I didn't want to flat out install Java 15 in case it messed up something else on my system. So I tried to learn a bit about Java environments and came up with a way to easily switch configurations. See [Running Multiple Versions of Java](running-multiple-versions-of-java.md).

Android dependencies info: https://developer.android.com/studio/build/dependencies

Some explanation of what the `task` keyword does: https://docs.gradle.org/current/dsl/org.gradle.api.Task.html

Do I need to write a gradle plugin? This question shows iteration over dependencies: https://stackoverflow.com/questions/13202522/how-to-iterate-gradle-dependencies-in-custom-gradle-plugin

Found this gist which shows how to iterate over dependencies: https://gist.github.com/matthiasbalke/887f44c45de3d7a8ebc4d5f9f219c210

This is what doLast() does: https://discuss.gradle.org/t/what-is-dolast-for/27731

Where do all the gradle properties come from? https://medium.com/tooploox/where-do-gradle-properties-come-from-bf77e43ee572


Debugging gradle file:
* Basically from this reference: https://stackoverflow.com/questions/58408741/debugging-gradle-files-in-android-studio (but did not follow the steps about setting up the remote configuration)
* Instead, ran the command like this:

```
./gradlew -q app:extDepInfo -Dorg.gradle.debug=true --no-daemon
```

It'll (confusingly) say "Starting Daemon" and at that point you can attach the debugger from Android Studio (Run/Attach to Process)

use `implementation gradleApi()` to declare dependency on gradle API or maybe localGroovy()

Tried this but it didn't work
```
project.getGradle().addListener(new DependencyResolutionListener() {
    @Override
    void beforeResolve(org.gradle.api.artifacts.ResolvableDependencies dependencies) {

    }

    @Override
    void afterResolve(org.gradle.api.artifacts.ResolvableDependencies dependencies) {
        println "This set is: ${dependencies.name}"
        dependencies.getFiles().each { f ->
            println "f: ${f.absolutePath}"
        }
    }
});
```

How to print the list of repositories:
```
task printRepos {
    doLast {
        println "Repos: "
        project.repositories.each {
            println "${it.name} : ${it.url}"
        }
    }
}
```

Draft task to print detailed dependency information:
```groovy
task extDepInfo {
    doLast {
        def wantConfig = "releaseRuntimeClasspath"
        def conf = project.configurations.getByName(wantConfig)

        def depData = [
            externalModules: [
                resolved: [],
                unresolved: [],
            ],
            projects: []
        ]

        conf.getAllDependencies().each { d ->
            if (d instanceof ExternalModuleDependency) {
                try {
                    def fileColl = conf.fileCollection(d)
                    def dep = [
                        name: d.name,
                        group: d.group,
                        version: d.version,
                        files: fileColl.collect { f -> f.absolutePath }
                    ]
                    depData.externalModules.resolved.add(dep);
                } catch (Exception ex) {
                    depData.externalModules.unresolved.add([
                        name: d.name,
                        group: d.group,
                        version: d.version,
                    ])
                }
            } else if (d instanceof ProjectDependency) {
                depData.projects.add([
                    name: d.name,
                    projectDir: d.dependencyProject.projectDir.absolutePath,
                ])
            }
        }

        println JsonOutput.prettyPrint(JsonOutput.toJson(depData))
    }
}
```

NEXT: try setting the project repositories one at a time, and then trying to resolve each dependency one at a time.

seemed to output dependencies of the build script


