
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

### 1. 通用命名规则（ General Naming Rules）
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
文件名要全部小写，可以包含下划线（_）或短线（-），按项目约定来，如果没有的话最好用"_"。
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
变量名一律小写，单词间以下划线相连，类的成员变量以下划线结尾，如my_exciting_local_variable、 my_exciting_member_variable_。

**普通变量命名：**
举例：
	string table_name; // OK - uses underscore.
	string tablename; // OK - all lowercase.
	string tableName; // Bad - mixed case.
	
**全局变量：**
对全局变量没有特别要求，少用就好，可以以g_或其他易与局部变量区分的标志为前缀。

### 5. 常量命名
在名称前加k： kDaysInAWeek。
所有编译时常量（无论是局部的、全局的还是类中的）和其他变量保持些许区别， k后接大写字母开头的单词：
	const int kDaysInAWeek = 7
	
### 6. 函数命名  
普通函数大小写混合，存取函数则要求与变量名匹配： MyExcitingFunction()、MyExcitingMethod()、 my_exciting_member_variable()、set_my_exciting_member_variable()。

普通函数：
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





