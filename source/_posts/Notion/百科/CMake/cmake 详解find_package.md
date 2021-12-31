---
title: cmake 详解find_package.md
toc: true
date: 2021-12-22 09:25:00
---
# cmake : 详解find_package

[https://blog.csdn.net/LaineGates/article/details/108247128](https://blog.csdn.net/LaineGates/article/details/108247128)

参考[cmake官方文档](https://cmake.org/cmake/help/latest/command/find_package.html)`find_package()`有两种用法：`Basic Signature and Module Mode`和`Full Signature and Config Mode`。
 前者在cmake自带的`Modules`目录下查找`Find<PackageName>.cmake`
 后者在所有搜索目录里找`<PackageName>Config.cmake`或者`<PackageName>-config.cmake`。

# Basic Mode

```
find_package(<PackageName>  # package名，如Boost
			[version] 		# 版本号，如1.1.1.1，最长为4
			[EXACT] 		# 版本号必须完全匹配才算找到
			[QUIET] 		# 查找时不输出辅助信息
			[MODULE]		# 在CMAKE_MODULE_PATH找"Find<PackageName>.cmake"
            [REQUIRED]      # 必须找到库，否则停止cmake运行
            [[COMPONENTS] [components...]] # 查找子模块，暂时没找到怎么设置
            [OPTIONAL_COMPONENTS components...] # 备选模块
            [NO_POLICY_SCOPE]
            )

```

# Config Mode

```
find_package(<PackageName> 
			[version] 
			[EXACT] 
			[QUIET]
            [REQUIRED] 
            [[COMPONENTS] [components...]]
            [OPTIONAL_COMPONENTS components...]
            [CONFIG|NO_MODULE]
            [NO_POLICY_SCOPE]
            [NAMES name1 [name2 ...]]
            [CONFIGS config1 [config2 ...]]
            [HINTS path1 [path2 ... ]]
            [PATHS path1 [path2 ... ]]
            [PATH_SUFFIXES suffix1 [suffix2 ...]]
            [NO_DEFAULT_PATH]
            [NO_PACKAGE_ROOT_PATH]
            [NO_CMAKE_PATH]
            [NO_CMAKE_ENVIRONMENT_PATH]
            [NO_SYSTEM_ENVIRONMENT_PATH]
            [NO_CMAKE_PACKAGE_REGISTRY]
            [NO_CMAKE_BUILDS_PATH] # Deprecated; does nothing.
            [NO_CMAKE_SYSTEM_PATH]
            [NO_CMAKE_SYSTEM_PACKAGE_REGISTRY]
            [CMAKE_FIND_ROOT_PATH_BOTH |
              	ONLY_CMAKE_FIND_ROOT_PATH |
              	NO_CMAKE_FIND_ROOT_PATH]
              )

```

与basic mode类似，此处只讲不同的。

- `CONFIG`模式之前没用过，记录原文。Config mode search attempts to locate a configuration file provided by the package to be found. A cache entry called _DIR is created to hold the directory containing the file. By default the command searches for a package with the name . If the NAMES option is given the names following it are used instead of . The command searches for a file called Config.cmake or -config.cmake for each name specified. A replacement set of possible configuration file names may be given using the CONFIGS option. The search procedure is specified below. Once found, the configuration file is read and processed by CMake. Since the file is provided by the package it already knows the location of package contents. The full path to the configuration file is stored in the cmake variable _CONFIG.

# 获取版本信息

cmake会在`<config-file>.cmake`目录，查找`<config-file>-version.cmake` 或 `<config-file>Version.cmake`。
 如果查找失败，任何版本检查都会失败。
 如果查找成功，以下变量会被设置：`<PackageName>_VERSION<PackageName>_VERSION_MAJOR<PackageName>_VERSION_MINOR<PackageName>_VERSION_PATCH<PackageName>_VERSION_TWEAK<PackageName>_VERSION_COUNT`