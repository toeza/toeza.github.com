---
layout: post

title:  Predefined symbols 预编译标签
date:   2016-11-24 11:53:00 +0800

categories: OS.X
tag: [编程]

---

* content
{:toc}



查看预编译标签可以使用如下命令行:

	% gcc -E -dM - < /dev/null

The -E flag tells gcc to display preprocessor output, -dM is a gcc debugging flag that dumps out symbols at some point during the compilation process, and - < /dev/null tells gcc to expect its program text from standard in and feeds it /dev/null to indicate there is actually no text coming in.

    #define __DBL_MIN_EXP__ 	(-1021)
    #define __FLT_MIN__ 		1.17549435e-38F
    #define __CHAR_BIT__ 		8
    #define __WCHAR_MAX__ 		2147483647
    #define __DBL_DENORM_MIN__ 	4.9406564584124654e-324
    #define __LITTLE_ENDIAN__ 	1
    ...
    #define __APPLE__ 			1
    #define __GNUC__ 			4
    #define __MMX__ 			1
    #define OBJC_NEW_PROPERTIES 1
    #define __GXX_ABI_VERSION 	1002


You can feed the same arguments to **clang** to see what symbols Apple’s new compiler defines automatically.

Some of the symbols are pretty esoteric, and some can be very useful. Some of the more useful ones include:

    __APPLE__ 			Defined for an Apple platform, such as OS X.

    __APPLE_CC__ 		This is an integer value representing the version of the compiler.

    __OBJC__ 			Defined if the compiler is compiling in Objective-C mode.

    __cplusplus 		Defined if the compiler is compiling in C++ mode.

    __MACH__ 			Defined if the Mach system calls are available.

    __LITTLE_ENDIAN__ 	Defined if you are compiling for a little endian processor, like Intel or ARM.

    __BIG_ENDIAN__ 		Defined if you are compiling for a big endian processor, like PowerPC.

    __LP64__ 			Defined if you are compiling in 64-bit mode.

    There are also some built-in preprocessor symbols that have varying values:
    __DATE__ 		The current date, as a char *.
    __TIME__ 		The current time, as a char *.
    __FILE__ 		The name of the file currently being compiled, as a char *.
    __LINE__ 		The line number of the file before preprocessing, as an int.

    __func__ 			The name of the function or Objective-C method being compiled, as a char *. This is not actually a preprocessor feature but something that comes from the compiler. Remember that the preprocessor does not know anything about the languages of the files it processes.

    __FUNCTION__ 		Equivalent to __func__ and available in older versions of gcc where __func__ is not. With modern Mac or iOS programming, they are
    interchangeable.

    __PRETTY_FUNCTION__ The name of the function as a char *, as with __func__, but it includes type information.



These do not appear when you ask the compiler to display all of the **built-in macros** because they are being **expanded by the compiler**, not the preprocessor. 