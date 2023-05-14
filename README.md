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

This extension does not register Kotlin nature compilation via modification of the macrodef `extension_compile` defined
in the `compiling.xml` file. It Was done on purpose, just to ensure consistency of the OOTB files.

### macrodefs

- `kotlin_compile` - macro for compiling Kotlin source files
- `kotlinnature_compile_core` - invokes `kotlin_compile` on core source files
- `kotlinnature_compile_web` - invokes `kotlin_compile` on web source files
- `_after_build` - invokes Kotlin source files compilation after the build, if executed from the `bin/platform`
  directory, will not be executed if `kotlinnature.extension_compile.extended=true`
- `kotlinnature_extension_compile_register` - special build target which will extend OOTB `compiling.xml`:`extension_compile`
  macro with
  kotlinnature invocations, also will set flag `kotlinnature.extension_compile.extended` to `true`
- `kotlinnature_extension_compile_unregister` - build target which will revert changes applied
  by `kotlinnature_extension_compile_register`

### properties

- `kotlinnature.compiler.version` - must point to valid Kotlin compiler release version
    - Kotlin releases can be found here https://github.com/JetBrains/kotlin/releases
    - default is `1.8.21`
- `kotlinc.fork` - if set to **true** will Fork new process for Kotlin compilation
    - default is `false`
- `<ext>.kotlinc.fork` - enables extension specific `kotlin.fork` property
- `kotlinnature.extension_compile.extended` - internal flag, which indicated that `kotlinnature` has been registered in
  the `compiling.xml`
    - default is `false`

## How-to use

- Checkout this repository and integrate into your project, preferably as a GIT submodule
- Add new extension dependency in the `localextensions.xml` for `kotlinnature`
- Adjust project's `.gitignore` file to ignore `kotlinnature` completely
- Ensure that you're using latest version of
  the [SAP Commerce Developers Toolset plugin](https://plugins.jetbrains.com/plugin/12867-sap-commerce-developers-toolset)
  for Intellij IDEA, as it will
  automatically register both `kotlinsrc` and `kotlintestsrc` as source directories

## Tips & tricks

> Tricks below are not needed if `compiling.xml` was adjusted due invocation of the `kotlinnature_extension_compile_register` build target

- To be able to compile Kotlin source code of the individual extension by calling `ant build` from its directory custom
  macrodef has to be registered

```xml
<macrodef name="<extname>_after_build">
    <sequential>
        <build_kotlinnature extname="<extname>"/>
    </sequential>
</macrodef>
```

- On other hand, if modification of extension's `buildcallback.xml` file is not an option and Kotlin source code
  compilation per individual extension is still a case, one may adjust OOTB's `compiling.xml` file manually or via
  provided macro `kotlinnature_extension_compile`:

```xml
<macrodef name="extension_compile">
    <attribute name="extname"/>
    <sequential>
        <extension_compile_core extname="@{extname}"/>
        <kotlinnature_compile_core extname="@{extname}"/>
        <extension_generate_deployment extname="@{extname}"/>
        <extension_compile_web extname="@{extname}"/>
        <kotlinnature_compile_web extname="@{extname}"/>
        <extension_compile_hmc extname="@{extname}"/>
        <extension_compile_backoffice extname="@{extname}"/>
        <extension_compile_hac extname="@{extname}"/>
    </sequential>
</macrodef>
```
