---
layout: post

title:  C and Objective-C
date:   2016-11-23 17:22:00 +0800

categories: OS.X
tag: [编程]

---

* content
{:toc}


However, the primary application programming interfaces (APIs) from Apple are designed to work in plain C and Objective-C.

The Unix
API is C-based, while Cocoa and Cocoa Touch are Objective-C based.


# C #


## The Compiler pipeline (编译输出管道) ##

	1.	C 是编译语言，不是脚本语言。
	2.	源文件 -> 编译 -> 二进制文件 -> 链接 -> 系统库文件，框架 -> 可执行文件


如图:

![Figure 1.1]({{ '/styles/images/Post/Figure1_1_CompilerPipeline.png' | prepend: site.baseurl }} "The compiler pipeline")


## The C preprocessor ##

1.	replacing specific text with other text
2.	macros with arguments
3.	including text from other files
4.	conditionally including or excluding text

	C 预编译器只是单纯的做文本操作。

>It has no knowledge of the C language, so you can happily use the preprocessor to create a real mess or to come up with some cool hacks. (Sometimes the two are indistinguishable.)  


### Preprocessor symbols (预编译) ###

1.	`#define`

	    #define SOME_SYMBOL 23
    	#define ANOTHER_SYMBOL
    	#define MACRO(x) doSomethingCoolWith(x)

2.	You can also define macros in Xcode projects by including a space-separated list of definitions in the **Preprocessor Macros build setting**.


### Stringization and concatenation ###

		#define FIVE 5
		#define PRINT_EXPR(x) printf("%s = %d\n", #x, (x))
			PRINT_EXPR (5);
			PRINT_EXPR (5 * 10);
			PRINT_EXPR ((int)sqrt(FIVE*FIVE) + (int)log(25 / 5));
		#define SPLIT_FUNC(x,y) x##y
			SPLIT_FUNC (prin, tf) ("hello\n");

### Conditional compilation (条件编译) ###

		#ifdef DEFINED_NO_VALUE
			printf ("defined_no_value is defined\n");
		#else
			i can has syntax error;
		#endif

		#ifdef ZED
			printf ("zed is defined\n");
		#endif

		#if ZED
			printf ("zed evaluates to true\n");
		#else
			printf ("zed evaluates to false\n");
		#endif

		#if VERSION > 5 && VERSION < 20
			printf ("version is in the correct range.\n");
		#endif
		


### Predefined symbols (预定义) ###

查看预定义标签 [details]({{"/2016/11/24/Predefined-symbols/" | prepend:site.baseurl}}) 

	% gcc -E -dM - < /dev/null


[Read More]({{"/2016/11/24/Predefined-symbols/" | prepend:site.baseurl}})


### File inclusion (文件引入) ###

1.	`#include` 
2.	尖括号意味着被包含的文件为一个系统文件:`#include <stdio.h>`
3.	工程中的文件用双引号:`#include "ATMMachine.h"`
4.	被引入的文件也可能引入了其他的文件，因此很容易出现某一个文件被多次引入的问题。C 编译器不喜欢某个数据结构被声明多次。比如：

		#include <fcntl.h> // for open()
		#include <ulimit.h> // for ulimit()
		#include <pthread.h> // for pthread_create()
		#include <dirent.h> // for opendir()
		int main (void) {
			// nobody home
			return 0;
		} 

	![figure 1.2]({{ "/styles/images/Post/Figuer1_2_includes.png" | prepend:site.baseurl }})

5.	图中很多头文件在很多地方都被引用了，比如 `<sys/cdefs.h>`被引用了7次，如何防止多次引用产生的错误呢？如何保证头文件中的内容只被引用一次呢？

		#ifndef INCLUDE_GUARD_H
		#define INCLUDE_GUARD_H

			// Put the real header contents here.

		#endif // INCLUDE_GUARD_H

6.	该技术的不足之处：

	1.	在所有的头文件之中，保护标签必须是唯一的(guard symbol)
	2.	预编译程序必须要浏览一遍其他的所有的头文件，比如 图Figure1.2 中的 `<sys/cdefs.h>` 会被浏览7次，即使它只被使用一次

7.	`#pragma`
8.	在 Objective-C 中更方便，使用 `#import` 就可以保证该文件只被引入一次


### Macro hygiene (宏安全) ###

1.	如果定义了：`#define SQUARE(x) x*x` 则 `SQUARE(5)=5*5`。但是 `SQUARE(2+3)` 会被编译成 `2+3*2+3`。解决方法: 我们应该把宏定义写成: `#define SQUARE(x) (x)*(x)`

2.	更完善的写法应该是: `#define SQUARE(x) ((x)*(x))`

3.	`SQUARE(i++)` 会被替换成 `((i++)*(i++))`。不推荐这种用法！


### Multiline macros (占据多行的宏定义) ###

    #define FOUND_AN_ERROR(desc) \
    	error_count++; \
    	fprintf(stderr, "Found an error '%s' at file %s, line %d\n", \
    		desc, __FILE__, __LINE__)

该宏的使用方法：

    if (argc == 2) {
    	FOUND_AN_ERROR ("something really bad happened");
    }

该宏定义是有问题的：

1.	如果 `if` 语句中不加括号的话，比如：

		if (argc == 2) 
    		FOUND_AN_ERROR ("something really bad happened");

	那么 `fprintf` 语句是一定会被输出的！之前正确的代码现在会被认为是错误的。
	代码会变成：

		if (argc == 2)
			error_count++;

		fprintf(stderr, "Found an error '%s' at file %s, line %d\n",
		"something bad happened", "multilineMacro.m", 13);


2.	修改宏定义：

		#define FOUND_AN_ERROR(desc) \
			do { \
				error_count++; \
				fprintf(stderr, "Found an error '%s' at file %s, line %d\n", \
				desc, __FILE__, __LINE__); \
			} while (0)

	`while(0)` 意味着大括号中的代码仅仅只会被执行一次。但是还是有问题。

3.	如果宏定义中带有 `break` 或者 `continue` 关键字的话。break只会跳出宏定义中的循环。

		#define FOUND_AN_ERROR(desc) \
			do { \
				error_count++; \
				fprintf(stderr, "Found an error '%s' at file %s, line %d\n", \
				desc, __FILE__, __LINE__); \
				break; \
			} while (0)

		while (no_error) {
			if (x > 10) FOUND_AN_ERROR ("x too large");
			/* do something with x */
		}
	
	最终的解决方案：

		#define FOUND_AN_ERROR(desc) \
			if (1) { \
				error_count++; \
				fprintf(stderr, "Found an error '%s' at file %s, line %d\n", \
				desc, __FILE__, __LINE__); \
				break; \
			} else do {} while (0)


## Const and volatile variables (只读常量和变量) ##

const 是一个 C 的关键词 用来告诉编译器该变量无法被修改。

	const int i = 23;
	i = 24; // 错误: 无法为一个'只读'的变量 'i' 赋值

const 也适用于指针变量：

	// pointer to const char 		字符串：不可变
	const char *string = "bork"; 	// The data pointed to by string is const.
	string = "greeble"; 			// Pointer reassignment is ok.
	string[0] = 'f'; 				// error: assignment of read-only location

声明一个常量指针：

	// const pointer to char		指针：不可变
	char *const string2 = "bork"; 	// The pointer itself is const.
	string2 = "greeble"; 			// error: assignment of read-only variable 'string2'
	string2[0] = 'f'; 				// This is ok.


声明一个指向常量字符串的常量指针：

	// const pointer to const char		指针指：不可变；字符串：不可变
	const char * const string3 = "bork";// Pointer and pointee are const
	string3 = "greeble"; 				// error: assignment of read-only variable 'string3'
	string3[0] = 'f'; 					// error: assignment of read-only location

记忆方法：`const` 接在什么后面.如果接在一个数据类型后面，那么该数据就被认为是一个常量。 如果 `const` 跟着 `*` 后面, 那么该指针就被认为是一个指针常量。 	

`volatile` 正好与 `const` 相反。


## Variable argument lists (可变参数列表) ##

1.	`printf` 家族函数都是可变参数的函数。

2.	如何处理一个可变参数数量的函数

	1.	声明一个 `va_list` 类型的变量，`va_list` 的行为像是一个指针指向参数的值
	
	2.	`va_start()` 初始化 `va_list`，并传入最后声明的函数参数的名字，`va_list` 指向第一个额外的变量

	3.	`va_arg()` 调用带有数据类型的 `va_arg()` 获得你期望的参数，返回一个正确的该类型字节数，并且 `va_list` 指向下一个参数。

	4.	`va_end()` 保持调用 `va_arg()`，给它期望的类型，直到你已经处理完所有的参数。调用 `va_end()` 来清楚内部的状态。


	Example 1.7 vararg.m

    	// vararg.m -- use varargs to sum a list of numbers
    	// gcc -g -Wall -o vararg vararg.m

    	#import <stdio.h>
    	#import <stdarg.h>

    	// sum all the integers passed in. Stopping if it's zero
    	int addemUp (int firstNum, ...) {
    		va_list args;

    		int sum = firstNum;
    		int number;

    		va_start (args, firstNum);
    		while (1) {
    			number = va_arg (args, int);
    			sum += number;
    			if (number == 0) break;
    		}

    		va_end (args);
    		return sum;
    	} // addemUp

    	int main (int argc, char *argv[]) {
    		int sumbody;
    		sumbody = addemUp (1, 2, 3, 4, 5, 6, 7, 8, 9, 0);
   			printf ("sum of 1..9 is %d\n", sumbody);

    		sumbody = addemUp (1, 3, 5, 7, 9, 11, 0);
    		printf ("sum of odds from 1..11 is %d\n", sumbody);

    	return 0;
    	} // main


	![Figure 1.3 Variadic function stack usage]({{ "/styles/images/Post/Figure1_3_VariadicFunctionStackUsage.png" | prepend:site.baseurl }})


### Varargs gotchas (可变参数) ###

64位机的程序中 `size_t` 是8个字节，而 `int` 是4个字节，但是在32位机的程序中 `size_t` 和 `int` 都是4个字节。
错误代码：

	size_t mysize = somevalue;
	printf ("mysize is %d\n", mysize);

	修改方法：

	printf ("mysize is %d\n", (int)mysize); 或者是 printf ("mysize is %zu\n", mysize);


说明：Here, z is a length modifier that alters the unsigned conversion specifier u to match the size of the platform-dependent type size_t. Only a few such types have corresponding length modifiers; you can find the list in the printf(3) manpage.


### QuietLog ###

1.	`QuietLog()` 是一个像 `NSLog()` 一样的函数。但是它不打印一些额外的信息，比如 ProcessID ，当前时间等等。

2.	不能使用 `vprintf()` 来实现。因为 `vprintf()` 在打印一个对象的描述的时候不能识别 Cocoa 中的 `%@`。

3.	使用 `NSString` 创建模版，打印输出。

		void QuietLog (NSString *format, ...) {

			va_list argList;
			va_start (argList, format);

			// NSString luckily provides us with this handy method which
			// will do all the work for us, including handling %@

			NSString *string;
			string = [[NSString alloc] initWithFormat: format arguments: argList];
			va_end (argList);

			printf ("%s\n", [string UTF8String]);
			[string release];

		} // QuietLog

	使用：`QuietLog (@"QuietLog is %@", [NSNumber numberWithInt: 42]);`


### Variadic macros (可变宏) ###

1.	如果你无法确定宏中的参数，那么可以使用 `__VA_ARGS__` 标签来引用剩余的所有参数。记住这只是一个文本操作，进程中并不会创建 `va_list` 变量。

2.	不幸的是，如果你传入的是一个可选参数的时候，`__VA_ARGS__` 是有问题的。

		#define THING(string, ...) printf (string, __VA_ARGS__)

	如果是这样：

		THING ("hello %s %s\n", "there", "george");
		=>printf ("hello %s %s\n", "there", "george");

	则不会有问题，但是如果是这样：

		THING ("hi\n");
		=>printf ("hi\n", );

	显然，问题出现了

3.	使用 `##__VA_ARGS__` 则宏写成 `#define THING(string, ...) printf (string,## __VA_ARGS__)`

	那么 `THING ("hi\n");` => `printf ("hi\n");`，因为 `##` 操作将使预处理器(preprocessor)去除掉它前面的那个逗号。

4.	debuglog-macro

		#import <stdio.h>
		#import <stdarg.h>

		int globalLevel = 50;

		#define DEBUG_LOG(logLevel, format, ...) \
		do { \
			if ((logLevel) > globalLevel) printf((format), ##__VA_ARGS__); \
		} while (0)
	
	区分 DEBUG 和 RELEASE 的打印


## Bitwise operations ##

1.	Cocoa API 偶然会用到，Unix API 经常会用到。

2.	1 byte = 8 bits

	![Figure1_4_TwoBytes]( {{ "/styles/images/Post/Figure1_4_TwoBytes.png" | prepend:site.baseurl }} )


### Bitwise operators ###

1.	4中操作，分别是 AND, OR, XOR, and NOT

2.	AND

	![Figure1_5_BitwiseAND]({{"/styles/images/Post/Figure1_5_BitwiseAND.png" | prepend:site.baseurl}})


3.	OR

	![Figure1_6_BitwiseOR]({{ "/styles/images/Post/Figure1_6_BitwiseOR.png" | prepend:site.baseurl}})


4.	XOR

	![Figure1_7_BitwiseXOR]({{ "/styles/images/Post/Figure1_7_BitwiseXOR.png" | prepend:site.baseurl }})

5.	NOT

	![Figure1_8_BitwiseNOT]({{ "/styles/images/Post/Figure1_8_BitwiseNOT.png" | prepend:site.baseurl }})

6.	Bitewise shift

	![Figure1_9_BitwiseShift]({{ "/styles/images/Post/Figure1_9_BitwiseShift.png" |prepend:site.baseurl }})


### Setting and clearing bits ###

1.	枚举的应用

		enum {
			NSEnumerationConcurrent = (1UL << 0), 	// binary 0001
			NSEnumerationReverse = (1UL << 1), 		// binary 0010
		};

		typedef NSUInteger NSEnumerationOptions;

2.	“bitmasking”

		#define BIT_POSITION 8 			// 00001000
		#define BIT_POSITION (1 << 3) 	// 00001000

3.	使用 bitwise OR 设置 bitmask 的 bits  `flags |= BIT_POSITION;`

	![Figure1_10_SettingABit]({{ "/styles/images/Post/Figure1_10_SettingABit.png" | prepend:site.baseurl }})

4.	使用 bitwise AND 测试 bitmasks bits 是否是一个集合

		if (flags & BIT_POSITION) {
			// do something appropriate
		}

	![Figure1_11_TestingABit]({{ "/styles/images/Post/Figure1_11_TestingABit.png" | prepend:site.baseurl }})

5.	clear a bitmask’s bits

	![Figure1_12_ClearingABit]({{ "/styles/images/Post/Figure1_12_ClearingABit.png" | prepend:site.baseurl }})

正确的代码：

	if ((flags & MULTI_BITS) == MULTI_BITS) {
		// do something if all bits are set
	}

Example 1.13 bitmask.m

	// bitmask.m -- play with bitmasks
	// gcc -g -Wall -o bitmask bitmask.m

	#include <stdio.h> // for printf

	#define THING_1_MASK 1 // 00000001
	#define THING_2_MASK 2 // 00000010
	#define THING_3_MASK 4 // 00000100

	#define ALL_THINGS (THING_1_MASK | THING_2_MASK | THING_3_MASK) // 00000111

	#define ANOTHER_MASK 	(1 << 5) // 00100000
	#define ANOTHER_MASK_2 	(1 << 6) // 01000000

	#define ALL_ANOTHERS 	(ANOTHER_MASK | ANOTHER_MASK_2) // 01100000
	#define ALL_USEFUL_BITS (ALL_THINGS | ALL_ANOTHERS) 	// 01100111

	static void showMaskValue (int value) {

		printf ("\n"); // space out the output
		printf ("value %x:\n", value);

		if (value & THING_1_MASK) 			printf (" THING_1\n");
		if (value & THING_2_MASK) 			printf (" THING_2\n");
		if (value & THING_3_MASK) 			printf (" THING_3\n");
		if (value & ANOTHER_MASK) 			printf (" ANOTHER_MASK\n");
		if (value & ANOTHER_MASK_2) 		printf (" ANOTHER_MASK_2\n");
		if ((value & ALL_ANOTHERS) == ALL_ANOTHERS) printf (" ALL ANOTHERS\n");

	} // showMaskValue


	static int setBits (int value, int maskValue) {

		// Set a bit by just OR-ing in a value.
		value |= maskValue;
		return value;

	} // setBits


	static int clearBits (int value, int maskValue) {

		// To clear a bit, AND it with the complement of the mask.
		value &= ~maskValue;
		return value;

	} // clearBits


	int main (void) {

		int intval = 0;
		intval = setBits (intval, THING_1_MASK); // 00000001 = 0x01
		intval = setBits (intval, THING_3_MASK); // 00000101 = 0x05
		showMaskValue (intval);

		intval = setBits (intval, ALL_ANOTHERS); // 01100101 = 0x65
		intval = clearBits (intval, THING_2_MASK); // 01100101 = 0x65
		intval = clearBits (intval, THING_3_MASK); // 01100001 = 0x61
		showMaskValue (intval);

		return 0;

	} // main


# Objective-C #

Objective-C is the language used to program the Cocoa toolkit. It is a thin layer on top of C with a little additional syntax, a runtime component, and a big pile of metadata to support the dynamic nature of the language. But fundamentally, Objective-C is still C. Many programmers forget Objective-C’s C heritage and fail to take advantage of C’s language features and libraries.


## C callbacks in Objective-C ##


1.	A question：“How do I put a method into a function pointer?”

	“I’m using a C API that uses callbacks with Cocoa, how do I make it work?”

2.	虽然 Objective-C 是基于 C的函数，但是你不能使用 Objective-C 的方法来作为 C API 的回调。

3.	当你向一个对象发送消息的时候 => 编译器会替换为：`objc_msgSend()`

	`objc_msgSend()` 的原型是：`id objc_msgSend(id self, SEL op, ...);`

4.	`[box drawInRect: someRect]` => `objc_msgSend (box, @selector(drawInRect:), someRect);`

	`box`:消息接收器;`@selector()`:方法(`_cmd`);

5.	Core Foundation framework 提供一个 XML 解析器(XML parser) 就是以 C 的回调为基础的。

	1.	Set up your context to include your Objective-C object:

			Watcher *watcher = [[Watcher alloc] init];
			CFXMLParserContext context = { 0, watcher, NULL, NULL, NULL };
	
	2.	Set up your callbacks to include the C functions:

			CFXMLParserCallBacks callbacks = { 0, createStructure, addChild,
												endStructure, resolveEntity,
												handleError };

	3.	Create your parser (notice the passing of the context for the last parameter):

			parser = CFXMLParserCreate (kCFAllocatorDefault, (CFDataRef)xmlData, NULL,
										kCFXMLParserAllOptions,
										kCFXMLNodeCurrentVersion,
										&callbacks, &context);

	4.	The CFXMLParser will grovel through the XML and call the callbacks as needed. From there, you can cast the info pointer into an object pointer and call a method. For instance,

			void *createStructure (CFXMLParserRef parser,CFXMLNodeRef node, void *info) {
				
				Watcher *watcher = (Watcher *) info;
				[watcher watchCreateStructure: node];

				// ... do some work ...

				return newXMLStructureDealie;

			} // createStructure

	5.	Alternatively, you could create an NSInvocation or a block and use that for your context pointer. Then you can have a generic callback that just casts the incoming context pointer to an invocation or a block and then executes it.


## Objective-C 2.0 ##

Mac OS X 10.5 included a 2.0 update to the Objective-C language.


### Protocols (协议) ###

1.	Objective-C 协议分为：正式协议的和非正式协议

2.	例如：NSTableDataSource 有两个协议是必须实现的

		-numberOfRowsInTableView:
		-tableView:objectValueForTableColumn:row:

	非正式协议不会要求你来实现它们，所以 NSTableView 依赖于运行时来检查数据源确保它们都被实现了！

3.	Objective-C 2.0 提出了更好的解决方法：`@required` and `@optional`

	`@required` 要求声明的方法必须实现 ->默认
	`@optional` 声明的方法非必须实现


### Class extensions (类的拓展) ###

1.	categories

	1.	将又多又长的方法归类。例如：NSWindow-> NSKeyboardUI, NSToolbarSupport, NSDrag (categories)
	2.	用来声明私有方法“private”，实现文件的头部

2.	代码示例：

		@interface BigShow ()
			- (void) moveToNextSlide;
			- (int) currentSlideIndex;
		@end

	这段代码告诉编译器这些方法也是类 BigShow 的一部分，即使他们被书写在不同的地方。如果你没有在类的@implementation文件中提供他们的实现方法，编译器依然会发出警告。

3.	有些书也把 "class extensions" 称为 "class continuations"

4.	你也可以在类的拓展里使用协议

		@interface GroovyController : UIViewController <UITableViewDataSourceProtocol>
		@interface GroovyController () <UITableViewDataSourceProtocol>

5.	好处：通过将协议放在实现文件中，可以减少头文件中类的引用，这样可以减少编译时间


### Fast enumeration (快速枚举) ###

1.	容器的概念，迭代器的概念

2.	快速枚举的例子：

		NSEnumerator *enumerator = [array objectEnumerator];
		NSString *string;
		while ((string = [enumerator nextObject])) {
			NSLog (@"%@", string);
		}

3.	Objective-C 2.0 采用 “for-in” 语句

		for (NSString *string in array) {
			NSLog (@"%@", string);
		}

	也可以使用 NSEnumerator 来代替容器

		NSEnumerator *enumerator = [array objectEnumerator];
		for (NSString *string in enumerator) {
			NSLog (@"%@", string);
		}

4.	“for-in” 语句比容器的迭代方法速度是要快的！它采用了新的 NSFastEnumeration 协议,

5.	NSFastEnumeration 协议只有一个方法：

		- (NSUInteger) countByEnumeratingWithState: (NSFastEnumerationState *) state
										   objects: (id *) stackbuf
											 count: (NSUInteger) len;


	state 的数据类型：

		typedef struct {
			unsigned long 		state;
			id 				*	itemsPtr;
			unsigned long 	*	mutationsPtr;
			unsigned long 		extra[5];
		} NSFastEnumerationState;

	点此了解更多关于 NSFastEnumeration 的使用方法！[READE MORE ...][NSFastEnumeration]

[NSFastEnumeration]:<{{ "" | prepend:site.baseurl}}> "NSFastEnumeration"




