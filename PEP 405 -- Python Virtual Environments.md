# PEP 405 -- Python Virtual Environments

PEP 405 -- Python虚拟环境

> 原文地址：<https://www.python.org/dev/peps/pep-0405/>

| PEP:            | 405                                                          |
| :-------------- | ------------------------------------------------------------ |
| Title:          | Python Virtual Environments                                  |
| Author:         | Carl Meyer <carl at oddbird.net>                             |
| BDFL-Delegate:  | Nick Coghlan                                                 |
| Status:         | Final                                                        |
| Type:           | Standards Track                                              |
| Created:        | 13-Jun-2011                                                  |
| Python-Version: | 3.3                                                          |
| Post-History:   | 24-Oct-2011, 28-Oct-2011, 06-Mar-2012, 24-May-2012           |
| Resolution:     | https://mail.python.org/pipermail/python-dev/2012-May/119668.html |

------

Contents

- [Abstract](https://www.python.org/dev/peps/pep-0405/#abstract)
- [Motivation](https://www.python.org/dev/peps/pep-0405/#motivation)
- Specification
  - [Isolation from system site-packages](https://www.python.org/dev/peps/pep-0405/#isolation-from-system-site-packages)
  - [Creating virtual environments](https://www.python.org/dev/peps/pep-0405/#creating-virtual-environments)
  - [Sysconfig install schemes and user-site](https://www.python.org/dev/peps/pep-0405/#sysconfig-install-schemes-and-user-site)
  - [Copies versus symlinks](https://www.python.org/dev/peps/pep-0405/#copies-versus-symlinks)
  - [Include files](https://www.python.org/dev/peps/pep-0405/#include-files)
  - [API](https://www.python.org/dev/peps/pep-0405/#api)
- Backwards Compatibility
  - [Splitting the meanings of `sys.prefix`](https://www.python.org/dev/peps/pep-0405/#splitting-the-meanings-of-sys-prefix)
  - [Impact on other Python implementations](https://www.python.org/dev/peps/pep-0405/#impact-on-other-python-implementations)
- [Reference Implementation](https://www.python.org/dev/peps/pep-0405/#reference-implementation)
- [References](https://www.python.org/dev/peps/pep-0405/#id2)
- [Copyright](https://www.python.org/dev/peps/pep-0405/#copyright)

# [Abstract](https://www.python.org/dev/peps/pep-0405/#id15)

概览

This PEP proposes to add to Python a mechanism for lightweight "virtual environments" with their own site directories, optionally isolated from system site directories. Each virtual environment has its own Python binary (allowing creation of environments with various Python versions) and can have its own independent set of installed Python packages in its site directories, but shares the standard library with the base installed Python.

这个 PEP 提议在 Python 中增加一个轻量级的 "虚拟环境 "机制，有自己的站点目录，可以选择与系统站点目录隔离。每个虚拟环境都有自己的 Python 二进制文件 (允许创建具有不同 Python 版本的环境)，并可以在其站点目录中拥有自己独立的已安装的 Python 包，但与基本安装的 Python 共享标准库。

# [Motivation](https://www.python.org/dev/peps/pep-0405/#id16)

起因

The utility of Python virtual environments has already been well established by the popularity of existing third-party virtual-environment tools, primarily Ian Bicking's [virtualenv](http://www.virtualenv.org/) [[1\]](https://www.python.org/dev/peps/pep-0405/#id3). Virtual environments are already widely used for dependency management and isolation, ease of installing and using Python packages without system-administrator access, and automated testing of Python software across multiple Python versions, among other uses.

Python 虚拟环境的效用已经被现有的第三方虚拟环境工具的普及所证实，主要是 Ian Bicking 的 virtualenv [1]。虚拟环境已经被广泛用于依赖性管理和隔离，便于安装和使用Python包而不需要系统管理员的访问，以及跨多个Python版本的Python软件的自动测试，以及其他用途。

Existing virtual environment tools suffer from lack of support from the behavior of Python itself. Tools such as [rvirtualenv](https://github.com/kvbik/rvirtualenv) [[2\]](https://www.python.org/dev/peps/pep-0405/#id5), which do not copy the Python binary into the virtual environment, cannot provide reliable isolation from system site directories. Virtualenv, which does copy the Python binary, is forced to duplicate much of Python's `site` module and manually symlink/copy an ever-changing set of standard-library modules into the virtual environment in order to perform a delicate boot-strapping dance at every startup. (Virtualenv must copy the binary in order to provide isolation, as Python dereferences a symlinked executable before searching for `sys.prefix`.)

现有的虚拟环境工具由于缺乏Python本身的行为支持而受到影响。像 [rvirtualenv](https://github.com/kvbik/rvirtualenv) [[2\]](https://www.python.org/dev/peps/pep-0405/#id5) 这样的工具，不把 Python 二进制文件复制到虚拟环境中，不能提供与系统站点目录的可靠隔离。Virtualenv 复制了 Python 的二进制文件，它被迫复制了大部分 Python 的 `site` 模块，并手动将一组不断变化的标准库模块用符号连接/复制到虚拟环境中，以便在每次启动时进行微妙的启动限制。(Virtualenv 必须复制二进制文件以提供隔离，因为 Python 在搜索`sys.prefix`之前会解除对符号链接的可执行文件的索引。)

The `PYTHONHOME` environment variable, Python's only existing built-in solution for virtual environments, requires copying/symlinking the entire standard library into every environment. Copying the whole standard library is not a lightweight solution, and cross-platform support for symlinks remains inconsistent (even on Windows platforms that do support them, creating them often requires administrator privileges).

环境变量`PYTHONHOME`是Python现有的唯一内置的虚拟环境解决方案，需要将整个标准库复制/符号链接到每个环境中。复制整个标准库并不是一个轻量级的解决方案，而且对符号链接的跨平台支持仍然不一致(即使在支持它们的Windows平台上，创建它们往往需要管理员的权限)。

A virtual environment mechanism integrated with Python and drawing on years of experience with existing third-party tools can lower maintenance, raise reliability, and be more easily available to all Python users.

一个与Python集成的虚拟环境机制，并借鉴现有第三方工具的多年经验，可以降低维护成本，提高可靠性，并更容易为所有Python用户所用。

# [Specification](https://www.python.org/dev/peps/pep-0405/#id17)

具体要求

When the Python binary is executed, it attempts to determine its prefix (which it stores in `sys.prefix`), which is then used to find the standard library and other key files, and by the `site` module to determine the location of the site-package directories. Currently the prefix is found (assuming `PYTHONHOME` is not set) by first walking up the filesystem tree looking for a marker file (`os.py`) that signifies the presence of the standard library, and if none is found, falling back to the build-time prefix hardcoded in the binary.

当 Python 二进制文件被执行时，它试图确定它的前缀 (存储在 `sys.prefix` 中)，然后用它来寻找标准库和其他关键文件，并由 `site` 模块来确定 site-package 目录的位置。目前，前缀是通过在文件系统树上寻找标志着标准库存在的标记文件（`os.py`）来找到的（假设没有设置`PYTHONHOME`），如果没有找到，就回到二进制文件中硬编码的构建时前缀。

This PEP proposes to add a new first step to this search. If a `pyvenv.cfg` file is found either adjacent to the Python executable or one directory above it (if the executable is a symlink, it is not dereferenced), this file is scanned for lines of the form `key = value`. If a `home` key is found, this signifies that the Python binary belongs to a virtual environment, and the value of the `home` key is the directory containing the Python executable used to create this virtual environment.

本 PEP 提议为这个搜索增加一个新的第一个步骤。如果发现一个 `pyvenv.cfg` 文件，要么与 Python 可执行文件相邻，要么在它上面的一个目录中 (如果可执行文件是一个符号链接，它不会被取消引用)，这个文件将被扫描，以寻找 `key = value` 形式的行。如果发现了`home`键名，这就意味着Python二进制文件属于一个虚拟环境，`home`键的值是包含用于创建这个虚拟环境的Python可执行文件的目录。

In this case, prefix-finding continues as normal using the value of the `home` key as the effective Python binary location, which finds the prefix of the base installation. `sys.base_prefix` is set to this value, while `sys.prefix` is set to the directory containing `pyvenv.cfg`.

在这种情况下，前缀搜索继续正常进行，使用`home`键的值作为有效的Python二进制位置，找到基本安装的前缀。`sys.base_prefix`被设置为这个值，而`sys.prefix`被设置为包含`pyvenv.cfg`的目录。

(If `pyvenv.cfg` is not found or does not contain the `home` key, prefix-finding continues normally, and `sys.prefix` will be equal to `sys.base_prefix`.)

(如果没有找到 `pyvenv.cfg` 或者不包含 `home` 键，前缀查找将正常进行，并且 `sys.prefix` 将等于 `sys.base_prefix`)。

Also, `sys.base_exec_prefix` is added, and handled similarly with regard to `sys.exec_prefix`. (`sys.exec_prefix` is the equivalent of `sys.prefix`, but for platform-specific files; by default it has the same value as `sys.prefix`.)

同时，`sys.base_exec_prefix`也被添加进来，并且与`sys.exec_prefix`的处理方式类似。(`sys.exec_prefix`相当于`sys.prefix`，但用于平台特定的文件；默认情况下，它的值与`sys.prefix`相同。)

The `site` and `sysconfig` standard-library modules are modified such that the standard library and header files are found relative to `sys.base_prefix` / `sys.base_exec_prefix`, while site-package directories ("purelib" and "platlib", in `sysconfig` terms) are still found relative to `sys.prefix` / `sys.exec_prefix`.

`site`和`sysconfig`的标准库模块被修改，使标准库和头文件相对于`sys.base_prefix`/`sys.base_exec_prefix`被找到，而site-package目录（"purelib "和 "platlib"，在`sysconfig`项中）仍然相对于`sys.prefix`/`sys.exec_prefix`找到。

Thus, a Python virtual environment in its simplest form would consist of nothing more than a copy or symlink of the Python binary accompanied by a `pyvenv.cfg` file and a site-packages directory.

因此，Python 虚拟环境最简单的形式不过是一个 Python 二进制文件的拷贝或符号链接，以及一个 `pyvenv.cfg` 文件和一个 site-packages 目录。

## [Isolation from system site-packages](https://www.python.org/dev/peps/pep-0405/#id18)

从系统 site-package 隔离

By default, a virtual environment is entirely isolated from the system-level site-packages directories.

默认情况下，虚拟环境是与系统级 site-package 目录是完全隔离的。

If the `pyvenv.cfg` file also contains a key `include-system-site-packages` with a value of `true` (not case sensitive), the `site` module will also add the system site directories to `sys.path` after the virtual environment site directories. Thus system-installed packages will still be importable, but a package of the same name installed in the virtual environment will take precedence.

如果 `pyvenv.cfg` 文件还包含一个值为 `true` 的键 `include-system-site-packages`（不区分大小写），site 模块也会在虚拟环境站点目录之后将系统站点目录添加到 `sys.path`。因此，系统安装的软件包仍然可以被导入，但在虚拟环境中安装的同名软件包将被优先使用。

[PEP 370](https://www.python.org/dev/peps/pep-0370) user-level site-packages are considered part of the system site-packages for venv purposes: they are not available from an isolated venv, but are available from an `include-system-site-packages = true` venv.

[PEP 370](https://www.python.org/dev/peps/pep-0370)用户级 site-packages 被视为 venv 的系统 site-packages 的一部分：它们不能从孤立的 venv 中获得，但可以从`include-system-site-packages = true` venv中获得。

## [Creating virtual environments](https://www.python.org/dev/peps/pep-0405/#id19)

创建虚拟环境

This PEP also proposes adding a new `venv` module to the standard library which implements the creation of virtual environments. This module can be executed using the `-m` flag:

这个PEP还建议在标准库中增加一个新的`venv`模块，实现虚拟环境的创建。这个模块可以使用`-m`标志来执行。

```shell
python3 -m venv /path/to/new/virtual/environment
```

A `pyvenv` installed script is also provided to make this more convenient:

```shell
pyvenv /path/to/new/virtual/environment
```

Running this command creates the target directory (creating any parent directories that don't exist already) and places a `pyvenv.cfg` file in it with a `home` key pointing to the Python installation the command was run from. It also creates a `bin/` (or `Scripts` on Windows) subdirectory containing a copy (or symlink) of the `python3` executable, and the `pysetup3` script from the `packaging` standard library module (to facilitate easy installation of packages from PyPI into the new venv). And it creates an (initially empty) `lib/pythonX.Y/site-packages` (or `Lib\site-packages` on Windows) subdirectory.

运行这个命令可以创建目标目录 (会创建所有不存在的父目录)，并在其中放置一个 `pyvenv.cfg` 文件，其键`home`指向命令运行时的 Python 安装目录。它还创建了一个 `bin/` (或 Windows 上的 `Scripts`) 子目录，包含 `python3` 可执行文件的副本 (或符号链接)，以及来自打包标准库模块的 `pysetup3` 脚本 (以方便从 PyPI 安装软件包到新的 venv 中)。它还创建了一个（最初是空的）`lib/pythonX.Y/site-packages`（或Windows下的`Lib\site-packages`）子目录。

If the target directory already exists an error will be raised, unless the `--clear` option was provided, in which case the target directory will be deleted and virtual environment creation will proceed as usual.

如果目标目录已经存在，将产生一个错误，除非提供了`--clear`选项，在这种情况下，目标目录将被删除，虚拟环境创建将照常进行。

The created `pyvenv.cfg` file also includes the `include-system-site-packages` key, set to `true` if `pyvenv` is run with the `--system-site-packages` option, `false` by default.

创建的 `pyvenv.cfg` 文件还包括 `include-system-site-packages` 键，如果 `pyvenv` 在运行时使用 `--system-site-packages` 选项，则设置为 `true`，默认为 `false`。

Multiple paths can be given to `pyvenv`, in which case an identical venv will be created, according to the given options, at each provided path.

可以给`pyvenv`提供多个路径，在这种情况下，将根据给定的选项在每个提供的路径上创建一个相同的 venv。

The `venv` module also places "shell activation scripts" for POSIX and Windows systems in the `bin` or `Scripts` directory of the venv. These scripts simply add the virtual environment's `bin` (or `Scripts`) directory to the front of the user's shell PATH. This is not strictly necessary for use of a virtual environment (as an explicit path to the venv's python binary or scripts can just as well be used), but it is convenient.

`venv`模块还为POSIX和Windows系统在venv的`bin`或`Scripts`目录下放置 "shell激活脚本"。这些脚本只是将虚拟环境的`bin`（或`Scripts`）目录添加到用户的shell PATH前面。对于使用虚拟环境来说，这并不是严格意义上的必要条件（因为 venv 的 python 二进制文件或脚本的明确路径也可以被使用），但它很方便。

In order to allow `pysetup` and other Python package managers to install packages into the virtual environment the same way they would install into a normal Python installation, and avoid special-casing virtual environments in `sysconfig` beyond using `sys.base_prefix` in place of `sys.prefix` where appropriate, the internal virtual environment layout mimics the layout of the Python installation itself on each platform. So a typical virtual environment layout on a POSIX system would be:

为了让 `pysetup` 和其它 Python 软件包管理器能够像安装到正常的 Python 软件一样将软件包安装到虚拟环境中，并避免在 `sysconfig` 中使用 `sys.base_prefix` 代替 `sys.prefix` 以外的虚拟环境，内部虚拟环境布局模仿每个平台上的 Python 安装本身的布局。所以在 POSIX 系统上，一个典型的虚拟环境布局是：

```
pyvenv.cfg
bin/python3
bin/python
bin/pysetup3
include/
lib/python3.3/site-packages/
```

While on a Windows system:

在Windows系统上时：

```
pyvenv.cfg
Scripts/python.exe
Scripts/python3.dll
Scripts/pysetup3.exe
Scripts/pysetup3-script.py
        ... other DLLs and pyds...
Include/
Lib/site-packages/
```

Third-party packages installed into the virtual environment will have their Python modules placed in the `site-packages` directory, and their executables placed in `bin/` or `Scripts`.

安装到虚拟环境中的第三方软件包将把它们的 Python 模块放在 site-packages 目录中，而它们的可执行文件则放在 `bin/` 或 `Scripts` 中。

Note

注意

On a normal Windows system-level installation, the Python binary itself wouldn't go inside the "Scripts/" subdirectory, as it does in the default venv layout. This is useful in a virtual environment so that a user only has to add a single directory to their shell PATH in order to effectively "activate" the virtual environment.

在正常的 Windows 系统级安装中，Python 二进制文件本身不会在 "Scripts/" 子目录中，就像在默认的 venv 布局中一样。这在虚拟环境中很有用，这样用户只需要在他们的 shell PATH 中添加一个目录，就可以有效地 "激活 "虚拟环境。

Note

注意

On Windows, it is necessary to also copy or symlink DLLs and pyd files from compiled stdlib modules into the env, because if the venv is created from a non-system-wide Python installation, Windows won't be able to find the Python installation's copies of those files when Python is run from the venv.

在 Windows 上，有必要将 DLLs 和 pyd 文件从已编译的 stdlib 模块中复制或用符号连接到 env 中，因为如果 venv 是从非系统范围内的 Python 安装中创建的，当从 venv 中运行 Python 时，Windows 将无法找到这些文件的 Python 安装副本。

## [Sysconfig install schemes and user-site](https://www.python.org/dev/peps/pep-0405/#id20)

sysconfig安装方案和用户站点

This approach explicitly chooses not to introduce a new sysconfig install scheme for venvs. Rather, by modifying `sys.prefix` we ensure that existing install schemes which base locations on `sys.prefix` will simply work in a venv. Installation to other install schemes (for instance, the user-site schemes) whose paths are not relative to `sys.prefix`, will not be affected by a venv at all.

这种方法明确地选择不为venvs引入一个新的sysconfig安装方案。相反，通过修改 `sys.prefix`，我们确保现有的以 `sys.prefix` 为基础的安装方案在 venv 中可以正常工作。安装到其他安装方案（例如，用户站点方案）的路径不是相对于 `sys.prefix` 的，将完全不受 venv 的影响。

It may be feasible to create an alternative implementation of Python virtual environments based on a virtual-specific sysconfig scheme, but it would be less robust, as it would require more code to be aware of whether it is operating within a virtual environment or not.

基于虚拟特定的 sysconfig 方案创建 Python 虚拟环境的替代实现可能是可行的，但它将不那么强大，因为它需要更多的代码来知悉它是否在虚拟环境中运行。

## [Copies versus symlinks](https://www.python.org/dev/peps/pep-0405/#id21)

副本和符号链接

The technique in this PEP works equally well in general with a copied or symlinked Python binary (and other needed DLLs on Windows). Symlinking is preferable where possible, because in the case of an upgrade to the underlying Python installation, a Python executable copied in a venv might become out-of-sync with the installed standard library and require manual upgrade.

本 PEP 中的技术在一般情况下对复制的或符号链接的 Python 二进制文件 (以及 Windows 上需要的其它 DLL) 也同样有效。在可能的情况下，符号连接是最好的，因为在升级Python底层安装的情况下，复制在venv中的Python可执行文件可能会与安装的标准库不同步，需要手动升级。

There are some cross-platform difficulties with symlinks:

在符号链接存在一些跨平台的难题：

- Not all Windows versions support symlinks, and even on those that do, creating them often requires administrator privileges.

  并非所有的Windows版本都支持符号链接，即使在那些支持符号链接的版本上，创建它们往往需要管理员权限。

- On OS X framework builds of Python, sys.executable is just a stub that executes the real Python binary. Symlinking this stub does not work; it must be copied. (Fortunately the stub is also small, and not changed by bugfix upgrades to Python, so copying it is not an issue).

  在 Python 的 OS X 框架构建中，sys.executable 只是一个执行真正 Python 二进制文件的存根。通过符号链接连接这个存根是行不通的；它必须被复制。(幸运的是，这个存根也很小，而且不会因为 Python 的错误修正升级而改变，所以复制它不是一个问题)。

Thus, this PEP proposes to symlink the binary on all platforms except for Windows, and OS X framework builds. A `--symlink` option is available to force the use of symlinks on Windows versions that support them, if the appropriate permissions are available. (This option has no effect on OS X framework builds, since symlinking can never work there, and has no advantages).

因此，本PEP建议在所有平台上对二进制文件进行符号链接，除了Windows和OS X框架的构建。有一个`--symlink`选项可以在Windows版本上强制使用符号链接，如果有合适的权限的话。这个选项对OS X框架的构建没有影响，因为符号链接在那里永远无法工作，而且没有任何好处）。

On Windows, if `--symlink` is not used, this means that if the underlying Python installation is upgraded, the Python binary and DLLs in the venv should be updated, or there could be issues of mismatch with the upgraded standard library. The pyvenv script accepts a `--upgrade` option for easily performing this upgrade on an existing venv.

在 Windows 上，如果没有使用 `--symlink`，这意味着如果底层的 Python 安装被升级，venv 中的 Python 二进制文件和 DLL 应该被更新，否则可能会出现与升级后的标准库不匹配的问题。pyvenv 脚本接受一个 `--upgrade` 选项，可以轻松地在现有的 venv 上执行这种升级。

## [Include files](https://www.python.org/dev/peps/pep-0405/#id22)

include文件

Current virtualenv handles include files in this way:

目前virtualenv以这种方式处理 include 文件：

On POSIX systems where the installed Python's include files are found in `${base_prefix}/include/pythonX.X`, virtualenv creates `${venv}/include/` and symlinks `${base_prefix}/include/pythonX.X` to `${venv}/include/pythonX.X`. On Windows, where Python's include files are found in `{{ sys.prefix }}/Include` and symlinks are not reliably available, virtualenv copies `{{ sys.prefix }}/Include` to `${venv}/Include`. This ensures that extension modules built and installed within the virtualenv will always find the Python header files they need in the expected location relative to `sys.prefix`.

在 POSIX 系统中，如果安装的 Python 的include文件在 `${base_prefix}/include/pythonX.X` 中，virtualenv 会创建 `${venv}/include/` 并将`${base_prefix}/include/pythonX`符号链接到`${venv}/include/pythonX.X`. 在 Windows 上，Python 的include文件在 `{{ sys.prefix }}/Include` 中找到，并且符号链接不可靠，virtualenv 将 `{{ sys.prefix }}/Include` 复制到 `${venv}/Include`。这可以确保在 virtualenv 中构建和安装的扩展模块总是能在相对于` sys.prefix` 的预期位置找到它们需要的 Python 头文件。

This solution is not ideal when an extension module installs its own header files, as the default installation location for those header files may be a symlink to a system directory that may not be writable. One installer, pip, explicitly works around this by installing header files to a nonstandard location `${venv}/include/site/pythonX.X/`, as in Python there's currently no standard abstraction for a site-specific include directory.

当一个扩展模块安装自己的头文件时，这个解决方案并不理想，因为这些头文件的默认安装位置可能是一个系统目录的符号链接，而这个目录可能是不可写的。有一个安装程序，pip，明确地通过将头文件安装到一个非标准的位置 `${venv}/include/site/pythonX.X/` 来解决这个问题，因为在 Python 中，目前还没有针对特定站点的 include 目录的标准抽象概念。

This PEP proposes a slightly different approach, though one with essentially the same effect and the same set of advantages and disadvantages. Rather than symlinking or copying include files into the venv, we simply modify the sysconfig schemes so that header files are always sought relative to `base_prefix` rather than `prefix`. (We also create an `include/` directory within the venv, so installers have somewhere to put include files installed within the env).

这个PEP提出了一个稍有不同的方法，尽管它的效果基本相同，并且有同样的优缺点。我们不是通过符号链接或复制include文件到venv中，而是简单地修改sysconfig方案，使头文件总是相对于`base_prefix`而不是`prefix`被寻找。我们还在venv中创建了一个`include/`目录，这样安装人员就有地方可以放置安装在env中的include文件）。

Better handling of include files in distutils/packaging and, by extension, pyvenv, is an area that may deserve its own future PEP. For now, we propose that the behavior of virtualenv has thus far proved itself to be at least "good enough" in practice.

更好地处理distutils/packaging中的include文件，以及扩展到pyvenv，是一个值得在未来有自己的PEP的领域。现在，我们建议virtualenv的行为到目前为止已经证明了它在实践中至少是 "足够好 "的。

## [API](https://www.python.org/dev/peps/pep-0405/#id23)

The high-level method described above makes use of a simple API which provides mechanisms for third-party virtual environment creators to customize environment creation according to their needs.

上面描述的高级方法利用了一个简单的API，它为第三方虚拟环境创建者提供了根据他们的需要定制环境创建的机制。

The `venv` module contains an `EnvBuilder` class which accepts the following keyword arguments on instantiation:

`venv` 模块包含一个 `EnvBuilder` 类，在实例化时接受以下关键字参数：

- `system_site_packages` - A Boolean value indicating that the system Python site-packages should be available to the environment. Defaults to `False`.

  `system_site_packages` - 一个布尔值，表示系统的 Python site-packages 应该对环境可用。默认值是`False`。

- `clear` - A Boolean value which, if true, will delete any existing target directory instead of raising an exception. Defaults to `False`.

  `clear` - 一个布尔值，如果为真，将删除任何现有的目标目录，而不是引发一个异常。默认值为`False`。

- `symlinks` - A Boolean value indicating whether to attempt to symlink the Python binary (and any necessary DLLs or other binaries, e.g. `pythonw.exe`), rather than copying. Defaults to `False`.

  `symlinks` - 一个布尔值，表示是否尝试将Python二进制文件（以及任何必要的DLLs或其他二进制文件，例如pythonw.exe）进行符号连接，而不是复制。默认值为 `False`。

The instantiated env-builder has a `create` method, which takes as required argument the path (absolute or relative to the current directory) of the target directory which is to contain the virtual environment. The `create` method either creates the environment in the specified directory, or raises an appropriate exception.

实例化的env-builder有一个`create`方法，该方法的必要参数是目标目录的路径（绝对路径或与当前目录的相对路径），该目录将包含虚拟环境。`create`方法要么在指定的目录中创建环境，要么引发一个适当的异常。

The `venv` module also provides a module-level `create` function as a convenience:

`venv`模块还提供了一个模块级的创建功能，作为一种便利：

```
def create(env_dir,
           system_site_packages=False, clear=False, use_symlinks=False):
    builder = EnvBuilder(
        system_site_packages=system_site_packages,
        clear=clear,
        use_symlinks=use_symlinks)
    builder.create(env_dir)
```

Creators of third-party virtual environment tools are free to use the provided `EnvBuilder` class as a base class.

第三方虚拟环境工具的创建者可以自由使用提供的`EnvBuilder`类作为基类。

The `create` method of the `EnvBuilder` class illustrates the hooks available for customization:

`EnvBuilder` 类的 `create`方法说明了可用于定制的钩子（方法）：

```
def create(self, env_dir):
    """
    Create a virtualized Python environment in a directory.

    :param env_dir: The target directory to create an environment in.

    """
    env_dir = os.path.abspath(env_dir)
    context = self.create_directories(env_dir)
    self.create_configuration(context)
    self.setup_python(context)
    self.post_setup(context)
```

Each of the methods `create_directories`, `create_configuration`, `setup_python`, and `post_setup` can be overridden. The functions of these methods are:

每个方法`create_directories`、`create_configuration`、`setup_python`和`post_setup`都可以被重写。这些方法的功能如下：

- `create_directories` - creates the environment directory and all necessary directories, and returns a context object. This is just a holder for attributes (such as paths), for use by the other methods.

  `create_directories` - 创建环境目录和所有必要的目录，并返回一个context对象。这只是一个属性（如路径）的持有人，供其他方法使用。

- `create_configuration` - creates the `pyvenv.cfg` configuration file in the environment.

  `create_configuration` - 在环境中创建 `pyvenv.cfg` 配置文件。

- `setup_python` - creates a copy of the Python executable (and, under Windows, DLLs) in the environment.

  setup_python - 在环境中创建一个Python可执行文件（以及在Windows下，DLLs）的副本。

- `post_setup` - A (no-op by default) hook method which can be overridden in third party subclasses to pre-install packages or install scripts in the virtual environment.

  `post_setup` - 一个可以在第三方子类中重载的钩子方法（默认情况下无操作），用于在虚拟环境中预装软件包或安装脚本。

In addition, `EnvBuilder` provides a utility method that can be called from `post_setup` in subclasses to assist in installing custom scripts into the virtual environment. The method `install_scripts` accepts as arguments the `context` object (see above) and a path to a directory. The directory should contain subdirectories "common", "posix", "nt", each containing scripts destined for the bin directory in the environment. The contents of "common" and the directory corresponding to `os.name` are copied after doing some text replacement of placeholders:

此外，`EnvBuilder`提供了一个实用方法，可以从子类的`post_setup`中调用，以协助将自定义脚本安装到虚拟环境中。方法`install_scripts`接受`context`对象（见上文）和一个目录的路径作为参数。这个目录应该包含"common"、"posix"、"nt "等子目录，每个目录都包含了环境中的bin目录的脚本。"common "的内容和对应于`os.name`的目录在做了一些占位符的文本替换后被复制。

- `__VENV_DIR__` is replaced with absolute path of the environment directory.

   `__VENV_DIR__`被替换为环境目录的绝对路径。

- `__VENV_NAME__` is replaced with the environment name (final path segment of environment directory).

   `__VENV_NAME__`被替换为环境名称（环境目录的最终路径字段）。

- `__VENV_BIN_NAME__` is replaced with the name of the bin directory (either `bin` or `Scripts`).

  `__VENV_BIN_NAME__`被替换为bin目录的名称（无论是`bin`还是`Scripts`）。

- `__VENV_PYTHON__` is replaced with the absolute path of the environment's executable.

  `__VENV_PYTHON__`被替换为环境的可执行文件的绝对路径。

The `DistributeEnvBuilder` subclass in the reference implementation illustrates how the customization hook can be used in practice to pre-install Distribute into the virtual environment. It's not envisaged that `DistributeEnvBuilder` will be actually added to Python core, but it makes the reference implementation more immediately useful for testing and exploratory purposes.

参考实现中的`DistributeEnvBuilder`子类说明了如何在实践中使用定制钩子来预装Distribute到虚拟环境中。我们没有设想`DistributeEnvBuilder`会被实际添加到Python核心中，但它使参考实现对测试和探索性的目的更加直接有用。

# [Backwards Compatibility](https://www.python.org/dev/peps/pep-0405/#id24)

向后兼容

## [Splitting the meanings of `sys.prefix`](https://www.python.org/dev/peps/pep-0405/#id25)

拆分`sys.prefix`的意义

Any virtual environment tool along these lines (which attempts to isolate site-packages, while still making use of the base Python's standard library with no need for it to be symlinked into the virtual environment) is proposing a split between two different meanings (among others) that are currently both wrapped up in `sys.prefix`: the answers to the questions "Where is the standard library?" and "Where is the site-packages location where third-party modules should be installed?"

任何沿着这种思路的虚拟环境工具（试图隔离站点包，同时仍然利用基础Python的标准库，而不需要将其符号链接到虚拟环境中）都是在提出两个不同含义（除其他外）的分割，目前这两个含义都被包裹在`sys.prefix`中："标准库在哪里？"和 "应该安装第三方模块的站点包位置在哪里？"问题的答案。

This split could be handled by introducing a new `sys` attribute for either the former prefix or the latter prefix. Either option potentially introduces some backwards-incompatibility with software written to assume the other meaning for `sys.prefix`. (Such software should preferably be using the APIs in the `site` and `sysconfig` modules to answer these questions rather than using `sys.prefix` directly, in which case there is no backwards-compatibility issue, but in practice `sys.prefix` is sometimes used.)

这种分离可以通过为前一个前缀或后一个前缀引入一个新的`sys`属性来处理。无论哪种方案，都有可能带来一些向后兼容的软件，这些软件被写成`sys.prefix`的其他含义。这样的软件最好使用site和sysconfig模块中的API来回答这些问题，而不是直接使用`sys.prefix`，在这种情况下没有向后兼容的问题，但实际上`sys.prefix`有时会被使用）。

The [documentation](http://docs.python.org/dev/library/sys.html#sys.prefix) [[6\]](https://www.python.org/dev/peps/pep-0405/#id13) for `sys.prefix` describes it as "A string giving the site-specific directory prefix where the platform independent Python files are installed," and specifically mentions the standard library and header files as found under `sys.prefix`. It does not mention `site-packages`.

`sys.prefix`的 [文档](http://docs.python.org/dev/library/sys.html#sys.prefix) [[6\]](https://www.python.org/dev/peps/pep-0405/#id13) 将其描述为 "一个字符串，给出特定站点的目录前缀，其中安装了独立于平台的 Python 文件"，并特别提到了在`sys.prefix`下发现的标准库和头文件。它没有提到 `site-packages`。

Maintaining this documented definition would mean leaving `sys.prefix` pointing to the base system installation (which is where the standard library and header files are found), and introducing a new value in `sys` (something like `sys.site_prefix`) to point to the prefix for `site-packages`. This would maintain the documented semantics of `sys.prefix`, but risk breaking isolation if third-party code uses `sys.prefix` rather than `sys.site_prefix` or the appropriate `site` API to find site-packages directories.

保持这个文档中的定义意味着让`sys.prefix`指向基本的系统安装（这是找到标准库和头文件的地方），并在`sys`中引入一个新的值（类似`sys.site_prefix`）来指向`site-packages`的前缀。这将保持`sys.prefix`的语义，但如果第三方代码使用`sys.prefix`而不是`sys.site_prefix`或适当的站点API来寻找 site-packages 目录，则有可能破坏隔离。

The most notable case is probably [setuptools](http://peak.telecommunity.com/DevCenter/setuptools) [[3\]](https://www.python.org/dev/peps/pep-0405/#id7) and its fork [distribute](http://packages.python.org/distribute/) [[4\]](https://www.python.org/dev/peps/pep-0405/#id9), which mostly use `distutils` and `sysconfig` APIs, but do use `sys.prefix` directly to build up a list of site directories for pre-flight checking where `pth` files can usefully be placed.

最值得注意的情况可能是setuptools [3]和它的fork distribute [4]，它们大多使用distutils和sysconfig API，但确实直接使用`sys.prefix`来建立一个站点目录列表，用于 pre-flight 的检查，其中`pth`文件可以有效地放置。

Otherwise, a Google Code Search turns up what appears to be a roughly even mix of usage between packages using `sys.prefix` to build up a site-packages path and packages using it to e.g. eliminate the standard-library from code-execution tracing.

此外，谷歌代码搜索发现，使用`sys.prefix`来建立站点软件包路径的软件包和使用它来消除代码执行跟踪中的标准库的软件包之间的使用似乎是大致均匀的。

Although it requires modifying the documented definition of `sys.prefix`, this PEP prefers to have `sys.prefix` point to the virtual environment (where `site-packages` is found), and introduce `sys.base_prefix` to point to the standard library and Python header files. Rationale for this choice:

尽管这需要修改文件中对 `sys.prefix` 的定义，本 PEP 更倾向于让 `sys.prefix` 指向虚拟环境 (找到 site-packages 的地方)，并引入 `sys.base_prefix` 来指向标准库和 Python 头文件。这种选择的理由：

- It is preferable to err on the side of greater isolation of the virtual environment.

  最好是站在更大的虚拟环境隔离的一边。

- Virtualenv already modifies `sys.prefix` to point at the virtual environment, and in practice this has not been a problem.

  Virtualenv已经修改了`sys.prefix`以指向虚拟环境，在实践中这并不是一个问题。

- No modification is required to setuptools/distribute.

  setuptools/distribute不需要修改。

## [Impact on other Python implementations](https://www.python.org/dev/peps/pep-0405/#id26)

对其他Python实现的影响

The majority of this PEP's changes occur in the standard library, which is shared by other Python implementations and should not present any problem.

这个PEP的大部分变化发生在标准库中，它被其他Python实现所共享，应该不会出现任何问题。

Other Python implementations will need to replicate the new `sys.prefix`-finding behavior of the interpreter bootstrap, including locating and parsing the `pyvenv.cfg` file, if it is present.

其他Python实现将需要复制解释器引导的新的`sys.prefix`搜索行为，包括定位和解析`pyvenv.cfg`文件，如果它存在的话。

# [Reference Implementation](https://www.python.org/dev/peps/pep-0405/#id27)

参考实现

The reference implementation is found in [a clone of the CPython Mercurial repository](http://hg.python.org/sandbox/vsajip#venv) [[5\]](https://www.python.org/dev/peps/pep-0405/#id11). To test it, build and run `bin/pyvenv /path/to/new/venv` to create a virtual environment.

参考实现可以在CPython Mercurial仓库的克隆中找到[5]。要测试它，请建立并运行`bin/pyvenv /path/to/new/venv`来创建一个虚拟环境。

# [References](https://www.python.org/dev/peps/pep-0405/#id28)

| [[1\]](https://www.python.org/dev/peps/pep-0405/#id4)  | [http://www.virtualenv.org](http://www.virtualenv.org/) |
| ------------------------------------------------------ | ------------------------------------------------------- |
|                                                        |                                                         |
| [[2\]](https://www.python.org/dev/peps/pep-0405/#id6)  | https://github.com/kvbik/rvirtualenv                    |
|                                                        |                                                         |
| [[3\]](https://www.python.org/dev/peps/pep-0405/#id8)  | http://peak.telecommunity.com/DevCenter/setuptools      |
|                                                        |                                                         |
| [[4\]](https://www.python.org/dev/peps/pep-0405/#id10) | http://packages.python.org/distribute/                  |
|                                                        |                                                         |
| [[5\]](https://www.python.org/dev/peps/pep-0405/#id12) | http://hg.python.org/sandbox/vsajip#venv                |
|                                                        |                                                         |
| [[6\]](https://www.python.org/dev/peps/pep-0405/#id14) | http://docs.python.org/dev/library/sys.html#sys.prefix  |
|                                                        |                                                         |

# [Copyright](https://www.python.org/dev/peps/pep-0405/#id29)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0405.txt

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。