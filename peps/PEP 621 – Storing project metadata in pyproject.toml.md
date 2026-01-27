# PEP 621 – Storing project metadata in pyproject.toml

> 原文地址：https://peps.python.org/pep-0621

Author:

Brett Cannon <brett at python.org>, Dustin Ingram <di at python.org>, Paul Ganssle <paul at ganssle.io>, Pradyun Gedam <pradyunsg at gmail.com>, Sébastien Eustace <sebastien at eustace.io>, Thomas Kluyver <thomas at kluyver.me.uk>, Tzu-ping Chung <uranusjr at gmail.com>

Discussions-To:

[Discourse thread](https://discuss.python.org/t/pep-621-round-3/5472)

Status:

Final

Type:

Standards Track

Topic:

[Packaging](../topic/packaging/)

Created:

22-Jun-2020

Post-History:

22-Jun-2020, 18-Oct-2020, 24-Oct-2020, 31-Oct-2020

Resolution:

[Discourse message](https://discuss.python.org/t/pep-621-round-3/5472/109)

---

Table of Contents  目录

-   [Abstract](#abstract)
-   [Motivation](#motivation)
-   [Rationale](#rationale)
-   [Specification](#specification)
    -   [Details](#details)
        -   [Table name](#table-name)
        -   [`name`](#name)
        -   [`version`](#version)
        -   [`description`](#description)
        -   [`readme`](#readme)
        -   [`requires-python`](#requires-python)
        -   [`license`](#license)
        -   [`authors`/`maintainers`](#authors-maintainers)
        -   [`keywords`](#keywords)
        -   [`classifiers`](#classifiers)
        -   [`urls`](#urls)
        -   [Entry points](#entry-points)
        -   [`dependencies`/`optional-dependencies`](#dependencies-optional-dependencies)
        -   [`dynamic`](#dynamic)
    -   [Example](#example)
-   [Backwards Compatibility](#backwards-compatibility)
-   [Security Implications](#security-implications)
-   [Reference Implementation](#reference-implementation)
-   [Rejected Ideas](#rejected-ideas)
    -   [Other table names](#other-table-names)
        -   [Anything under `[build-system]`](#anything-under-build-system)
        -   [`[package]`](#package)
        -   [`[metadata]`](#metadata)
    -   [Support for a metadata provider](#support-for-a-metadata-provider)
    -   [Require a normalized project name](#require-a-normalized-project-name)
    -   [Specify files to include when building](#specify-files-to-include-when-building)
    -   [Name the `[project.urls]` table `[project.project-urls]`](#name-the-project-urls-table-project-project-urls)
    -   [Have a separate `url`/`home-page` field](#have-a-separate-url-home-page-field)
    -   [Recommend that tools put development-related dependencies into a “dev” extra](#recommend-that-tools-put-development-related-dependencies-into-a-dev-extra)
    -   [Have the `dynamic` field only require specifying missing required fields](#have-the-dynamic-field-only-require-specifying-missing-required-fields)
    -   [Different structures for the `readme` field](#different-structures-for-the-readme-field)
    -   [Allowing the `readme` field to imply `text/plain`](#allowing-the-readme-field-to-imply-text-plain)
    -   [Other names for `dependencies`/`optional-dependencies`](#other-names-for-dependencies-optional-dependencies)
    -   [Drop `maintainers` to unify with `authors`](#drop-maintainers-to-unify-with-authors)
    -   [Support an arbitrary depth of tables for `project.entry-points`](#support-an-arbitrary-depth-of-tables-for-project-entry-points)
    -   [Using structured TOML dictionaries to specify dependencies](#using-structured-toml-dictionaries-to-specify-dependencies)
    -   [Require build back-ends to update `pyproject.toml` when generating an sdist](#require-build-back-ends-to-update-pyproject-toml-when-generating-an-sdist)
    -   [Allow tools to add/extend data](#allow-tools-to-add-extend-data)
-   [Open Issues](#open-issues)
-   [Copyright](#copyright)

## [Abstract  摘要](#abstract)

This PEP specifies how to write a project’s [core metadata](https://packaging.python.org/specifications/core-metadata/) in a `pyproject.toml` file for packaging-related tools to consume.  
本 PEP 规定了如何将项目的核心元数据写入 `pyproject.toml` 文件，以便打包相关工具使用。

## [Motivation  动机](#motivation)

The key motivators of this PEP are:  
本 PEP 的主要动机是：

-   Encourage users to specify core metadata statically for speed, ease of specification, unambiguity, and deterministic consumption by build back-ends  
    鼓励用户静态指定核心元数据，以提高速度、简化规范、消除歧义，并确保构建后端的一致性消费
-   Provide a tool-agnostic way of specifying metadata for ease of learning and transitioning between build back-ends  
    提供一种与工具无关的方式来指定元数据，以便于学习和在不同构建后端之间切换
-   Allow for more code sharing between build back-ends for the “boring parts” of a project’s metadata  
    允许构建后端之间在项目的元数据“枯燥部分”进行更多代码共享

To speak specifically to the motivation for static metadata, that has been an overall goal of the packaging ecosystem for some time. As such, making it easy to specify metadata statically is important. This also means that raising the cost of specifying data as dynamic is acceptable as users should skew towards wanting to provide static metadata.  
具体来说，关于静态元数据的动机，这一直是打包生态系统的一个总体目标。因此，轻松指定静态元数据非常重要。这也意味着，提高指定动态数据的成本是可以接受的，因为用户应该倾向于提供静态元数据。

Requiring the distinction between static and dynamic metadata also helps with disambiguation for when metadata isn’t specified. When any metadata _may_ be dynamic, it means you never know if the absence of metadata is on purpose or because it is to be provided later. By requiring that dynamic metadata be specified, it disambiguates the intent when metadata goes unspecified.  
要求区分静态和动态元数据也有助于在元数据未指定时消除歧义。当任何元数据都可能动态时，这意味着你永远不知道元数据的缺失是故意的还是因为稍后会提供。通过要求指定动态元数据，它在元数据未指定时消除了意图的歧义。

This PEP does **not** attempt to standardize all possible metadata required by a build back-end, only the metadata covered by the [core metadata](https://packaging.python.org/specifications/core-metadata/) specification which are very common across projects and would stand to benefit from being static and consistently specified. This means build back-ends are still free and able to innovate around patterns like how to specify the files to include in a wheel. There is also an included escape hatch for users and build back-ends to use when they choose to partially opt-out of this PEP (compared to opting-out of this PEP entirely, which is also possible).  
本 PEP 并未试图标准化构建后端所需的所有可能元数据，仅涵盖核心元数据规范中常见的元数据，这些元数据从静态和一致指定中受益匪浅。这意味着构建后端仍然自由且能够围绕如何指定轮包中包含的文件等模式进行创新。此外，还提供了一个逃生通道，供用户和构建后端在选择部分退出本 PEP 时使用（与完全退出本 PEP 相比，后者也是可能的）。

This PEP is also not trying to change the underlying [core metadata](https://packaging.python.org/specifications/core-metadata/) in any way. Such considerations should be done in a separate PEP which may lead to changes or additions to what this PEP specifies.  
本 PEP 也不试图以任何方式改变底层核心元数据。此类考虑应在单独的 PEP 中进行，该 PEP 可能导致对本文 PEP 指定内容的变更或补充。

## [Rationale  理由](#rationale)

The design guidelines the authors of this PEP followed were:  
本 PEP 的作者遵循的设计指南是：

-   Define a representation of as much of the [core metadata](https://packaging.python.org/specifications/core-metadata/) in `pyproject.toml` as is reasonable  
    在 `pyproject.toml` 中定义尽可能多的核心元数据的表示
-   Define the metadata statically with an escape hatch for those who want to define it dynamically later via a build back-end  
    静态定义元数据，为那些希望稍后通过构建后端动态定义的人提供一个逃生通道
-   Use familiar names where it makes sense, but be willing to use more modern terminology  
    在合适的地方使用熟悉的名字，但也愿意使用更现代的术语
-   Try to be ergonomic within a TOML file instead of mirroring how build back-ends specify metadata at a low-level when it makes sense  
    尽量在 TOML 文件中保持易用性，而不是在合适的时候镜像构建后端在低级别上指定元数据的方式
-   Learn from other build back-ends in the packaging ecosystem which have used TOML for their metadata  
    从包装生态系统中使用 TOML 作为其元数据的其他构建后端中学习
-   Don’t try to standardize things which lack a pre-existing standard at a lower-level  
    不要试图标准化那些在较低级别缺乏现有标准的事情
-   _When_ metadata is specified using this PEP, it is considered canonical  
    当使用此 PEP 指定元数据时，它被视为规范

## [Specification  规范](#specification)

When specifying project metadata, tools MUST adhere and honour the metadata as specified in this PEP. If metadata is improperly specified then tools MUST raise an error to notify the user about their mistake.  
在指定项目元数据时，工具必须遵守并尊重本 PEP 中指定的元数据。如果元数据指定不当，工具必须引发错误以通知用户他们的错误。

Data specified using this PEP is considered canonical. Tools CANNOT remove, add or change data that has been statically specified. Only when a field is marked as `dynamic` may a tool provide a “new” value.  
使用此 PEP 指定的数据被视为权威。工具不能删除、添加或更改静态指定的数据。只有当字段被标记为 `dynamic` 时，工具才能提供“新”值。

### [Details  详细说明](#details)

#### [Table name  表名](#table-name)

Tools MUST specify fields defined by this PEP in a table named `[project]`. No tools may add fields to this table which are not defined by this PEP or subsequent PEPs. For tools wishing to store their own settings in `pyproject.toml`, they may use the `[tool]` table as defined in [PEP 518](../pep-0518/ "PEP 518 – Specifying Minimum Build System Requirements for Python Projects"). The lack of a `[project]` table implicitly means the build back-end will dynamically provide all fields.  
工具必须在本 PEP 定义的字段指定一个名为 `[project]` 的表。任何工具都不能向此表中添加本 PEP 或后续 PEP 未定义的字段。希望将自身设置存储在 `pyproject.toml` 中的工具可以使用 PEP 518 中定义的 `[tool]` 表。缺少 `[project]` 表隐含表示构建后端将动态提供所有字段。

#### [`name`](#name)

-   Format: string  格式：字符串
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `Name` ([link](https://packaging.python.org/specifications/core-metadata/#name))  
    核心元数据： `Name` (链接)
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): `module`/`dist-name` ([link](https://flit.readthedocs.io/en/latest/pyproject_toml.html#metadata-section))  
        Flit： `module` / `dist-name` (链接)
    -   [Poetry](https://python-poetry.org/): `name` ([link](https://python-poetry.org/docs/pyproject/#name))  
        Poetry: `name` (链接)
    -   [Setuptools](https://setuptools.readthedocs.io/): `name` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `name` (链接)

The name of the project.  
项目的名称。

Tools MUST require users to statically define this field.  
工具必须要求用户静态定义此字段。

Tools SHOULD normalize this name, as specified by [PEP 503](../pep-0503/ "PEP 503 – Simple Repository API"), as soon as it is read for internal consistency.  
工具应按照 PEP 503 的规定，在读取后立即对此名称进行规范化处理，以确保内部一致性。

#### [`version`](#version)

-   Format: string  格式：字符串
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `Version` ([link](https://packaging.python.org/specifications/core-metadata/#version))  
    核心元数据： `Version` (链接)
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): N/A (read from a `__version__` attribute) ([link](https://flit.readthedocs.io/en/latest/index.html#usage))  
        Flit: N/A（从 `__version__` 属性中读取）（链接）
    -   [Poetry](https://python-poetry.org/): `version` ([link](https://python-poetry.org/docs/pyproject/#version))  
        Poetry: `version` (链接)
    -   [Setuptools](https://setuptools.readthedocs.io/): `version` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `version` (链接)

The version of the project as supported by [PEP 440](../pep-0440/ "PEP 440 – Version Identification and Dependency Specification").  
PEP 440 支持的项目版本。

Users SHOULD prefer to specify already-normalized versions.  
用户应当优先指定已经标准化了的版本。

#### [`description`](#description)

-   Format: string  格式：字符串
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `Summary` ([link](https://packaging.python.org/specifications/core-metadata/#summary))  
    核心元数据： `Summary` (链接)
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): N/A  Flit: 不适用
    -   [Poetry](https://python-poetry.org/): `description` ([link](https://python-poetry.org/docs/pyproject/#description))  
        Poetry: `description` (链接)
    -   [Setuptools](https://setuptools.readthedocs.io/): `description` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `description` (链接)

The summary description of the project.  
项目的摘要描述。

#### [`readme`](#readme)

-   Format: String or table  
    格式：字符串或表格
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `Description` ([link](https://packaging.python.org/specifications/core-metadata/#description))  
    核心元数据： `Description` (链接)
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): `description-file` ([link](https://flit.readthedocs.io/en/latest/pyproject_toml.html#metadata-section))  
        Flit: `description-file` (链接)
    -   [Poetry](https://python-poetry.org/): `readme` ([link](https://python-poetry.org/docs/pyproject/#readme))  
        Poetry: `readme` (链接)
    -   [Setuptools](https://setuptools.readthedocs.io/): `long_description` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `long_description` (链接)

The full description of the project (i.e. the README).  
项目的完整描述（即 README）。

The field accepts either a string or a table. If it is a string then it is the relative path to a text file containing the full description. Tools MUST assume the file’s encoding is UTF-8. If the file path ends in a case-insensitive `.md` suffix, then tools MUST assume the content-type is `text/markdown`. If the file path ends in a case-insensitive `.rst`, then tools MUST assume the content-type is `text/x-rst`. If a tool recognizes more extensions than this PEP, they MAY infer the content-type for the user without specifying this field as `dynamic`. For all unrecognized suffixes when a content-type is not provided, tools MUST raise an error.  
该字段接受字符串或表格。如果是字符串，则表示包含完整描述的文本文件的相对路径。工具必须假设该文件的编码为 UTF-8。如果文件路径以不区分大小写的 `.md` 后缀结尾，则工具必须假设内容类型为 `text/markdown` 。如果文件路径以不区分大小写的 `.rst` 后缀结尾，则工具必须假设内容类型为 `text/x-rst` 。如果一个工具识别的扩展名比本 PEP 更多，它们可以推断用户的内容类型，而无需指定此字段为 `dynamic` 。当未提供内容类型时，对于所有未识别的后缀，工具必须报错。

The `readme` field may also take a table. The `file` key has a string value representing a relative path to a file containing the full description. The `text` key has a string value which is the full description. These keys are mutually-exclusive, thus tools MUST raise an error if the metadata specifies both keys.  
`readme` 字段也可以是一个表格。 `file` 键有一个字符串值，表示指向包含完整描述的文件的相对路径。 `text` 键有一个字符串值，即完整描述。这些键是互斥的，因此如果元数据同时指定了这两个键，工具必须抛出错误。

A table specified in the `readme` field also has a `content-type` field which takes a string specifying the content-type of the full description. A tool MUST raise an error if the metadata does not specify this field in the table. If the metadata does not specify the `charset` parameter, then it is assumed to be UTF-8. Tools MAY support other encodings if they choose to. Tools MAY support alternative content-types which they can transform to a content-type as supported by the [core metadata](https://packaging.python.org/specifications/core-metadata/). Otherwise tools MUST raise an error for unsupported content-types.  
在 `readme` 字段中指定的表格还有一个 `content-type` 字段，该字段使用字符串指定完整描述的内容类型。如果元数据未在表格中指定此字段，工具必须引发错误。如果元数据未指定 `charset` 参数，则假定其为 UTF-8。如果工具选择支持，则可以支持其他编码。工具可以支持它们可以转换为核心元数据支持的内容类型的替代内容类型。否则，对于不支持的 内容类型，工具必须引发错误。

#### [`requires-python`](#requires-python)

-   Format: string  格式：字符串
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `Requires-Python` ([link](https://packaging.python.org/specifications/core-metadata/#summary))  
    核心元数据： `Requires-Python` (链接)
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): `requires-python` ([link](https://flit.readthedocs.io/en/latest/pyproject_toml.html#metadata-section))  
        Flit: `requires-python` (链接)
    -   [Poetry](https://python-poetry.org/): As a `python` dependency in the `[tool.poetry.dependencies]` table ([link](https://python-poetry.org/docs/pyproject/#dependencies-and-dev-dependencies))  
        Poetry: 作为 `python` 表格中的依赖项（ `[tool.poetry.dependencies]` 表格）（链接）
    -   [Setuptools](https://setuptools.readthedocs.io/): `python_requires` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `python_requires` (链接)

The Python version requirements of the project.  
项目的 Python 版本要求。

#### [`license`](#license)

-   Format: Table  格式：表格
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `License` ([link](https://packaging.python.org/specifications/core-metadata/#license))  
    核心元数据： `License` (链接)
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): `license` ([link](https://flit.readthedocs.io/en/latest/pyproject_toml.html#metadata-section))  
        Flit: `license` (链接)
    -   [Poetry](https://python-poetry.org/): `license` ([link](https://python-poetry.org/docs/pyproject/#license))  
        Poetry: `license` (链接)
    -   [Setuptools](https://setuptools.readthedocs.io/): `license`, `license_file`, `license_files` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `license` , `license_file` , `license_files` (链接)

The table may have one of two keys. The `file` key has a string value that is a relative file path to the file which contains the license for the project. Tools MUST assume the file’s encoding is UTF-8. The `text` key has a string value which is the license of the project whose meaning is that of the `License` field from the [core metadata](https://packaging.python.org/specifications/core-metadata/). These keys are mutually exclusive, so a tool MUST raise an error if the metadata specifies both keys.  
该表可以有两个键之一。 `file` 键有一个字符串值，是项目许可证文件所在文件的相对文件路径。工具必须假设该文件的编码为 UTF-8。 `text` 键有一个字符串值，是项目的许可证，其含义与核心元数据中的 `License` 字段相同。这些键是互斥的，因此如果元数据同时指定了这两个键，工具必须引发错误。

A practical string value for the `license` key has been purposefully left out to allow for a future PEP to specify support for [SPDX](https://spdx.dev/) expressions (the same logic applies to any sort of “type” field specifying what license the `file` or `text` represents).  
为 `license` 键留出一个实用的字符串值，以便未来 PEP 可以指定对 SPDX 表达式的支持（相同的逻辑适用于任何指定 `file` 或 `text` 代表的许可证的“类型”字段）。

#### [`authors`/`maintainers`](#authors-maintainers)

-   Format: Array of inline tables with string keys and values  
    格式：包含字符串键值对的行内表格数组
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `Author`/`Author-email`/`Maintainer`/`Maintainer-email` ([link](https://packaging.python.org/specifications/core-metadata/#author))  
    核心元数据： `Author` / `Author-email` / `Maintainer` / `Maintainer-email` (链接)
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): `author`/`author-email`/`maintainer`/`maintainer-email` ([link](https://flit.readthedocs.io/en/latest/pyproject_toml.html#metadata-section))  
        Flit: `author` / `author-email` / `maintainer` / `maintainer-email` (链接)
    -   [Poetry](https://python-poetry.org/): `authors`/`maintainers` ([link](https://python-poetry.org/docs/pyproject/#authors))  
        Poetry: `authors` / `maintainers` (链接)
    -   [Setuptools](https://setuptools.readthedocs.io/): `author`/`author_email`/`maintainer`/`maintainer_email` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `author` / `author_email` / `maintainer` / `maintainer_email` (链接)

The people or organizations considered to be the “authors” of the project. The exact meaning is open to interpretation — it may list the original or primary authors, current maintainers, or owners of the package.  
被认为是项目“作者”的人员或组织。具体含义可以有多种解释——它可能列出原始或主要作者、当前维护者或软件包的所有者。

The “maintainers” field is similar to “authors” in that its exact meaning is open to interpretation.  
“maintainers”字段与“authors”字段类似，其确切含义是开放的，可以有多种解释。

These fields accept an array of tables with 2 keys: `name` and `email`. Both values must be strings. The `name` value MUST be a valid email name (i.e. whatever can be put as a name, before an email, in [**RFC 822**](https://datatracker.ietf.org/doc/html/rfc822.html)) and not contain commas. The `email` value MUST be a valid email address. Both keys are optional.  
这些字段接受一个包含 2 个键的表格数组： `name` 和 `email` 。两个值都必须是字符串。 `name` 值必须是有效的电子邮件名称（即 RFC 822 中放在电子邮件之前可以使用的任何名称），并且不能包含逗号。 `email` 值必须是有效的电子邮件地址。这两个键都是可选的。

Using the data to fill in [core metadata](https://packaging.python.org/specifications/core-metadata/) is as follows:  
使用数据填充核心元数据如下：

1.  If only `name` is provided, the value goes in `Author`/`Maintainer` as appropriate.  
    如果仅提供 `name` ，则值将根据情况放入 `Author` / `Maintainer` 。
2.  If only `email` is provided, the value goes in `Author-email`/`Maintainer-email` as appropriate.  
    如果仅提供 `email` ，则值将根据情况放入 `Author-email` / `Maintainer-email` 。
3.  If both `email` and `name` are provided, the value goes in `Author-email`/`Maintainer-email` as appropriate, with the format `{name} <{email}>` (with appropriate quoting, e.g. using `email.headerregistry.Address`).  
    如果同时提供 `email` 和 `name` ，则值将根据情况放入 `Author-email` / `Maintainer-email` ，格式为 `{name} <{email}>` （使用适当的引号，例如使用 `email.headerregistry.Address` ）。
4.  Multiple values should be separated by commas.  
    多个值应该用逗号分隔。

#### [`keywords`](#keywords)

-   Format: array of strings  
    格式：字符串数组
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `Keywords` ([link](https://packaging.python.org/specifications/core-metadata/#keywords))  
    核心元数据： `Keywords` (链接)
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): `keywords` ([link](https://flit.readthedocs.io/en/latest/pyproject_toml.html#metadata-section))  
        Flit: `keywords` (链接)
    -   [Poetry](https://python-poetry.org/): `keywords` ([link](https://python-poetry.org/docs/pyproject/#keywords))  
        Poetry: `keywords` (链接)
    -   [Setuptools](https://setuptools.readthedocs.io/): `keywords` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `keywords` (链接)

The keywords for the project.  
项目的关键词。

#### [`classifiers`](#classifiers)

-   Format: array of strings  
    格式：字符串数组
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `Classifier` ([link](https://packaging.python.org/specifications/core-metadata/#classifier-multiple-use))  
    核心元数据： `Classifier` (链接)
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): `classifiers` ([link](https://flit.readthedocs.io/en/latest/pyproject_toml.html#metadata-section))  
        Flit: `classifiers` (链接)
    -   [Poetry](https://python-poetry.org/): `classifiers` ([link](https://python-poetry.org/docs/pyproject/#classifiers))  
        Poetry: `classifiers` (链接)
    -   [Setuptools](https://setuptools.readthedocs.io/): `classifiers` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `classifiers` (链接)

[Trove classifiers](https://pypi.org/classifiers/) which apply to the project.  
适用于项目的 Trove 分类器。

#### [`urls`](#urls)

-   Format: Table, with keys and values of strings  
    格式：表格，包含字符串的键和值
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `Project-URL` ([link](https://packaging.python.org/specifications/core-metadata/#project-url-multiple-use))  
    核心元数据： `Project-URL` (链接)
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): `[tool.flit.metadata.urls]` table ([link](https://flit.readthedocs.io/en/latest/pyproject_toml.html#metadata-section))  
        Flit: `[tool.flit.metadata.urls]` 表格 (链接)
    -   [Poetry](https://python-poetry.org/): `[tool.poetry.urls]` table ([link](https://python-poetry.org/docs/pyproject/#urls))  
        Poetry: `[tool.poetry.urls]` 表格 (链接)
    -   [Setuptools](https://setuptools.readthedocs.io/): `project_urls` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `project_urls` (链接)

A table of URLs where the key is the URL label and the value is the URL itself.  
一个 URL 表，其中键是 URL 标签，值是 URL 本身。

#### [Entry points  入口点](#entry-points)

-   Format: Table (`[project.scripts]`, `[project.gui-scripts]`, and `[project.entry-points]`)  
    格式：表格（ `[project.scripts]` ， `[project.gui-scripts]` ，和 `[project.entry-points]` ）
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): N/A; [Entry points specification](https://packaging.python.org/specifications/entry-points/)  
    核心元数据：N/A；入口点规范
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): `[tool.flit.scripts]` table for console scripts, `[tool.flit.entrypoints]` for the rest ([link](https://flit.readthedocs.io/en/latest/pyproject_toml.html#scripts-section))  
        Flit: `[tool.flit.scripts]` 表用于控制台脚本， `[tool.flit.entrypoints]` 用于其他（链接）
    -   [Poetry](https://python-poetry.org/): `[tool.poetry.scripts]` table for console scripts ([link](https://python-poetry.org/docs/pyproject/#scripts))  
        Poetry: `[tool.poetry.scripts]` 表用于控制台脚本（链接）
    -   [Setuptools](https://setuptools.readthedocs.io/): `entry_points` ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `entry_points` (链接)

There are three tables related to entry points. The `[project.scripts]` table corresponds to the `console_scripts` group in the [entry points specification](https://packaging.python.org/specifications/entry-points/). The key of the table is the name of the entry point and the value is the object reference.  
有三个与入口点相关的表。 `[project.scripts]` 表对应于入口点规范中的 `console_scripts` 组。表的键是入口点的名称，值是对象引用。

The `[project.gui-scripts]` table corresponds to the `gui_scripts` group in the [entry points specification](https://packaging.python.org/specifications/entry-points/). Its format is the same as `[project.scripts]`.  
`[project.gui-scripts]` 表格对应于 entry points 规范中的 `gui_scripts` 组。其格式与 `[project.scripts]` 相同。

The `[project.entry-points]` table is a collection of tables. Each sub-table’s name is an entry point group. The key and value semantics are the same as `[project.scripts]`. Users MUST NOT create nested sub-tables but instead keep the entry point groups to only one level deep.  
`[project.entry-points]` 表格是一个表格集合。每个子表格的名称是一个入口点组。键和值的语义与 `[project.scripts]` 相同。用户必须不得创建嵌套子表格，而应将入口点组保持在一个级别深度。

Build back-ends MUST raise an error if the metadata defines a `[project.entry-points.console_scripts]` or `[project.entry-points.gui_scripts]` table, as they would be ambiguous in the face of `[project.scripts]` and `[project.gui-scripts]`, respectively.  
构建后端如果元数据定义了 `[project.entry-points.console_scripts]` 或 `[project.entry-points.gui_scripts]` 表格，必须抛出错误，因为它们在 `[project.scripts]` 和 `[project.gui-scripts]` 面前会变得模糊不清。

#### [`dependencies`/`optional-dependencies`](#dependencies-optional-dependencies)

-   Format: Array of [PEP 508](../pep-0508/ "PEP 508 – Dependency specification for Python Software Packages") strings (`dependencies`) and a table with values of arrays of [PEP 508](../pep-0508/ "PEP 508 – Dependency specification for Python Software Packages") strings (`optional-dependencies`)  
    格式：PEP 508 字符串数组（ `dependencies` ）和一个值是 PEP 508 字符串数组的表格（ `optional-dependencies` ）
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): `Requires-Dist` and `Provides-Extra` ([link](https://packaging.python.org/specifications/core-metadata/#requires-dist-multiple-use), [link](https://packaging.python.org/specifications/core-metadata/#provides-extra-multiple-use))  
    核心元数据： `Requires-Dist` 和 `Provides-Extra` （链接，链接）
-   Synonyms  同义词
    -   [Flit](https://flit.readthedocs.io/): `requires` for required dependencies, `requires-extra` for optional dependencies ([link](https://flit.readthedocs.io/en/latest/pyproject_toml.html#metadata-section))  
        Flit: `requires` 用于必需依赖， `requires-extra` 用于可选依赖（链接）
    -   [Poetry](https://python-poetry.org/): `[tool.poetry.dependencies]` for dependencies (both required and for development), `[tool.poetry.extras]` for optional dependencies ([link](https://python-poetry.org/docs/pyproject/#dependencies-and-dev-dependencies))  
        Poetry: `[tool.poetry.dependencies]` 用于依赖（包括必需和开发）， `[tool.poetry.extras]` 用于可选依赖（链接）
    -   [Setuptools](https://setuptools.readthedocs.io/): `install_requires` for required dependencies, `extras_require` for optional dependencies ([link](https://setuptools.readthedocs.io/en/latest/setuptools.html#metadata))  
        Setuptools: `install_requires` 用于必需依赖， `extras_require` 用于可选依赖（链接）

The (optional) dependencies of the project.  
项目的（可选）依赖。

For `dependencies`, it is a key whose value is an array of strings. Each string represents a dependency of the project and MUST be formatted as a valid [PEP 508](../pep-0508/ "PEP 508 – Dependency specification for Python Software Packages") string. Each string maps directly to a `Requires-Dist` entry in the [core metadata](https://packaging.python.org/specifications/core-metadata/).  
对于 `dependencies` ，它是一个键，其值是一个字符串数组。每个字符串代表项目的依赖项，并且必须按照有效的 PEP 508 字符串格式进行格式化。每个字符串直接映射到核心元数据中的 `Requires-Dist` 条目。

For `optional-dependencies`, it is a table where each key specifies an extra and whose value is an array of strings. The strings of the arrays must be valid [PEP 508](../pep-0508/ "PEP 508 – Dependency specification for Python Software Packages") strings. The keys MUST be valid values for the `Provides-Extra` [core metadata](https://packaging.python.org/specifications/core-metadata/). Each value in the array thus becomes a corresponding `Requires-Dist` entry for the matching `Provides-Extra` metadata.  
对于 `optional-dependencies` ，它是一个表，其中每个键指定一个额外内容，其值是一个字符串数组。数组中的字符串必须是有效的 PEP 508 字符串。键必须是 `Provides-Extra` 核心元数据的有效值。因此，数组中的每个值都成为匹配的 `Provides-Extra` 元数据的对应 `Requires-Dist` 条目。

#### [`dynamic`](#dynamic)

-   Format: Array of strings  
    格式：字符串数组
-   [Core metadata](https://packaging.python.org/specifications/core-metadata/): N/A  核心元数据：不适用
-   No synonyms  没有同义词

Specifies which fields listed by this PEP were intentionally unspecified so another tool can/will provide such metadata dynamically. This clearly delineates which metadata is purposefully unspecified and expected to stay unspecified compared to being provided via tooling later on.  
指定了本 PEP 列出的哪些字段是有意未指定的，以便另一个工具可以/将会动态提供此类元数据。这明确界定了哪些元数据是有意未指定的，并且预期会保持未指定，而不是通过工具后来提供。

-   A build back-end MUST honour statically-specified metadata (which means the metadata did not list the field in `dynamic`).  
    一个构建后端必须遵守静态指定的元数据（这意味着元数据没有在 `dynamic` 中列出该字段）。
-   A build back-end MUST raise an error if the metadata specifies the `name` in `dynamic`.  
    构建后端如果元数据指定了 `name` 在 `dynamic` 中，必须抛出错误。
-   If the [core metadata](https://packaging.python.org/specifications/core-metadata/) specification lists a field as “Required”, then the metadata MUST specify the field statically or list it in `dynamic` (build back-ends MUST raise an error otherwise, i.e. it should not be possible for a required field to not be listed somehow in the `[project]` table).  
    如果核心元数据规范将某个字段列为“必需”，那么元数据必须静态指定该字段，或者将其列在 `dynamic` 中（构建后端必须在这种情况下引发错误，即不应有可能必需字段未被以某种方式列在 `[project]` 表中）。
-   If the [core metadata](https://packaging.python.org/specifications/core-metadata/) specification lists a field as “Optional”, the metadata MAY list it in `dynamic` if the expectation is a build back-end will provide the data for the field later.  
    如果核心元数据规范将某个字段列为“可选”，那么元数据可能会在 `dynamic` 中列出该字段，如果预期是构建后端稍后会提供该字段的数据。
-   Build back-ends MUST raise an error if the metadata specifies a field statically as well as being listed in `dynamic`.  
    构建后端如果静态指定了某个字段，并且该字段也列在 `dynamic` 中，则必须抛出错误。
-   If the metadata does not list a field in `dynamic`, then a build back-end CANNOT fill in the requisite metadata on behalf of the user (i.e. `dynamic` is the only way to allow a tool to fill in metadata and the user must opt into the filling in).  
    如果元数据没有在 `dynamic` 中列出某个字段，那么构建后端不能代表用户填写必要的元数据（即 `dynamic` 是唯一允许工具填写元数据的方式，用户必须选择填写）。
-   Build back-ends MUST raise an error if the metadata specifies a field in dynamic but the build back-end was unable to provide the data for it.  
    构建后端如果动态指定了某个字段，但无法提供该字段的数据，则必须抛出错误。

### [Example  示例](#example)

```toml
[project]
name = "spam"
version = "2020.0.0"
description = "Lovely Spam! Wonderful Spam!"
readme = "README.rst"
requires-python = ">=3.8"
license = {file = "LICENSE.txt"}
keywords = ["egg", "bacon", "sausage", "tomatoes", "Lobster Thermidor"]
authors = [
  {email = "hi@pradyunsg.me"},
  {name = "Tzu-ping Chung"}
]
maintainers = [
  {name = "Brett Cannon", email = "brett@python.org"}
]
classifiers = [
  "Development Status :: 4 - Beta",
  "Programming Language :: Python"
]

dependencies = [
  "httpx",
  "gidgethub[httpx]>4.0.0",
  "django>2.1; os_name != 'nt'",
  "django>2.0; os_name == 'nt'"
]

[project.optional-dependencies]
test = [
  "pytest < 5.0.0",
  "pytest-cov[all]"
]

[project.urls]
homepage = "https://example.com"
documentation = "https://readthedocs.org"
repository = "https://github.com"
changelog = "https://github.com/me/spam/blob/master/CHANGELOG.md"

[project.scripts]
spam-cli = "spam:main_cli"

[project.gui-scripts]
spam-gui = "spam:main_gui"

[project.entry-points."spam.magical"]
tomatoes = "spam:main_tomatoes"
```

## [Backwards Compatibility  向后兼容](#backwards-compatibility)

As this provides a new way to specify a project’s [core metadata](https://packaging.python.org/specifications/core-metadata/) and is using a new table name which falls under the reserved namespace as outlined in [PEP 518](../pep-0518/ "PEP 518 – Specifying Minimum Build System Requirements for Python Projects"), there are no backwards-compatibility concerns.  
这种方式提供了一种新的指定项目核心元数据的方法，并且使用了一个新的表名，该表名属于 PEP 518 中定义的保留命名空间，因此不存在向后兼容性问题。

## [Security Implications  安全影响](#security-implications)

There are no direct security concerns as this PEP covers how to statically define project metadata. Any security issues would stem from how tools consume the metadata and choose to act upon it.  
由于本 PEP 涵盖了如何静态定义项目元数据，因此没有直接的安全问题。任何安全问题都源于工具如何消费元数据并决定如何处理它。

## [Reference Implementation  参考实现](#reference-implementation)
There are currently no proofs-of-concept from any build back-end implementing this PEP.  
目前还没有任何构建后端实现本 PEP 的概念验证。

## [Rejected Ideas  被拒绝的想法](#rejected-ideas)

### [Other table names  其他表名](#other-table-names)

#### [Anything under `[build-system]`   `[build-system]` 下的任何内容](#anything-under-build-system)

There was worry that using this table name would exacerbate confusion between build metadata and project metadata, e.g. by using `[build-system.metadata]` as a table.  
有人担心使用这个表名会加剧构建元数据和项目元数据之间的混淆，例如将 `[build-system.metadata]` 用作表。

#### [`[package]`](#package)

Garnered no strong support.  
没有得到强有力的支持。

#### [`[metadata]`](#metadata)

The strongest contender after `[project]`, but in the end it was agreed that `[project]` read better for certain sub-tables, e.g. `[project.urls]`.  
在 `[project]` 之后的最有力竞争者，但最终大家一致认为 `[project]` 在某些子表中读起来更好，例如 `[project.urls]` 。

### [Support for a metadata provider  支持元数据提供者](#support-for-a-metadata-provider)
Initially there was a proposal to add a middle layer between the static metadata specified by this PEP and `prepare_metadata_for_build_wheel()` as specified by [PEP 517](../pep-0517/ "PEP 517 – A build-system independent format for source trees"). The idea was that if a project wanted to insert itself between a build back-end and the metadata there would be a hook to do so.  
最初曾有一项提议，在 PEP 中指定的静态元数据与 PEP 517 指定的 `prepare_metadata_for_build_wheel()` 之间添加一个中间层。该想法是，如果项目希望在构建后端和元数据之间插入自己，将会有一个钩子来实现这一点。

In the end the authors considered this idea unnecessarily complicated and would move the PEP away from its design goal to push people to define core metadata statically as much as possible.  
最终，作者们认为这一想法过于复杂，会使 PEP 偏离其设计目标，即推动人们尽可能静态地定义核心元数据。

### [Require a normalized project name  需要规范化的项目名称](#require-a-normalized-project-name)
While it would make things easier for tools to only work with the normalized name as specified in [PEP 503](../pep-0503/ "PEP 503 – Simple Repository API"), the idea was ultimately rejected as it would hurt projects transitioning to using this PEP.  
虽然仅使用 PEP 503 中指定的标准化名称会让工具更方便工作，但最终这个想法被否决了，因为它会阻碍项目迁移到使用这个 PEP。

### [Specify files to include when building  指定构建时包含的文件](#specify-files-to-include-when-building)
The authors decided fairly quickly during design discussions that this PEP should focus exclusively on project metadata and not build metadata. As such, specifying what files should end up in a source distribution or wheel file is out of scope for this PEP.  
在设计讨论中，作者们很快就决定这个 PEP 应专注于项目元数据而不是构建元数据。因此，指定哪些文件应该最终出现在源分发包或轮文件中不在本 PEP 的范围内。

### [Name the `[project.urls]` table `[project.project-urls]`  为 `[project.urls]` 表命名 `[project.project-urls]`](#name-the-project-urls-table-project-project-urls)
This suggestion came thanks to the corresponding [core metadata](https://packaging.python.org/specifications/core-metadata/) being `Project-Url`. But once the overall table name of `[project]` was chosen, the redundant use of the word “project” suggested the current, shorter name was a better fit.  
这个建议得益于相应的核心元数据是 `Project-Url` 。但一旦选择了 `[project]` 的整体表名，"project"一词的冗余使用表明当前较短的名称更合适。

### [Have a separate `url`/`home-page` field  拥有单独的 `url` / `home-page` 字段](#have-a-separate-url-home-page-field)
While the [core metadata](https://packaging.python.org/specifications/core-metadata/) supports it, having a single field for a project’s URL while also supporting a full table seemed redundant and confusing.  
虽然核心元数据支持它，但项目 URL 使用单个字段同时支持完整表似乎冗余且令人困惑。

### [Recommend that tools put development-related dependencies into a “dev” extra  建议工具将开发相关的依赖项放入“dev”额外部分](#recommend-that-tools-put-development-related-dependencies-into-a-dev-extra)
As various tools have grown the concept of required dependencies versus development dependencies, the idea of suggesting to tools that they put such development tool into a “dev” grouping came up. In the end, though, the authors deemed it out-of-scope for this specification to suggest such a workflow.  
随着各种工具的发展，关于必需依赖与开发依赖的概念逐渐形成，有人提出建议让工具将这些开发工具归入“dev”分组。但最终，作者们认为这一工作流程超出了本规范的范畴。

### [Have the `dynamic` field only require specifying missing required fields  让 `dynamic` 字段仅要求指定缺失的必需字段](#have-the-dynamic-field-only-require-specifying-missing-required-fields)
The authors considered the idea that the `dynamic` field would only require the listing of missing required fields and make listing optional fields optional. In the end, though, this went against the design goal of promoting specifying as much information statically as possible.  
作者们曾考虑过一种想法，即 `dynamic` 字段只需列出缺失的必需字段，并使可选字段的列出变为可选。但最终，这一想法违背了尽可能静态地指定信息的设设计目标。

### [Different structures for the `readme` field  `readme` 字段的不同的结构](#different-structures-for-the-readme-field)
The `readme` field had a proposed `readme_content_type` field, but the authors considered the string/table hybrid more practical for the common case while still accommodating the more complex case. Same goes for using `long_description` and a corresponding `long_description_content_type` field.  
`readme` 字段曾有一个提议的 `readme_content_type` 字段，但作者们认为字符串/表格混合形式在常见情况下更为实用，同时仍能容纳更复杂的情况。使用 `long_description` 和相应的 `long_description_content_type` 字段也是如此。

The `file` key in the table format was originally proposed as `path`, but `file` corresponds to setuptools’ `file` key and there is no strong reason otherwise to choose one over the other.  
表格格式中的 `file` 键最初提议为 `path` ，但 `file` 对应于 setuptools 的 `file` 键，且没有充分的理由选择其中一个而非另一个。

### [Allowing the `readme` field to imply `text/plain`  允许 `readme` 字段暗示 `text/plain`](#allowing-the-readme-field-to-imply-text-plain)
The authors considered allowing for unspecified content-types which would default to `text/plain`, but decided that it would be best to be explicit in this case to prevent accidental incorrect renderings on PyPI and to force users to be clear in their intent.  
作者们曾考虑允许未指定内容类型，默认为 `text/plain` ，但决定在这种情况下明确指定，以防止 PyPI 上的意外错误渲染，并迫使用户明确其意图。

### [Other names for `dependencies`/`optional-dependencies`  其他 `dependencies` 的名称 / `optional-dependencies`](#other-names-for-dependencies-optional-dependencies)
The authors originally proposed `requires`/`extra-requires` as names, but decided to go with the current names after a survey of other packaging ecosystems showed Python was an outlier:  
作者们最初提议使用 `requires` / `extra-requires` 作为名称，但在对其他打包生态系统进行调查后发现 Python 是一个特例，因此决定采用当前名称：

1.  [npm](https://docs.npmjs.com/files/package.json#optionaldependencies)
2.  [Rust](https://doc.rust-lang.org/cargo/guide/dependencies.html)
3.  [Dart](https://dart.dev/guides/packages)
4.  [Swift](https://swift.org/package-manager/)
5.  [Ruby](https://guides.rubygems.org/specification-reference/#add_runtime_dependency)

Normalizing on the current names helps minimize confusion for people coming from other ecosystems without using terminology that is necessarily foreign to new programmers. It also prevents potential confusion with `requires` in the `[build-system]` table as specified in [PEP 518](../pep-0518/ "PEP 518 – Specifying Minimum Build System Requirements for Python Projects").  
统一当前名称有助于减少来自其他生态系统的人的困惑，同时也不使用对新手程序员来说必然是陌生的术语。它还可以防止与 PEP 518 中指定的 `[build-system]` 表中的 `requires` 产生潜在混淆。

### [Drop `maintainers` to unify with `authors`  将 `maintainers` 降级以与 `authors` 统一](#drop-maintainers-to-unify-with-authors)
As the difference between `Authors` and `Maintainers` fields in the [core metadata](https://packaging.python.org/specifications/core-metadata/) is unspecified and ambiguous, this PEP originally proposed unifying them as a single `authors` field. Other ecosystems have selected “author” as the term to use, so the thinking was to standardize on `Author` in the core metadata as the place to list people maintaining a project.  
由于核心元数据中 `Authors` 和 `Maintainers` 字段之间的差异未明确规定且存在歧义，该 PEP 最初提议将它们统一为一个 `authors` 字段。其他生态系统已选择使用“author”作为术语，因此考虑将核心元数据中的 `Author` 作为列出维护项目的人员的位置进行标准化。

In the end, though, the decision to adhere to the core metadata was deemed more important to help with the acceptance of this PEP, rather than trying to introduce a new interpretation for some of the core metadata.  
然而，最终决定坚持核心元数据被认为更有助于 PEP 的接受，而不是试图为某些核心元数据引入新的解释。

### [Support an arbitrary depth of tables for `project.entry-points`  支持 `project.entry-points` 的任意深度表格](#support-an-arbitrary-depth-of-tables-for-project-entry-points)
There was a worry that keeping `project.entry-points` to a depth of 1 for sub-tables would cause confusion to users if they use a dotted name and are not used to table names using quotation marks (e.g. `project.entry-points."spam.magical"`). But supporting an arbitrary depth – e.g. `project.entry-points.spam.magical` – would preclude any form of an exploded table format in the future. It would also complicate things for build back-ends as they would have to make sure to traverse the full table structure rather than a single level and raising errors as appropriate on value types.  
存在一个担忧，即如果子表将 `project.entry-points` 保持为 1 级深度，当用户使用点命名且不习惯使用引号（例如 `project.entry-points."spam.magical"` ）的表名时，可能会造成混淆。但支持任意深度——例如 `project.entry-points.spam.magical` ——将排除未来任何形式的展开表格式。这也会使构建后端复杂化，因为它们必须确保遍历完整的表结构而不是单级，并在适当的时候对值类型引发错误。

### [Using structured TOML dictionaries to specify dependencies  使用结构化 TOML 字典来指定依赖项](#using-structured-toml-dictionaries-to-specify-dependencies)
The format for specifying the dependencies of a project was the most hotly contested topic in terms of data format. It led to the creation of both [PEP 631](../pep-0631/ "PEP 631 – Dependency specification in pyproject.toml based on PEP 508") and [PEP 633](../pep-0633/ "PEP 633 – Dependency specification in pyproject.toml using an exploded TOML table") which represent what is in this PEP and using TOML dictionaries more extensively, respectively. The decision on those PEPs can be found at [https://discuss.python.org/t/how-to-specify-dependencies-pep-508-strings-or-a-table-in-toml/5243/38](https://discuss.python.org/t/how-to-specify-dependencies-pep-508-strings-or-a-table-in-toml/5243/38).  
指定项目依赖项的格式是关于数据格式的最具争议的话题。这导致了 PEP 631 和 PEP 633 的产生，它们分别代表了本 PEP 中的内容以及更广泛地使用 TOML 字典。关于这些 PEP 的决定可以在 https://discuss.python.org/t/how-to-specify-dependencies-pep-508-strings-or-a-table-in-toml/5243/38 找到。

The authors briefly considered supporting both formats, but decided that it would lead to confusion as people would need to be familiar with two formats instead of just one.  
作者们简要考虑过支持这两种格式，但决定这样做会导致混淆，因为人们需要熟悉两种格式而不是一种。

### [Require build back-ends to update `pyproject.toml` when generating an sdist  要求构建后端在生成 sdist 时更新 `pyproject.toml`](#require-build-back-ends-to-update-pyproject-toml-when-generating-an-sdist)
When this PEP was written, sdists did not require having static, canonical metadata like this PEP does. The idea was then considered to use this PEP as a way to get such metadata into sdists. In the end, though, the idea of updating `pyproject.toml` was not generally liked, and so the idea was rejected in favour of separately pursuing standardizing metadata in sdists.  
当这个 PEP 被撰写时，sdist（源分发包）并不需要像这个 PEP 所要求的这样静态的、标准的元数据。当时曾考虑使用这个 PEP 作为将此类元数据引入 sdist 的方式。然而，最终更新 `pyproject.toml` 的想法并未被普遍接受，因此这个想法被放弃，转而分别追求在 sdist 中标准化元数据。

### [Allow tools to add/extend data  允许工具添加/扩展数据](#allow-tools-to-add-extend-data)
In an earlier version of this PEP, tools were allowed to extend data for fields. For instance, build back-ends could take the version number and add a local version for when they built the wheel. Tools could also add more trove classifiers for things like the license or supported Python versions.  
在这个 PEP 的早期版本中，允许工具扩展字段的元数据。例如，构建后端可以获取版本号，并在构建轮（wheel）时添加本地版本号。工具还可以为许可证或支持的 Python 版本等添加更多的 trove 分类器。

In the end, though, it was thought better to start out stricter and contemplate loosening how static the data could be considered based on real-world usage.  
然而，最终认为从严格开始，并根据实际使用情况考虑放宽数据静态性的想法更为妥当。

## [Open Issues  开放问题](#open-issues)

None at the moment.  
目前没有。

## [Copyright  版权](#copyright)

This document is placed in the public domain or under the CC0-1.0-Universal license, whichever is more permissive.  
本文件置于公共领域或 CC0-1.0-通用许可协议之下，以其中更宽松的为准。

---

Source: [https://github.com/python/peps/blob/main/peps/pep-0621.rst](https://github.com/python/peps/blob/main/peps/pep-0621.rst)  
来源：https://github.com/python/peps/blob/main/peps/pep-0621.rst

Last modified: [2025-02-01 08:55:40 GMT](https://github.com/python/peps/commits/main/peps/pep-0621.rst)  
最后修改时间：2025-02-01 08:55:40 GMT

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。