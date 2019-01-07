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
