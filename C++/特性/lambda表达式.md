# lambda表达式语法
&emsp;

```cpp
#include <iostream>

int main(int argc, char* argv[])
{
	/**
	* 1. 基本形式与语法
	*/

	//如果没有传入参数，参数列表可以省略
	auto lambda1 = []() { std::cout << "Hello, World !" << std::endl; };
	auto lambda2 = [](int x, int y) -> int //返回参数可使用尾置返回语法
		{
			if (x > y)
				return 1;
			else if (x < y)
				return -1;
			else
				return 0;
		};

	/**
	* 输出:
	* Hello, World !
	*/
	lambda1();
	/**
	* 输出:
	* -1
	*/
	std::cout << lambda2(1, 2) << std::endl;
	/**
	* 输出:
	* 1
	*/
	std::cout << lambda2(2, 1) << std::endl;
	/**
	* 输出:
	* 0
	*/
	std::cout << lambda2(1, 1) << std::endl;

	return 0;
}
```
&emsp;

## 捕获
```C++
#include <iostream>
#include <algorithm>
#include <vector>
#include <string>

int main(int argc, char* argv[])
{
	/**
	* lambda表达式捕获外部变量示例
	*/

	std::vector<int> vec{ 1,2,3,4,5,6,7,8,9,10 };
	std::string str;

	//&str为引用捕获，在函数内部可以被修改
	std::for_each(vec.begin(), vec.end(), [&str](int n) {
		str.append(std::to_string(n)).append(",");
		});
	str.pop_back();

	/**
	* 输出:
	* 1,2,3,4,5,6,7,8,9,10
	*/
	std::cout << str << std::endl;

	//argc为值捕获（拷贝），该变量在函数内部修改后不会影响外部变量
	std::for_each(argv, argv + argc, [argc](char* args) {
		/**
		* 输出命令行参数
		*/
		std::cout << args << std::endl;
	});

	/**
	* 关于其他捕获语法：
	* [=]: 所有外部变量的捕获均为值捕获（拷贝）
	* [&]: 所有外部变量的捕获均为引用捕获
	* [=, var...]: var以引用捕获，其他外部变量以值捕获
	* [&, var...]: var以值捕获，其他外部变量以引用捕获
	*/

	return 0;
}
```
&emsp;

# 其他
&emsp;

当lambda表达式不使用`[]`捕获时，其表现与函数指针相同；如果使用了`[]`捕获，则会变成带有成员变量（引用或拷贝）和重载`operator()`函数运算符的类，所以将lambda表达式代入参数为函数指针的函数时，不能使用`[]`捕获。