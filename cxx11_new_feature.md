# c++ 11 新特性
## 1. 初始化列表

### c++03 初始化列表

```cpp
int arr[3] = {3, 2, 4};

std::vector<int> v;
v.push_back(3);
v.push_back(4);
v.push_back(2);
```

### c++11 扩展支持

所有的STL相关的容器都添加了这个新特性，可以支持初始化列表，如map, list

```cpp
std::vector<int> v = {3, 2, 4};
```

可以定义自己的支持初始化列表类型:

```cpp
#include <initializer_list>
class myVector {
	std::vector<int> my_v;
public:
	myVector(const initializer_list<int>& v){
		for(initializer_list<int>::iterator itr = v.begin(); itr != v.end(); ++itr){
			my_v.push_back(*itr);
		}
	}
};

myVector v = {1, 2, 3, 4};
myVector v{1, 2, 3, 4};
```

## 2. 统一初始化

### c++03 

```cpp
class dog{
public:
	int age;
	string name;
};

// 聚合初始化
dog d1 = {2, "terly"};
```

### c++11

```cpp
class dog{
public:
	dog(int age, string name){...};
};

dog d1 = {2, "terly"};
```

### 统一初始化编译器选择顺序

```cpp
// 1. 初始化列表构造函数
// 2. 普通构造函数
// 3. 聚合初始化器

dog d1{3};
class dog {
public:
	// 第三选择
	int age;
public:

	// 第二选择
	dog(int a){
		age = a;
	}

	// 第一选择
	dog(const initializer_list<int> & a){
		age = *(a.begin());
	}
	
};

```

## 3. auto 类型

```cpp
std::vector<int> v = {1, 2, 3, 4};
```

### c++03

```cpp
for (std::vector<int>::iterator i = .begin(); i != .end(); ++i) {
	my_vec.push_back(*i);
}
```

### c++11

```cpp
for (auto i = .begin(); i != .end(); ++i){
	my_vec.push_back(*i);
}

// int 类型
auto a = 6;

// double 类型
auto b = 4.9;

//int 类型
auto c = a;
```

## 4. foreach 支持

### c++03

```cpp
for (std::vector<int>::iterator i = .begin(); i != .end(); ++i) {
	cout << (*i);
}
```
### c++11

```cpp
for (auto i : v){
	cout << i; // i 是只读的
}

for(auto i& : v){
	i++; // i 为元素的引用
}
```

## 5. nullptr 类型

为了弥补 c++03 中NULL的不足， 例如有两个函数:

```cpp
void foo(int i){ 
	std::cout << "int foo" << std::endl;
}

void foo(char *p){
	std::cout << "char foo" << std::endl;
}

int main(){
	// 会产生歧义
	foo(NULL);

	//c++11 会调用foo(char* p)
	foo(nullptr);
}
```

## 6. 枚举类型

为了增强代码的安全性，c++11中定义了枚举类型，让我看下面的例子进行对比:

### c++03

```cpp
enum apple {green_a, red_a};
enum orange {big_o, small_o};
apple a = green_a;
orange o = big_o;

// 会输出 ‘green_a and big_o is same’
if(a == o){
	std::cout << "green_a and big_o is same" << std::endl;
} else {
	std::cout << "green_a and big_o is not same" << std::endl;
}
```
### c++11

```cpp
enum class apple {green_a, red_a};
enum class orange{big_o, small_o};

apple a = apple::green_a;
orange o = orange::big_o;

// 编译器会报错，因为没有定义 == (apple, orange)
if(a == o){
	std::cout << "green_a and big_o is same" << std::endl;
} else {
	std::cout << "green_a and big_o is not same" << std::endl;
} 
```
## 7. 静态断言

### c++03

```cpp
// 运行时判断
assert(mypointer != NULL);
```

### c++11

```cpp
// 编译时判断
static_assert(sizeof(int) == 4);
```

## 8. 委托构造

## c++03

在c++03中，不支持在一个构造函数中调用另一个构造函数，如以下代码是错误的：

```cpp
class dog
{
public:
	dog(){...}
	dog(int a) {
		dog();
		doOtherThingFun();
	}
};

// 为了达到以上效果必须这么写

class dog
{
public:
	init(){ ... }
	dog(){ init(); }
	dot(int a){
		init();
		doOtherThingFun();
	}
};

```

## c++11

```cpp
class dog {
public:
	// 每个构造函数中都会调用
	int age = 3;

public:
	dog();
	dog(int a) : dog() {doOtherThingFun();}	
};
```

## 9 虚函数增加overide关键字

### c++03

程序员可能因为疏忽，在继承类中没有使用继承的函数，而写了一个自己新定义的函数：

```cpp
class dog {
	virtual void A(int);
	virtual void B() const;
};

class yellowDog : public dog {
	// 下面将会创建两个新的函数
	virtual void A(float);
	virtual void B();
};
```

### c++11

```cpp
class dog
{
public:
	virtual void A(int);
	virtual void B() const;
};

class yellowDog : public dog {
public:
	// 如果添加 override 关键字，编译器会检查函数签名是否与父累的相同，如果不同会报错
	virtual void A(float) override;
	virtual void B() override;
	void c() override;
};
```

## 10 final 关键字

c++11 中 final 关键字可以修饰class或者虚函数，标明不能被继承或者重写:

```cpp
// 标明此类不能被继承
class dog final {

}

class dog {
	// 表明此虚函数不能被重写
	virtual void bark() final {}
}
```

## 11 默认构造函数

## c++03

```cpp
class dog
{
private:
	int age;
public:
	dog() {};
	dog(int a){
		a = age;
	}
};
```

## c++11

```cpp
class dog {
private:
	int age;
public:
	dog() = default;
	dog(int a){
		a = age;
	}
	
};
```
## 12 delete 关键字增强

### c++03

```cpp
class dog {
public:
	dog(int age){}
};

dog a(2);

// 实际上下面的语句是不合理的，但是c++03中是无错误的
dog b(3.0);
a = b;
```

### c++11

c++11 中的delete关键字可以限制上面的情况

```cpp
class dog {
public:
	dog(int age) {}
	dog(double) = delete;
	dog& operator=(const dog&) = delete;	
};

```

## 13. constexpr 关键字

### c++03

```cpp
// OK
int arr[6];

int A() {return 4;}

// 编译错误
int arr[A() + 3];
```

### c++11

```cpp
// 编译阶段强制计算
constexpr int A() {return 5;}
int arr[A() + 3];

constexpr int cube(int x) {return x*x*x;}
// 编译阶段进行计算
int y = cube(10)
```

## 14. 字符串

### c++03

```cpp
char *s = "hello";
```

### c++11

```cpp
// UTF-8
char *s1 = u8"hello";

// UTF-16
char16_t *s2 = u"hello";

// UTF-32
char32_t *s3 = U"hello";

//raw
char *s4 = R"hello\\";
```

## 15. lambda

```cpp
std::cout << [](int x, int y){return x + y;}(3, 4) << std::endl;
auto f = [](int x, int y){return x + y;};
std::cout << f(3, 4) << std::endl;

template<typename fun>
void filter(fun f, std::vector<int> v){
	for (auto i : v){
		if(f(i)){
			std::cout << i << std::endl;
		}
	}
}

int main(){
	std::vector<char> v{1, 2, 3, 4, 5};
	filter([](int x){return x > 3;}, v);
	filter([](int x){return x > 3 && x < 5;}, v);
	int b = 6;

	// lambda 表达式中用到了外部变量b，此变量需要以引用的方式传入，因此用[&]标记
	filter([&](int x){return x > 3 && x < b;}, v);
}
```
