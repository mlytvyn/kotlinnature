Kotlin nature extension for SAP Commerce Cloud (Hybris)
=====================

## Purpose

Thanks to advanced pluggability of the SAP Commerce, it is possible to enable an alternative language which can be run
on
JVM.

The same way SAP Commerce enables Groovy code compilation via `groovynature` extension, this extension adds support
of [Kotlin](https://kotlinlang.org) language compilation.

> Kotlin is a programming language that makes coding concise, cross-platform, and fun

Ultimately, the goal of this extension is to make SAP Commerce developers life easier and fun with Kotlin.

## Overview

Kotlin source files have to be placed in `kotlinsrc` and test source in `kotlintestsrc`.

It is possible to change version of Kotlin compiler via `local.properties` without modifying the source of
the `kotlinnature` extension. Kotlin compiler will be automatically downloaded from the JetBrains GitHub,
if `kotlin-ant.jar` is not present in the `kotlinnature/lib` directory.

**Important note**:
> modification of the OOTB SAP Commerce files is considered as not the best practice, which may affect migration or
> upgrade. Following that statement `kotlinnature` extension is not automatically injected into the
> Platform's `compiling.xml` and it was done on purpose.
>
> If modification of the OOTB files is acceptable, `kotlinnature` extension is shipped with two special Ant targets to
_register_ and _unregister_ itself in the `compiling.xml` file.

### Available targets

| Target                                      | Description                                                                                                                                                                                                |
|---------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `kotlinnature_extension_compile_register`   | build target which will extend OOTB `extension_compile` macro in `compiling.xml` file with compilation of Kotlin source code,<br>it will also set flag `kotlinnature.extension_compile.extended` to `true` |
| `kotlinnature_extension_compile_unregister` | build target which will revert changes applied by `kotlinnature_extension_compile_register`                                                                                                                |

### Available macrodefs

**Note**: below macros will be invoked automatically (no need to make it manually)

| Macro                       | Description                                                                                                                                                                       |
|-----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `kotlinnature_before_build` | macro which will download Kotlin compiler dependencies if `kotlinnature/lib/kotlin-ant.jar` is missing                                                                            |
| `kotlin_compile`            | macro for compiling Kotlin source files                                                                                                                                           |
| `kotlinnature_compile_core` | invokes `kotlin_compile` on core source files                                                                                                                                     |
| `kotlinnature_compile_web`  | invokes `kotlin_compile` on web source files                                                                                                                                      |
| `_after_build`              | invokes Kotlin source files compilation after the build, if executed from the `bin/platform` directory,<br>will not be executed if `kotlinnature.extension_compile.extended=true` |

### Available properties

| Property                                  | Default    | Description                                                                                                                      |
|-------------------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------|
| `kotlinnature.compiler.version`           | **1.8.21** | must point to valid Kotlin compiler release version<br>- releases can be found here https://github.com/JetBrains/kotlin/releases |
| `kotlinc.fork`                            | **false**  | if set to **true** will Fork new process for Kotlin compilation                                                                  |
| `<ext>.kotlinc.fork`                      |            | enables extension specific `kotlin.fork` property                                                                                |
| `kotlinnature.extension_compile.extended` | **false**  | internal flag, which indicates that `kotlinnature` has been registered in `compiling.xml`                                        |

## How-to use

- Checkout this repository and integrate into your project, preferably as a GIT submodule
- Add new `kotlinnature` extension dependency in `localextensions.xml` file
- Adjust project's `.gitignore` file to ignore `kotlinnature` completely
- Ensure that you're using latest version of
  the [SAP Commerce Developers Toolset plugin](https://plugins.jetbrains.com/plugin/12867-sap-commerce-developers-toolset)
  for Intellij IDEA, as it will automatically register both `kotlinsrc` and `kotlintestsrc` as source directories

## Tips & tricks

> The below tricks are not needed if `compiling.xml` was adjusted via invocation of
> `kotlinnature_extension_compile_register` build target

- To be able to compile Kotlin source code of the individual extension by calling `ant build` from its directory, custom
  macrodef has to be registered

```xml
<macrodef name="<extname>_after_build">
    <sequential>
        <build_kotlinnature extname="<extname>"/>
    </sequential>
</macrodef>
```

- Alternatively, if modification of extension's `buildcallback.xml` file is not an option and Kotlin source code
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
