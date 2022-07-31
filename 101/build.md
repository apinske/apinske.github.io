# maven & gradle

* samples
    * simple project
    * https://github.com/apinske/playground-java
* features
    * build flow
    * dependency resolution
    * artefact repo
* goals
    * common project structure
    * common repo format
    * declarative

## maven
* [phases](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference)
    * generate-sources
    * process-sources
    * generate-resources
    * process-resources
    * compile
    * prepare-package
    * package
    * install
    * deploy
    * ...
* plugins have goals
* [goals attached to phases](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#built-in-lifecycle-bindings)
* default plugins (compile, jar, ...)
* plugins usually have a default goal (attached to a specific phase)
* other features
    * [dependency resolution](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#) (scopes: compile, provided, ...)
    * filtering
    * site
    * ...

## gradle
* features
    * performance (daemon, incremental)
    * cleaner dependency resolution (not reliant on order)
    * scripts
* plugins have tasks
* tasks depend on each other (graph) 
* default plugins ([base](https://docs.gradle.org/current/userguide/base_plugin.html#base_plugin), [java](https://docs.gradle.org/current/userguide/java_plugin.html#java_plugin), ...)
