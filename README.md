# cppLearn
CPP 学习杂记

1  非虚函数是静态绑定的，在编译的时候已经确定了需要调用的函数地址
   把子类通过强制类型转换为父类，再掉用父类中被子类重写的非虚函数，依然执行的是父类的函数
   因为非虚函数没有动态的添加到虚函数表中，被强制转换为父类的子类对象，
   在编译的时候，子类对象被强制转换为父类对象，编译器就把当前对象当做父类对象来获取函数指针
   所以执行的依然是父类的函数
   
2
#include <iostream>
using namespace std;

class Hello
{
public:
    Hello(){};
    ~Hello(){};
    void Printf()
    {
        printf("in printf\n");
        printf("%p",this);
    }
};
 
int main() {
    Hello *p = NULL;
    p->Printf();//函数打印 in printf和(nil)
}
非虚函数的调用，被编译器替换为了普通函数的调用
p->Printf(); --> 替换为Printf(Hello* this)=Prinf(p)
对象p被当做Printf函数的一个普通参数传入，此时的Printf函数就像一个简单的全局函数
参数this为NULL

参考：https://www.cnblogs.com/likui360/p/6369915.html
虚函数的调用时要先有对象，然后从对象的内存空间中找到虚函数表，再找到虚函数执行，多以虚函数的执行一定要先有对象
非虚函数的地址对编译期来说“静态”的，也就是函数地址在编译期就已经确定了，实例地址对于非虚函数只是那个 this 指针参数。所以只要不访问类的实例数据就没什么问题
从汇编中可以看出，虚函数的访问是通过对象地址查表得到的
而普通函数的问题，编译器已经分配了具体的地址，不需要对象也可以得到函数的地址，然后执行

3 静态成员变量的内存分配
 static 成员变量和普通 static 变量一样，都在内存分区中的全局数据区分配内存，到程序结束时才释放。
 这就意味着，static 成员变量不随对象的创建而分配内存，也不随对象的销毁而释放内存。
 而普通成员变量在对象创建时分配内存，在对象销毁时释放内存
 
4 静态成员函数
静态成员函数与普通成员函数的根本区别在于：普通成员函数有 this 指针，可以访问类中的任意成员；
而静态成员函数没有 this 指针，只能访问静态成员（包括静态成员变量和静态成员函数）

5 this指针
http://c.biancheng.net/cpp/biancheng/view/201.html
成员函数最终被编译成与对象无关的普通函数，除了成员变量，会丢失所有信息，
所以编译时要在成员函数中添加一个额外的参数，把当前对象的首地址传入，以此来关联成员函数和成员变量。
这个额外的参数，实际上就是 this，它是成员函数和成员变量关联的桥梁

6  C++函数编译原理和成员函数的实现
https://blog.csdn.net/chengonghao/article/details/51034164
对象的大小只受成员变量的影响，和成员函数没有关系。成员函数在代码区分配内存，而不是在栈区

7  __thiscall
__stdcall，__cdecl，__pascal，__fastcall的区别
http://c.biancheng.net/cpp/html/2847.html

8  运算符重载
   运算符相当于一种函数，运算符重载相当于函数重载
   
9  虚析构函数在虚函数表中吗

https://blog.csdn.net/F_cff/article/details/79690470
虚析构函数的地址存在于虚函数表中，和普通虚函数别无二致，同时也会像普通的虚函数一样进行覆盖
虽然父子的析构函数名字不一样，但是他们占同一个坑（即父子析构函数在虚函数表中的位置是一样的，否则就不存在多态了）
析构时，到特定的坑中调用该类型的析构函数，其析构函数中又嵌套了很多对父类的析构函数的调用

10

虚函数表：相当于为不同的对象注册了不同的事件回调函数
事件发生：相当于调用对象方法 如：obj->fun()
事件处理：调用某个对象的方法后，就去虚函数表的对应位置取出函数指针，运行函数

11  虚基类表，虚继承

