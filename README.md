Kotlin nature extension for SAP Commerce Cloud (Hybris)
=====================

## Purpose

Thanks to advanced pluggability of the SAP Commerce it is possible to enable alternative language which can be run on
JVM.

In a same way SAP Commerce enables Groovy code compilation via `groovynature` extension, this extension adds support of
the [Kotlin](https://kotlinlang.org).

> Kotlin is a programming language that makes coding concise, cross-platform, and fun

At the end of the day, goal of this extension is to make SAP Commerce developers life easier and fun with the Kotlin.

## Quick overview

Kotlin source files have to be placed in the `kotlinsrc` and test source in the `kotlintestsrc`.

It is possible to adjust dependencies via `local.properties` without touching the source of the `kotlinnature`
extension. This gives possibility to change version of the Kotlin compiler without source code modifications.

### macrodefs

- `kotlin_compile` - macro for compiling Kotlin source files
- `kotlinnature_compile_core` - invokes `kotlin_compile` on core source files
- `kotlinnature_compile_web` - invokes `kotlin_compile` on web source files

### properties

- `kotlinnature.compiler.version` - must point to valid Kotlin compiler release version
    - Kotlin releases can be found here https://github.com/JetBrains/kotlin/releases
    - default is `1.8.21`
- `kotlinc.fork` - if set to **true** will Fork new process for Kotlin compilation
    - default is `false`
- `<ext>.kotlinc.fork` - enables extension specific `kotlin.fork` property

## How-to use

- Checkout this repository and integrate into your project, preferably as a GIT submodule
- Add new extension dependency in the `localextensions.xml` for `kotlinnature`
- Adjust project's `.gitignore` file to ignore `kotlinnature` completely
- Ensure that you're using latest version of the [SAP Commerce Developers Toolset plugin](https://plugins.jetbrains.com/plugin/12867-sap-commerce-developers-toolset) for Intellij IDEA, as it will
  automatically register both `kotlinsrc` and `kotlintestsrc` as source directories