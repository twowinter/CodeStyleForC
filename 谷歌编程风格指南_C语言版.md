
## 一、头文件

通常，每一个.cc文件（ C++的源文件）都有一个对应的.h文件（头文件），也有一些例外，如单元测试代码和只包含main()的.cc文件。
正确使用头文件可令代码在可读性、文件大小和性能上大为改观。
下面的规则将引导你规避使用头文件时的各种麻烦。

### 1.自包含头文件(感觉这部分得好好消化下，目前没思路)

头文件应该自包含(即自需要编译他们自己)，以及以.h结尾。非头文件想要被包含的话，应该以.inc结尾。

所有的头文件都应该自包含。用户和重构工具不应该遵从特殊条件去包含头文件。特别地，一个头文件应该有头部保护，包含它需要的所有头文件。

如果一个模版或者内联函数在.h文件中做了声明，相同的头文件就会提供它的定义。这些定义会被包含到使用它的.c文件中，否则程序就会连接失败。不要把定义分开到

### 2.#define的保护

所有头文件都应该使用#define防止头文件被多重包含（ multipleinclusion） ，命名格式应该是：<PROJECT>_<PATH>_<FILE>_H_

为保证唯一性，头文件的命名应基于所在项目源码树的全路径。例如，项目foo中的头文件foo/src/bar/baz.h，应该按如下方式保护：

	#ifndef FOO_BAR_BAZ_H_
	#define FOO_BAR_BAZ_H_
	...
	#endif //FOO_BAR_BAZ_H_
	
	
### 3. 前置声明(C++专有)

### 4. 内联函数(C++专有)

### 5. -inl.h文件(C++专有)

### 6. 包含文件的名称及次序
将包含次序标准化可增强可读性、避免隐藏依赖，次序如下：关联头文件、C库、C++库、其他库的.h、项目内的.h。

项目内头文件应按照项目源代码目录树结构排列，并且避免使用UNIX文件路径.（当前目录）和..（父目录）。例如， google-awesome-project/src/base/logging.h应像这样被包含：

	#include "base/logging.h"
	
dir/foo.cc 的主要作用是执行或测试dir2/foo2.h的功能，foo.cc中包含头文件的次序如下：

	1.dir2/foo2.h
	2.C系统文件
	3.C++系统文件
	4.其他库头文件
	5.本项目内头文件
	
这种排序方式可有效减少隐藏依赖，如果 dir2/foo2.h 遗漏了任何必要的包含，那编译 dir/foo.cc 时就会中断。这个规则保证了编译中断会率先出现在那些处理当前文件的人面前，而不是别的包中的无关的人。

dir/foo.cc和dir2/foo2.h通常位于相同目录下（像 base/basictypes_unittest.cc和 base/basictypes.h），但也可在不同目录下。

相同目录下头文件按字母序是不错的选择。

举例来说，google-awesome-project/src/foo/internal/fooserver.cc的包含次序如下：


	#include "foo/public/fooserver.h"

	#include <sys/types.h>
	#include <unistd.h>

	#include <hash_map>
	#include <vector>

	#include "base/basictypes.h"
	#include "base/commandlineflags.h"
	#include "foo/public/bar.h



## 二、作用域

名称污染。本节大部分是C++的知识，对于C，可以借鉴的是这个名词“名称污染”。就像模块内部的变量最好用结构体括起来，防止污染。

## 三、类

## 四、函数
### 1. 参数顺序

### 2. 编写短小函数
记住一点，编写短小函数。

## 五、Google特有的风情

## 六、其他C++特性

### sizeof
尽可能用sizeof(varname)代替sizeof(type)。
使用sizeof(varname)是因为当变量类型改变时代码自动同步，有些情况下sizeof(type)或许有意义，还是要尽量避免，如果变量类型改变的话不能同步。

	Struct data;
	memset(&data, 0, sizeof(data));
	memset(&data, 0, sizeof(Struct));// 不好的方式

## 七、命名
重要的一致性规则是命名管理，命名风格直接可以直接确定命名实体是：类型、变量、函数、常量、宏等等，无需查找实体声明，我们大脑中的模式匹配引擎依赖于这些命名规则。命名规则具有一定随意性，但相比按个人喜好命名，一致性更重要，所以不管你怎么想，规则总归是规则。

### 1. 通用命名规则
函数命名、变量命名、文件命名应具有描述性，不要过度缩写。
尽可能给出描述性名称，不要节约空间，让别人很快理解你的代码更重要，不要使用模糊的缩写或随意的字符。

	int price_count_reader;    // No abbreviation.
	int num_errors;            // "num" is a widespread convention.
	int num_dns_connections;   // Most people know what "DNS" stands for.


	int n;                     // Meaningless.
	int nerr;                  // Ambiguous abbreviation.
	int n_comp_conns;          // Ambiguous abbreviation.
	int wgc_connections;       // Only your group knows what this stands for.
	int pc_reader;             // Lots of things can be abbreviated "pc".
	int cstmr_id;              // Deletes internal letters.

### 2. 文件命名
文件名要全部小写，可以包含下划线（\_）或短线（-），按项目约定来，如果没有的话最好用"\_"。
可接受的文件命名:  

- my_useful_class.cc
- my-useful-class.cc
- myusefulclass.cc
- myusefulclass_test.cc // _unittest and _regtest are deprecated.

C++文件以.cc结尾，头文件以.h结尾。  
不要使用已经存在于/usr/include下的文件名，如db.h。  
通常，尽量让文件名更加明确，http_server_logs.h就比logs.h要好，定义类时文件名一般成对出现，如foo_bar.h和foo_bar.cc，对应类FooBar。  


### 3. 类型命名
类型命名每个单词以大写字母开头，不包含下划线： MyExcitingClass、 MyExcitingEnum。  
所有类型命名——类、结构体、类型定义（ typedef）、枚举——使用相同约定，例如：  

	// classes and structs
	class UrlTable { ...
	class UrlTableTester { ...
	struct UrlTableProperties { ...
	
	// typedefs
	typedef hash_map<UrlTableProperties *, string> PropertiesMap;
	
	// enums
	enum UrlTableErrors { ..
	
	
### 4. 变量命名
变量名一律小写，单词间以下划线相连，类的成员变量以下划线结尾，如my\_exciting\_local_variable、 my\_exciting\_member\_variable\_。  

**普通变量命名：**  
举例：  

	string table_name; // OK - uses underscore.
	string tablename; // OK - all lowercase.
	string tableName; // Bad - mixed case.
	
**全局变量：**  
对全局变量没有特别要求，少用就好，可以以g_或其他易与局部变量区分的标志为前缀。

### 5. 常量命名
在名称前加k： kDaysInAWeek。  
所有编译时常量（无论是局部的、全局的还是类中的）和其他变量保持些许区别，k后接大写字母开头的单词：  

	const int kDaysInAWeek = 7
	
### 6. 函数命名  

函数名以大写字母开头，每个单词首字母大写(这是大驼峰命名法或者叫帕斯卡命名法)。单词间没有下划线。如果有大写字母缩写词，倾向于把它当作单个词(例如 StartRpc()而不是 StartRPC())：

	AddTableEntry()
	DeleteUrl()
	OpenFileOrDie()
	
### 7. 枚举命名

枚举值可以像常量那样，也可以像宏那样，kEnumName 或者 ENUM_NAME。  
独立枚举值倾向于像常量那样命名，但是也接受像宏那样命名。像UrlTableErrors这样的枚举名称属于类型，因此大小写混合。

	enum UrlTableErrors {
		kOK = 0,
		kErrorOutOfMemory,
		kErrorMalformedInput,
	};
	enum AlternateUrlTableErrors {
		OK = 0,
		OUT_OF_MEMORY = 1,
		MALFORMED_INPUT = 2,
	};

从2009年1月之前，枚举值的风格都和宏一样。这样比较容易导致命名冲突，因此改为推荐常量枚举命名风格。如果可以的话，新代码尽量使用常量风格来命名。没必要去把老代码都改为常量风格，除非引起了编译错误。

### 8. 宏命名

你并不打算使用宏，对吧？如果使用，像这样： 'MY_MACRO_THAT_SCARES_SMALL_CHILDREN。'  
参考第四篇预处理宏， 通常是不使用宏的， 如果绝对要用， 其命名像枚举命名一样全部大写、
使用下划线：  

	#define ROUND(x) ...
	#define PI_ROUNDED 3.0

	
## 八、注释


注释虽然写起来很痛苦，但对保证代码可读性至为重要，下面的规则描述了应该注释什么、注释在哪儿。当然也要记住，注释的确很重要，但最好的代码本身就是文档（ selfdocumenting） ，类型和变量命名意义明确要比通过注释解释模糊的命名好得多。注释是为别人 （下一个需要理解你的代码的人） 而写的， 认真点吧， 那下一个人可能就是你！

### 1. 注释风格

使用//或/* */，统一就好。

//或/* */都可以， //只是用的更加广泛，在如何注释和注释风格上确保统一。


### 2. 文件注释

在每一个文件开头加入版权公告。  

文件注释描述了文件的内容。基本所有的文件都应该要有文件注释：  

#### 许可和作者

每个文件都应该有许可证，为项目选择合适的许可证版本，如Apache 2.0、BSD、 LGPL、 GPL；
如果你为一个文件做出巨大改变，可以加上自己名字。

#### 文件内容：  

通常，.h文件要对所声明的类的功能和用法作简单说明，.cc文件包含了更多的实现细节或算法讨论，如果你感觉这些实现细节或算法讨论对于阅读有帮助，可以把.cc中的注释放到.h中，并在.cc中指出文档在.h中。

不要单纯在.h和.cc间复制注释，复制的注释偏离了实际意义。


### 3. 函数注释

**函数声明：**
注释于声明之前，描述函数功能及用法，注释使用描述式（ "Opens the file"）而非指令式（ "Open the file"）；注释只是为了描述函数而不是告诉函数做什么。通常，注释不会描述函数如何实现，那是定义部分的事情。   

函数声明处注释的内容：  
1) inputs（输入） 及 outputs（输出） ；  
2) 对类成员函数而言：函数调用期间对象是否需要保持引用参数，是否会释放这些参数；  
3) 如果函数分配了空间，需要由调用者释放；  
4) 参数是否可以为NULL；  
5) 是否存在函数使用的性能隐忧（ performance implications） ；  
6) 如果函数是可重入的（ re-entrant） ，其同步前提（ synchronization assumptions）是什么？  

举例如下：  

	// Returns an iterator for this table.  It is the client's
	// responsibility to delete the iterator when it is done with it,
	// and it must not use the iterator once the GargantuanTable object
	// on which the iterator was created has been deleted.
	//
	// The iterator is initially positioned at the beginning of the table.
	//
	// This method is equivalent to:
	//    Iterator* iter = table->NewIterator();
	//    iter->Seek("");
	//    return iter;
	// If you are going to immediately seek to another place in the
	// returned iterator, it will be faster to use NewIterator()
	// and avoid the extra seek.
	Iterator* GetIterator() const;
	
但不要有无谓冗余或显而易见的注释，下面的注释就没有必要加上“returns false otherwise”，因为已经暗含其中了：  

	// Returns true if the table cannot hold any more entries.
	bool IsTableFull();


**函数定义：**  

每个函数定义时要以注释说明函数功能和实现要点，如使用的漂亮代码、实现的简要步骤、如此实现的理由、为什么前半部分要加锁而后半部分不需要。不要从.h文件或其他地方的函数声明处直接复制注释，简要说明函数功能是可以的，但重点要放在如何实现上。  

### 4. 变量注释   

通常变量名本身足以很好说明变量用途，特定情况下，需要额外注释说明。  

**全局变量（常量）：**  

和数据成员相似，所有全局变量（常量）也应注释说明含义及用途，如：

	// The total number of tests cases that we run through in this regression test.
	const int kNumTestCases = 6;
	
### 6. 实现注释
对于实现代码中巧妙的、晦涩的、有趣的、重要的地方加以注释。  

**代码前注释：**  

出彩的或复杂的代码块前要加注释，如：

	// Divide result by two, taking into account that x
	// contains the carry from the add.
	for (int i = 0; i < result->size(); i++) {
	x
	= (x << 8) + (*result)[i];
	(*result)[i] = x >> 1;
	x &= 1;
	}
	
**行注释：**  

比较隐晦的地方要在行尾加入注释，可以在代码之后空两格加行尾注释，如：  

	// If we have enough memory, mmap the data portion too.
	mmap_budget = max<int64>(0, mmap_budget - index_->length());
	if (mmap_budget >= data_size_ && !MmapData(mmap_chunk_bytes, mlock))
	  return;  // Error already logged.

注意，有两块注释描述这段代码，当函数返回时注释提及错误已经被记入日志。  
前后相邻几行都有注释，可以适当调整使之可读性更好：  

	DoSomething();                  // Comment here so the comments line up.
	DoSomethingElseThatIsLonger();  // Two spaces between the code and the comment.
	{ // One space before comment when opening a new scope is allowed,
	  // thus the comment lines up with the following comments and code.
	  DoSomethingElse();  // Two spaces before line comments normally.
	}
	vector<string> list{// Comments in braced lists describe the next element ..
						"First item",
						// .. and should be aligned appropriately.
						"Second item"};
	DoSomething(); /* For trailing block comments, one space is fine. */


### 7. 标点、拼写和语法

留意标点、拼写和语法，写的好的注释比差的要易读的多。  

注释一般是包含适当大写和句点（ .）的完整的句子，短一点的注释（如代码行尾的注释）可以随意点， 依然要注意风格的一致性。 完整的句子可读性更好， 也可以说明该注释是完整的而不是一点不成熟的想法。  

虽然被别人指出该用分号（ semicolon） 的时候用了逗号（ comma） 有点尴尬。清晰易读的代码还是很重要的，适当的标点、拼写和语法对此会有所帮助。

### 8. TODO注释

对那些临时的、短期的解决方案，或已经够好但并不完美的代码使用TODO注释。  

这样的注释要使用全大写的字符串TODO，后面括号（ parentheses） 里加上你的大名、邮件地址等，还可以加上冒号（ colon）：目的是可以根据统一的TODO格式进行查找：

	// TODO(kl@gmail.com): Use a "*" here for concatenation operator.
	// TODO(Zeke) change this to use relations.
	// TODO(bug 12345): remove the "Last visitors" feature

如果加上是为了在“将来某一天做某事”，可以加上一个特定的时间（ "Fix by November 2005"）或事件（ "Remove this code when all clients can handle XML
responses."）。

## 九、格式

代码风格和格式确实比较随意，但一个项目中所有人遵循同一风格是非常容易的，作为个人未必同意下述格式规则的每一处，但整个项目服从统一的编程风格是很重要的，这样做才能让所有人在阅读和理解代码时更加容易。  

### 1. 行长度

每一行代码字符数不超过80。

我们也认识到这条规则是存有争议的，但如此多的代码都遵照这一规则，我们感觉一致性更重要。

优点： 
提倡该原则的人认为强迫他们调整编辑器窗口大小很野蛮。很多人同时并排开几个窗口，根本没有多余空间拓宽某个窗口，人们将窗口最大尺寸加以限定，一致使用80列宽，为什么要改变呢？

缺点：
反对该原则的人则认为更宽的代码行更易阅读，80列的限制是上个世纪60年代的大型机的古板缺陷；现代设备具有更宽的显示屏，很轻松的可以显示更多代码。

结论： 
80个字符是最大值。

例外：
1) 如果一行注释包含了超过80字符的命令或URL，出于复制粘贴的方便可以超过80字符；
2) 包含长路径的可以超出80列，尽量避免；
3) 头文件保护（防止重复包含）可以无视该原则。

### 2. 非ASCII字符  

尽量不使用非ASCII字符，使用时必须使用 UTF-8格式。  

哪怕是英文，也不应将用户界面的文本硬编码到源代码中，因此非ASCII字符要少用。特殊情况下可以适当包含此类字符，如，代码分析外部数据文件时，可以适当硬编码数据文件中作为分隔符的非ASCII字符串；更常用的是（不需要本地化的）单元测试代码可能包含非ASCII字符串。此类情况下，应使用UTF-8格式，因为很多工具都可以理解和处理其编码， 十六进制编码也可以， 尤其是在增强可读性的情况下——如"\xEF\xBB\xBF"是Unicode的zero-width no-break space字符，以UTF-8格式包含在源文件中是不可见的。


### 3. 空格和Tabs

只使用空格，每次缩进2个空格。  

使用空格进行缩进，不要在代码中使用tabs，设定编辑器将tab转为空格。  

### 4. 函数声明与定义

返回类型和函数名在同一行，合适的话，参数也放在同一行。
函数看上去像这样：  

	ReturnType ClassName::FunctionName(Type par_name1, Type par_name2) {
	  DoSomething();
	  ...
	}
	
如果同一行文本较多，容不下所有参数：  

	ReturnType ClassName::ReallyLongFunctionName(Type par_name1, Type par_name2,
												 Type par_name3) {
	  DoSomething();
	  ...
	}
	
甚至连第一个参数都放不下：  

	ReturnType LongClassName::ReallyReallyReallyLongFunctionName(
	    Type par_name1,  // 4 space indent
	    Type par_name2,
	    Type par_name3) {
	  DoSomething();  // 2 space indent
	  ...
	}
	
注意以下几点：

- 选择好的参数名字。  
- 如果参数没用的话，参数名可以不要。  
- 返回值总是和函数名在同一行。  
- 函数声明和定义的返回类型的地方不要留空格。  
- 左圆括号（open parenthesis） 总是和函数名在同一行。  
- 函数名和左圆括号间没有空格。  
- 圆括号与参数间没有空格。  
- 左大括号（open curly brace） 总在最后一个参数同一行的末尾处，不是新的一行。  
- 右大括号（close curly brace） 总是单独位于函数最后一行。  
- 右圆括号（close parenthesis） 和左大括号间总是有一个空格。  
- 所有形参应尽可能对齐。  
- 缺省缩进为2个空格。  
- 独立封装的参数保持4个空格的缩进。  


	class Foo {
	 public:
	  Foo(Foo&&);
	  Foo(const Foo&);
	  Foo& operator=(Foo&&);
	  Foo& operator=(const Foo&);
	};

	class Shape {
	 public:
	  virtual void Rotate(double radians) = 0;
	};

	class Circle : public Shape {
	 public:
	  void Rotate(double radians) override;
	};

	void Circle::Rotate(double /*radians*/) {}
	// Bad - if someone wants to implement later, it's not clear what the
	// variable means.
	void Circle::Rotate(double) {}


Attributes, and macros that expand to attributes, appear at the very beginning of the function declaration or definition, before the return type:

	MUST_USE_RESULT bool IsOK();

### 5. 函数调用

尽量放在同一行，否则，将实参封装在圆括号中。
函数调用遵循如下形式：

	bool result = DoSomething(argument1, argument2, argument3);
	
如果同一行放不下，可断为多行，后面每一行都和第一个实参对齐，左圆括号后和右圆括号前不要留空格：

	bool result = DoSomething(averyveryveryverylongargument1,
                              argument2, argument3);
	
参数也可以紧随函数调用之后4个空格缩进：  

	if (...) {
	  ...
	  ...
	  if (...) {
		bool result = DoSomething(
			argument1, argument2,  // 4 space indent
			argument3, argument4);
		...
	  }
	  
有时为了可读性，也可以这样排版：

	// Transform the widget by a 3x3 matrix.
	my_widget.Transform(x1, x2, x3,
						y1, y2, y3,
						z1, z2, z3);

### 6. 条件语句

提倡不在圆括号中添加空格，关键字else另起一行。  

对基本条件语句有两种可以接受的格式，一种在圆括号和条件之间有空格，一种没有。

最常见的是没有空格的格式，那种都可以，还是一致性为主。如果你是在修改一个文件，参考当前已有格式；如果是写新的代码，参考目录下或项目中其他文件的格式，还在徘徊的话，就不要加空格了。

	if (condition) { // no spaces inside parentheses
	... // 2 space indent.
	} else { // The else goes on the same line as the closing brace.
	...
	}
	
如果你倾向于在圆括号内部加空格：  

	if ( condition ) { // spaces inside parentheses - rare
	... // 2 space indent.
	} else { // The else goes on the same line as the closing brace.
	...
	}

注意所有情况下if和左圆括号间有个空格，右圆括号和左大括号（如果使用的话）间也要有个空格：  

	if(condition) // Bad - space missing after IF.
	if (condition){ // Bad - space missing before {.
	if(condition){ // Doubly bad.
	
	if (condition) { // Good - proper space after IF and before {.

有些条件语句写在同一行以增强可读性，只有当语句简单并且没有使用else子句时使用：

	if (x == kFoo) return new Foo();
	if (x == kBar) return new Bar();

如果语句有else分支是不允许的：  

	// Not allowed - IF statement on one line when there is an ELSE clause
	if (x) DoThis();
	else DoThat();
	
通常，单行语句不需要使用大括号，如果你喜欢也无可厚非，也有人要求if必须使用大括号：

	if (condition)
	  DoSomething(); // 2 space indent.
	  
	if (condition) {
	  DoSomething(); // 2 space indent.
	}
	
但如果语句中哪一分支使用了大括号的话，其他部分也必须使用：  

	// Not allowed - curly on IF but not ELSE
	if (condition) {
	  foo;
	} else
	  bar;
	  
	// Not allowed - curly on ELSE but not IF
	if (condition)
	  foo;
	else {
	  bar;
	}

	// Curly braces around both IF and ELSE required because
	// one of the clauses used braces.
	if (condition) {
	  foo;
	} else {
	  bar;
	}

	
### 7. 循环和switch选择语句

switch语句可以使用大括号分块；空循环体应使用{}或continue。

switch语句中的case块可以使用大括号也可以不用，取决于你的喜好，使用时要依下文所述。

如果有不满足case枚举条件的值，要总是包含一个default（如果有输入值没有case去处理，编译器将报警）。如果default永不会执行，可以简单的使用assert：

	switch (var) {
	  case 0: {  // 2 space indent
		...      // 4 space indent
		break;
	  }
	  case 1: {
		...
		break;
	  }
	  default: {
		assert(false);
	  }
	}
	
单句声明的循环体中，括号是可选的。

	for (int i = 0; i < kSomeNumber; ++i)
	  printf("I love you\n");

	for (int i = 0; i < kSomeNumber; ++i) {
	  printf("I take it back\n");
	}

空循环，要用括号或者continue，而不能直接用个分号。

	while (condition) {
	  // Repeat test until it returns false.
	}
	for (int i = 0; i < kSomeNumber; ++i) {}  // Good - empty body.
	while (condition) continue;  // Good - continue indicates no logic.

	while (condition);  // Bad - looks like part of do/while loop.


### 8. 指针和引用表达式

箭头（->）前后不要有空格，指针/地址操作符（*、&）后不要有空格。

下面是指针和引用表达式的正确范例：

	x = *p;
	p = &x;
	x = r->y;
	
注意：  

- 在访问成员时，箭头前后没有空格；
- 指针操作符*或&后没有空格。  

在声明指针变量或参数时，星号与类型或变量名紧挨都可以：

	// These are fine, space preceding.
	char *c;
	const string &str;
	
	// These are fine, space following.
	char* c; // but remember to do "char* c, *d, *e, ...;"!
	const string& str;
	
	char * c; // Bad - spaces on both sides of *
	const string & str; // Bad - spaces on both sides of &
	
同一个文件（新建或现有）中起码要保持一致。

### 9. 布尔表达式  

如果一个布尔表达式超过标准行宽（0字符），如果断行要统一一下。
下例中，逻辑与（&&）操作符总位于行尾：

	if (this_one_thing > this_other_thing &&
	    a_third_thing == a_fourth_thing &&
	    yet_another & last_one) {
	  ...
	}
	
两个逻辑与（&&）操作符都位于行尾，可以考虑额外插入圆括号，合理使用的话对增强可读性是很有帮助的。

### 10. 返回值

return表达式中不要使用圆括号。

	return result;                  // No parentheses in the simple case.
	// Parentheses OK to make a complex expression more readable.
	return (some_long_condition &&
			another_condition);
			
	return (value);                // You wouldn't write var = (value);
	return(result);                // return is not a function!

	
### 11. 变量及数组初始化

选择=还是()。
需要做二者之间做出选择，下面的形式都是正确的：

	int x = 3;
	int x(3);
	string name("Some Name");
	string name = "Some Name"
	
### 12. 预处理指令

预处理指令不要缩进，从行首开始。
即使预处理指令位于缩进代码块中，指令也应从行首开始。

	// Good - directives at beginning of line
	  if (lopsided_score) {
	#if DISASTER_PENDING      // Correct -- Starts at beginning of line
		DropEverything();
	# if NOTIFY               // OK but not required -- Spaces after #
		NotifyClient();
	# endif
	#endif
		BackToNormal();
	  }
	  
	// Bad - indented directives
	  if (lopsided_score) {
		#if DISASTER_PENDING  // Wrong!  The "#if" should be at beginning of line
		DropEverything();
		#endif                // Wrong!  Do not indent "#endif"
		BackToNormal();
	  }	  

  
### 13. 水平留白
水平留白的使用因地制宜。不要在行尾添加无谓的留白。

#### 通常：  

	void f(bool b) {  // Open braces should always have a space before them.
	  ...
	int i = 0;  // Semicolons usually have no space before them.
	// Spaces inside braces for braced-init-list are optional.  If you use them,
	// put them on both sides!
	int x[] = { 0 };
	int x[] = {0};

添加冗余的留白会给其他人编辑时造成额外负担，例如在他们合并代码的时候，即使他们可以去除掉这个空格。因此，不要引入多余的空格。如果你不小心引入了多余的空格要尽快去掉，或者在专门清理空格时去掉（确信没有其他人同时在使用该文件）。

#### 循环和垂直留白：  

	if (b) {          // Space after the keyword in conditions and loops.
	} else {          // Spaces around else.
	}
	while (test) {}   // There is usually no space inside parentheses.
	switch (i) {
	for (int i = 0; i < 5; ++i) {
	// Loops and conditions may have spaces inside parentheses, but this
	// is rare.  Be consistent.
	switch ( i ) {
	if ( test ) {
	for ( int i = 0; i < 5; ++i ) {
	// For loops always have a space after the semicolon.  They may have a space
	// before the semicolon, but this is rare.
	for ( ; i < 5 ; ++i) {
	  ...

	// Range-based for loops always have a space before and after the colon.
	for (auto x : counts) {
	  ...
	}
	switch (i) {
	  case 1:         // No space before colon in a switch case.
		...
	  case 2: break;  // Use a space after a colon if there's code after it.

#### 操作符：  

	// Assignment operators always have spaces around them.
	x = 0;

	// Other binary operators usually have spaces around them, but it's
	// OK to remove spaces around factors.  Parentheses should have no
	// internal padding.
	v = w * x + y / z;
	v = w*x + y/z;
	v = w * (x + z);

	// No spaces separating unary operators and their arguments.
	x = -5;
	++x;
	if (x && !y)
	  ...
  
### 14. 垂直留白
垂直留白越少越好。

这不仅仅是规则而是原则问题了：不是非常有必要的话就不要使用空行。尤其是：不要在两个函数定义之间空超过2行，函数体头、尾不要有空行，函数体中也不要随意添加空行。

基本原则是：同一屏可以显示越多的代码，程序的控制流就越容易理解。当然，过于密集的代码块和过于疏松的代码块同样难看，取决于你的判断，但通常是越少越好。

一些关于空行的经验也许有用：
- 函数开始和结束的换行对可读性基本没什么帮助。
- 在连续的if-else的语句块中的空行也许能让代码更有可读性。

