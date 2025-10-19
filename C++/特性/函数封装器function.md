# 基本形式

```cpp
#include <iostream>
#include <functional>

/**
* 1. C++11新增的类型别名声明方式
*/

using pFunc1 = void();						//普通函数
using pFunc2 = void(int, int);				//带参数的函数
using pFunc3 = int(int, int);				//带参数和返回值的函数
using pFunc4 = void* ();					//返回指针的函数
using pFunc5 = const char* ();				//返回常量（底层）指针的函数
using pFunc6 = char* const();				//返回常量（顶层）指针的函数
using pFunc7 = const char* const(); ;		//返回常量指针（底层+顶层）的函数

std::function<void()> f1;
std::function<void(int, int)> f2;
std::function<int(int, int)> f3;
std::function<void* ()> f4;
std::function<const char* ()> f5;
std::function<char* const()> f6;
std::function<const char* const()> f7;

/**
* 测试函数
*/
void func1()
{
	std::cout << __FUNCTION__ << std::endl;
}
void func2(int x, int y)
{
	std::cout << __FUNCTION__ << std::endl;
}
int func3(int x, int y)
{
	std::cout << __FUNCTION__ << std::endl;
	return 0;
}
void* func4()
{
	std::cout << __FUNCTION__ << std::endl;
	return nullptr;
}
const char* func5()
{
	std::cout << __FUNCTION__ << std::endl;
	return nullptr;
}
char* const func6()
{
	std::cout << __FUNCTION__ << std::endl;
	return nullptr;
}
const char* const func7()
{
	std::cout << __FUNCTION__ << std::endl;
	return nullptr;
}

int main(int argc, char* argv[])
{
	f1 = func1;
	f2 = func2;
	f3 = func3;
	f4 = func4;
	f5 = func5;
	f6 = func6;
	f7 = func7;

	//以下均属出自身函数名(func1, func2...)
	f1();
	f2(0, 0);
	f3(0, 0);
	f4();
	f5();
	f6();
	f7();


	//可查看封装的函数指针类型
	std::cout << f1.target_type().name() << std::endl;
	std::cout << f5.target_type().name() << std::endl;
	std::cout << f4.target_type().name() << std::endl;

	//可使用target查看封装的函数指针地址(模板参数不能使用类型别名声明的类型)
	auto p1 = f1.target<void(*)()>();
	std::cout << p1 << std::endl;
	auto p7 = f7.target<const char* const(*)()>();
	std::cout << p7 << std::endl;

	/**
	* std::function支持拷贝，移动操作，在此不做赘述
	* 
	*/

	return 0;
}
```
&emsp;

# 其他
&emsp;

当遇到重载函数需要用std::function封装时，为避免编译时出现二义性问题（模板类的类型在编译后才会知道），将它们改写为lambda表达式或者函数指针的形式后再进行封装。