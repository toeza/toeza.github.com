---
layout: post

title:  The Compiler
date:   2016-11-25 15:50:00 +0800

categories: OS.X
tag: [编程]

---

* content
{:toc}


gcc 编译器支持多种语言.C 语言是典型的 Mac OS X 开发中的一种. 源文件的拓展名通常会告诉编译器它使用的是哪一种语言:

<table>
<tr> <td>.c </td> <td>Regular C</td> </tr>
<tr> <td>.m </td> <td>Objective-C</td> </tr>
<tr> <td>.C </td> <td>C++ (but do not use on HFS+ file systems since it is not case sensitive)</td> </tr>
<tr> <td>.cpp .cc </td> <td>C++</td> </tr>
<tr> <td>.mm </td> <td>Objective-C++, a blend of C++ and Objective-C</td> </tr>
</table>


# Handy Flags #

gcc 非常的庞大，带有很多的选项，功能，标示，拓展。也有很多很酷的东西。你可以在 XCode 中控制这些设置，但并不是所有的gcc设置都有GUI的显示。

下面是一些命令行的标示：

<table>

<tr>
<td style="width:20px"> -g </td>
<td> Add debugging symbols. Turn this on using the Level of Debug Symbols in Xcode’s Build tab in the Project or Target info panel. </td>
</tr>

<tr>
<td> -E </td>
<td> See preprocessor output. You can tell Xcode to preprocess a file by using the
Preprocess menu item in the contextual menu in the Xcode￿3 code editor. This
menu item also lives in the Build menu. As of this writing, Xcode￿4 has moved
it to the Generated￿Output portion of the assistant. </td>
</tr>

<tr>
<td> -S </td>
<td> See generated assembly code. </td>
</tr>

<tr>
<td> -save-temps </td>
<td> Keep temporary files around. </td>
</tr>

<tr>
<td> -Wall, -Wmost </td>
<td> Show more warnings. </td>
</tr>

<tr>
<td> -Werror </td>
<td> Treat warnings as errors. Turn this on by checking Treat￿Warnings￿as￿Errors in
Xcode’s Build tab in the Project or Target info panel. </td>
</tr>

<tr>
<td> -DSYMBOL </td>
<td> #define from the command line. </td>
</tr>

<tr>
<td> -DSYMBOL=value </td>
<td> #define with a value from the command line. You can tweak the macro
settings by editing Preprocessor￿Macros in Xcode’s Build tab in the Project or
Target info panel. </td>
</tr>

<tr>
<td> -O# </td>
<td> Set optimization levels (described below). Use the Optimization￿Level setting in
the Project or Target info panel’s Build tab to control this in Xcode. </td>
</tr>

<tr>
<td> -std </td>
<td> Choose a standard, or non-standard, C dialect. Supplying -std=c99 will give
you C99, which adds several gcc extensions and C++ features to the language
but disallows any additional gcc extensions. You can also use -std=gnu99 to get
both C99 and gcc’s extensions. </td>
</tr>

</table>

## Debugging ##

1.	The -g flag enables debugging symbols

2.	包含信息有：类型，变量名，数据结构

3.	苹果开发工具支持两种不同的 debug symbol 信息格式 Stabs 和 DWARF.  
Stabs 是旧版 Xcodes 中默认的, DWARF Xcode3 之后的版本默认的.


## Warnings ##

1.	Compiler warnings are a good thing.


	<table>

	<tr>
	<td style="width:20%"> -Wall will </td>
	<td> show a lot of warnings for most everything the gcc developers consider questionable </td>
	</tr>

	<tr>
	<td> -Wmost </td>
	<td> a good middle ground when using the Cocoa frameworks </td>
	</tr>

	<tr>
	<td> -Wno-unused </td>
	<td> No warnings for unused variables </td>
	</tr>

	</table>

2.	如果你想忽略某个文件的警告，但又不想关掉所有的警告。你可以在 XCode 为单个文件加入 compiler flag。

	>For Xcode￿3, select the source file in the Compile Sources phase of the target section of your Xcode project, choose Get￿Info, and then add your compiler flags to the build tab of the info window. The important thing to remember is that you set this deep in your target, not on your source file up in your source file Xcode groups.  

	<div style="hiden"></div>

	>Xcode￿4 has moved this feature to the Build￿Phases tab for your target. Expand Compile￿Sources and enter the compiler flags for the source file.


# Seeing Preprocessor Output #

The -E flag tells gcc to send the preprocessed source code to standard out. You can use Xcode3’s Preprocess command as well, or Xcode4’s generated output.


