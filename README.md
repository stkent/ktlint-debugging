Repository to help me investigate why `.editorconfig` is ignored in newer versions of ktlint when run using the [ktlint-gradle](https://github.com/JLLeitschuh/ktlint-gradle) plugin.

# Instructions

Run 

```
./gradlew --stop && ./gradlew ktlintCheck --rerun-tasks 
```

and see that the build is successful.

Update the ktlint version specified in the root `build.gradle` file:

```diff
diff --git a/build.gradle b/build.gradle
index 4f5a591..585867d 100644
--- a/build.gradle
+++ b/build.gradle
@@ -8,7 +8,7 @@ subprojects {
     apply plugin: "org.jlleitschuh.gradle.ktlint"
 
     ktlint {
-        version = "0.45.2"
+        version = "0.46.1"
         verbose = true
     }
 }
```

Run

```
./gradlew --stop && ./gradlew ktlintCheck --rerun-tasks 
```

and see that the build now fails.

```
> Task :app:ktlintMainSourceSetCheck FAILED
{redacted}/app/src/main/java/com/stkent/ktlintdebugging/MainActivity.kt:3:1 Unused import (no-unused-imports)
```

As far as I can tell, the reported failure should still be suppressed by the `.editorconfig` file:

```editorconfig
[*.{kt,kts}]
disabled_rules = no-unused-imports
```

in the newer version of ktlint ([0.46 changes](https://github.com/pinterest/ktlint/blob/master/CHANGELOG.md#0460---2022-06-18), [0.46.1 changes](https://github.com/pinterest/ktlint/blob/master/CHANGELOG.md#0461---2022-06-21)).

The suppression seems to work for both ktlint versions when run from the command line:

```shell
$ curl -sSLO https://github.com/pinterest/ktlint/releases/download/0.45.2/ktlint && chmod a+x ktlint && sudo mv ktlint /usr/local/bin/
$ ktlint
```

(exits with code 0).

```shell
$ curl -sSLO https://github.com/pinterest/ktlint/releases/download/0.46.1/ktlint && chmod a+x ktlint && sudo mv ktlint /usr/local/bin/
$ ktlint 
```

(also exits with code 0).

I believe the problem therefore lies with the [ktlint-gradle](https://github.com/JLLeitschuh/ktlint-gradle) plugin.
