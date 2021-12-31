---
title: Clang - C++20, C++17, C++14, C++11 and C++98 Statu.md
toc: true
date: 2021-12-22 09:25:00
---
# Clang - C++20, C++17, C++14, C++11 and C++98 Status

**实时链接** [https://clang.llvm.org/cxx_status.html](https://clang.llvm.org/cxx_status.html)

下述内容采集自 January 12, 2021 

---

Clang fully implements all published ISO C++ standards ([C++98 / C++03](https://clang.llvm.org/cxx_status.html), [C++11](https://clang.llvm.org/cxx_status.html), [C++14](https://clang.llvm.org/cxx_status.html), and [C++17](https://clang.llvm.org/cxx_status.html)), and some of the upcoming [C++20](https://clang.llvm.org/cxx_status.html) standard.

The Clang community is continually striving to improve C++ standards compliance between releases by submitting and tracking [C++ Defect Reports](https://clang.llvm.org/cxx_dr_status.html) and implementing resolutions as they become available.

Experimental work is also under way to implement [C++ Technical Specifications](https://clang.llvm.org/cxx_status.html) that will help drive the future of the C++ programming language.

The [LLVM bug tracker](https://bugs.llvm.org/) contains Clang C++ components that track known bugs with Clang's language conformance in each language mode.

## C++98 implementation status

Clang implements all of the ISO C++ 1998 standard (including the defects addressed in the ISO C++ 2003 standard) except for export (which was removed in C++11).

## C++11 implementation status

Clang 3.3 and later implement all of the [ISO C++ 2011 standard](https://www.iso.org/standard/50372.html).

By default, Clang builds C++ code according to the C++98 standard, with many C++11 features accepted as extensions. You can use Clang in C++11 mode with the `-std=c++11` option. Clang's C++11 mode can be used with [libc++](https://libcxx.llvm.org/) or with gcc's libstdc++.

## C++14 implementation status

Clang 3.4 and later implement all of the [ISO C++ 2014 standard](https://www.iso.org/standard/64029.html).

You can use Clang in C++14 mode with the `-std=c++14` option (use `-std=c++1y` in Clang 3.4 and earlier).

## C++17 implementation status

Clang 5 and later implement all the features of the [ISO C++ 2017 standard](https://www.iso.org/standard/68564.html).

You can use Clang in C++17 mode with the `-std=c++17` option (use `-std=c++1z` in Clang 4 and earlier).

## C++20 implementation status

Clang has support for some of the features of the ISO C++ 2020 Draft International Standard.

You can use Clang in C++20 mode with the `-std=c++20` option (use `-std=c++2a` in Clang 9 and earlier).

## Defect reports

Clang generally aims to implement resolutions to Defect Reports (bug fixes against prior standards) retroactively, in all prior standard versions where the fix is meaningful. Significant Defect Report changes to language features after the publication of the relevant standard are marked (DR) in the above table.

Clang also has a test suite for conformance to resolutions for issues on the [C++ core issues list](http://www.open-std.org/jtc1/sc22/wg21/docs/cwg_toc.html), most of which are considered Defect Reports. [Implementation status for C++ core issues](https://clang.llvm.org/cxx_dr_status.html) based on that test suite is tracked on a separate page.

## Technical specifications and standing documents

ISO C++ also publishes a number of documents describing additional language and library features that are not part of standard C++.