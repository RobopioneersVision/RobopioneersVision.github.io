---
title: C++ Standards Support in GCC - GNU Project.md
toc: true
date: 2021-12-22 09:25:00
---
# C++ Standards Support in GCC - GNU Project

**实时链接** [https://gcc.gnu.org/projects/cxx-status.html](https://gcc.gnu.org/projects/cxx-status.html)

下述内容采集于 January 12, 2021 

---

GCC supports different dialects of C++, corresponding to the multiple published ISO standards. Which standard it implements can be selected using the `-std=` command-line option.

For information about the status of C++ defect reports, please see [this page](https://gcc.gnu.org/projects/cxx-dr-status.html).

For information about the status of the library implementation, please see the  [Implementation Status](https://gcc.gnu.org/onlinedocs/libstdc++/manual/status.html) section of the Libstdc++ manual.

## C++20 Support in GCC

GCC has experimental support for the latest revision of the C++ standard, which was published in 2020.

C++20 features are available since GCC 8. To enable C++20 support, add the command-line parameter `-std=c++20` (use `-std=c++2a` in GCC 9 and earlier) to your `g++` command line. Or, to enable GNU extensions in addition to C++20 features, add `-std=gnu++20`.

**Important**: Because the ISO C++20 standard is very recent, GCC's support is **experimental**.

## C++20 Language Features

The following table lists new language features that have been accepted into the C++20 working draft. The "Proposal" column provides a link to the ISO C++ committee proposal that describes the feature, while the "Available in GCC?" column indicates the first version of GCC that contains an implementation of this feature (if it has been implemented).

## C++17 Support in GCC

GCC has almost full support for the previous revision of the C++ standard, which was published in 2017. Some library features are missing or incomplete, as described in [the library documentation](https://gcc.gnu.org/onlinedocs/libstdc++/manual/status.html#status.iso.2017).

C++17 features are available since GCC 5. This mode is the default in GCC 11; it can be explicitly selected with the `-std=c++17` command-line flag, or `-std=gnu++17` to enable GNU extensions as well.

## C++17 Language Features

The following table lists new language features that have been accepted into the C++17 working draft. The "Proposal" column provides a link to the ISO C++ committee proposal that describes the feature, while the "Available in GCC?" column indicates the first version of GCC that contains an implementation of this feature (if it has been implemented).

## Technical Specifications

GCC also implements experimental support for some language Technical Specifications published by the C++ committee.

**Important**: Because these Technical Specifications are still evolving toward future inclusion in a C++ standard, GCC's support is **experimental**. No attempt will be made to maintain backward compatibility with implementations of features that do not reflect the final standard.

## C++14 Support in GCC

GCC has full support for the of the 2014 C++ standard.

This mode is the default in GCC 6.1 up until GCC 10 (including); it can be explicitly selected with the `-std=c++14` command-line flag, or `-std=gnu++14` to enable GNU extensions as well.

## C++14 Language Features

The following table lists new language features that are part of the C++14 standard. The "Proposal" column provides a link to the ISO C++ committee proposal that describes the feature, while the "Available in GCC?" column indicates the first version of GCC that contains an implementation of this feature.

[Untitled](C++ Standards Support in GCC - GNU Project/Untitled Database1.csv)

This feature was briefly part of the C++14 working paper, but was not part of the published standard; as a result, it has been removed from the compiler.

## C++11 Support in GCC

GCC 4.8.1 was the first feature-complete implementation of the 2011 C++ standard, previously known as C++0x.

This mode can be selected with the `-std=c++11` command-line flag, or `-std=gnu++11` to enable GNU extensions as well.

For information about C++11 support in a specific version of GCC, please see:

[Untitled](C++ Standards Support in GCC - GNU Project/Untitled Database2.csv)

## C++98 Support in GCC

GCC has full support for the 1998 C++ standard as modified by the 2003 technical corrigendum and some later defect reports, excluding the `export` feature which was later removed from the language.

This mode is the default in GCC versions prior to 6.1; it can be explicitly selected with the `-std=c++98` command-line flag, or `-std=gnu++98` to enable GNU extensions as well.