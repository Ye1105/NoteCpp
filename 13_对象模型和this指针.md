<!--
 * @Author: 15868707168@163.com 15868707168@163.com
 * @Date: 2023-03-24 15:14:35
 * @LastEditors: 15868707168@163.com 15868707168@163.com
 * @LastEditTime: 2023-03-24 17:28:33
 * @FilePath: \CPlusPlusLessons\13.对象模型和this指针.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
#### 1. 只有非静态成员变量才属于类的对象上
+ 在 C++ 中，类内的成员变量和成员函数是分开存储的
+ ==只有非静态成员变量才属于类的对象上==,即【非静态函数、静态变量、静态函数】都不属于类的对象上。

#### 2. this 指针概念
+ C++ 中成员变量和成员函数是分开存储的，每一个非静态成员函数只会诞生一份函数实例，也就是说多个同类型的对象会公用一块代码，那么问题是：这一块代码是如何区分哪个对象调用自己的呢？
C++ 通过提供特殊的对象指针，this指针来解决上诉问题。**this指针指向被调用的成员函数所属的对象**
this指针是隐含每一个非静态成员函数内的一种指针
this指针不需要定义，直接使用即可
==this指针的本质是：指针常量，不可以修改指向==

+this指针的用途：
    + 当形参和成员变量同名时，可用this指针来区分
    + 当类的非静态成员函数中返回对象本身，可以使用 return *this

```C++
class Person {
private:

	int age;

public:

	Person(int age) {
		this->age = age;
	}

	Person PersonAddAge(Person& p) {
		age += p.age;
		return *this;
	}

	//析构代码，将堆区开辟数据做释放操作
	~Person() {

		cout << "析构函数函数" << endl;
	}

	//函数也不占对象空间，所有函数共享一个函数实例
	void printProp();

};


void Person::printProp() {

	cout << this->age << endl;
}

void call() {

	//Person p(18, "xiaoming");

	Person p(10);

	Person p2(p);

	Person p4 = p2.PersonAddAge(p2).PersonAddAge(p2).PersonAddAge(p2);

	p.printProp();

	p2.printProp();

	p4.printProp();
}

int main() {

	call();
	system("pause");
	return 0;
}
```

#### 3.空指针访问成员函数
+ C++ 中空指针也是可以调用成员函数的，但是也要注意没有用到this指针
+ 如果用到this指针，需要加以判断保证代码的健壮性

#### 4.const 修饰成员函数
+ **常函数**
    + 成员函数后加 const ，我们称这个函数为==常函数==
    + 常函数内不可以修改成员属性
    + 成员属性声明时加关键字 mutable 后，在常函数中依然可以修改

+ **常对象**
    + 声明对象前加 const 称该对象为常对象
    + 常对象只能调用常函数

```C++
class Person {
private:

	int age;

public:
	mutable int sex; //特殊变量，即使在常函数中，也可以修改这个值
	int order;

	Person(int age) {
		this->age = age;
	}

	Person PersonAddAge(Person& p) {
		age += p.age;
		return *this;
	}

	//this 指针的本质 是指针常量 指针的指向是不可以修改的
	//const Person * const this;
	void showAge()  const
	{
		this->sex = 10;

		//this->age = 10;
		//this=NULL; //this指针不可以修改指针的指向的
	}


	//析构代码，将堆区开辟数据做释放操作
	~Person() {

		cout << "析构函数函数" << endl;
	}

	//函数也不占对象空间，所有函数共享一个函数实例
	void printProp();

};

void Person::printProp() {

	cout << this->age << endl;
}

void call() {

	//Person p(18, "xiaoming");

	Person p(10);

	Person p2(p);

	Person p4 = p2.PersonAddAge(p2).PersonAddAge(p2).PersonAddAge(p2);

	p.printProp();

	p2.printProp();

	p4.printProp();
}


void call2() {
	const Person p(10);  //在对象前加const，变为常对象
	p.sex = 10;   //sex是特殊值，在常对象下也可以修改
	//p.order = 20;

	p.showAge();  //常对象只能调用常函数

	//p.printProp();
}

int main() {

	call2();
	system("pause");
	return 0;
}
```

