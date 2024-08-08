# Usage

## CLI

Vineflower can be run as a CLI tool.

```shell
java -jar vineflower.jar [args...] {in}+ [{out}]
```

### Arguments

% Several arguments are handled specially in the codebase so they do not have annotations which can be examined on their own

```{option} --cfg={file}, -cfg={file}
  A configuration file containing additional arguments to pass to the decompiler, one per line.
```

```{option} --add-external={library}, -e={library}
  Can be specified as many times as necessary to add library inputs to the decompile classpath
```

The following three options control how decompiled output should be written. If none are specified, the output type will be inferred based on the output. `.zip` or `.jar`-ending outputs will be treated as archives, all other values will be treated as a directory, or no output at all will write the decompiled output to the console.

```{option} --file
  Prefer saving to an archive file.
```

```{option} --folder
  Prefer saving to a folder, potentially extracting an input archive.
```

```{option} --legacy
  Use legacy Fernflower save behavior.
```

It is possible to limit decompiled output to only a certain subset of the input files, for cases where an entire archive is provided as input:

```{option} --only={prefix}, -only={prefix}
Only members whose paths start with `prefix` will be emitted
```

% The rest can be automatically inferred from IFernflowerPreferences

```{include} generated/usage.md
```

## Library

Vineflower can be also used as a library.

### Usage

Vineflower can be instantiated like so:
```java
import org.jetbrains.java.decompiler.main.Fernflower;

Fernflower ff = new Fernflower(/* saver */, /* properties */, /* logger */);
```
The constructor requires 3 parameters:
1) `IResultSaver` instance
- Has multiple implementations, notably `SingleFileSaver` and `DirectoryResultSaver`. First one can be
used for saving to a single file, the latter saves decompilation result into target directory.
2) `Map<String, Object>` customProperties
- Preferences(also arguments that you would pass into CLI), `IFernflowerPreferences.DEFAULTS`
can be used to request default options that can be further customized and passed into this parameter.
- All preferences can be found at: [Github](https://github.com/Vineflower/vineflower/blob/master/src/org/jetbrains/java/decompiler/main/extern/IFernflowerPreferences.java)
or inside `org.jetbrains.java.decompiler.main.extern.IFernflowerPreferences` class.
3) `IFernFlowerLogger` instance
- pass `IFernflowerLogger.NO_OP` if you don't want any logging, otherwise
create your own implementation and pass it to this parameter for custom logging.

Once we instantiate our decompiler, there is a few methods available to use:

```java
ff.addSource(File source);
```
This will add file as source which will be decompiled.

```java
ff.addLibrary(File library);
```
This will add library as context source, it will not be decompiled and may improve
decompilation results.

```java
ff.decompileContext();
```
Decompiles the sources and uses the `IResultSaver` we specified as constructor parameter to save the decompilation result.
