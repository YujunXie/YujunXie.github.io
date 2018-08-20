---
title: C++之STL(1)：string类
key: 20171125
tags: C++
---

STL为标准模板库（Standard Template Library）
封装了数据结构的内部实现，抽象为接口直接调用，使代码短小高效。

STL的基本组成：

- 容器：隐藏了如数组，链表，栈，队列等数据结构的内部实现细节，以模板类的方法提供这些数据结构。
- 迭代器：提供了访问容器中对象的方法。
- 算法：用来操作容器中数据的函数。

<!--more-->

1. string**类**（不是字符串）

引用：`#include<string>`

~~~
构造函数和析构函数： 
string s;         //生成一个空字符串s
string s(str);    //拷贝构造函数 生成str的复制品
string s(num,c);  //生成一个字符串，包含num个c字符
s.~string();      //销毁所有字符，释放内存

字符操作：
s[pos]; s.at(pos); //存取单一字符，注意[]不检查数组越界情况
s.substr(pos,n); //返回pos开始的n个字符组成的字符串

特性：
int capacity();    //返回当前容量（即string中不必增加内存即可存放的元素个数）
int max_size();    //返回string对象中可存放的最大字符串的长度
int size();  int length();   //返回当前字符串的长度
bool empty();       //当前字符串是否为空
void resize(int len,char c);//把字符串当前大小置为len，并用字符c填充不足的部分

输入操作：
>>;
getline(istream &amp;in,string &amp;s); 从输入流中读入字符，存到string变量
直到出现以下情况为止：
•读入了文件结束标志
•读到一个新行
•达到字符串的最大长度
–如果getline没有读入字符，将返回false，可用于判断文件是否结束

赋值：
s.assign(const char *s);        //用c类型字符串s赋值
s.assign(const char *s,int n);  //用c字符串s开始的n个字符赋值
s.assign(const string &amp;s);      //把字符串s赋给当前字符串
s.assign(int n,char c);         //用n个字符c赋值给当前字符串
s.assign(const string &amp;s,int start,int n);
s.assign(const_iterator first,const_itertor last);//把first和last迭代器之间的部分赋给字符串
=

连接：
+=
s.append();       //函数参数类型和赋值类似
s.push_pack(c);    //只能连接单个字符

比较：
==, >, <, >=, <=, !=
s.compare(const string &amp;s2);//比较当前字符串和s的大小
s.compare(int pos, int n,const string &amp;s2);//比较当前字符串从pos开始的n个字符组成的字符串与s的大小
s.compare(int pos, int n,const string &amp;s2,int pos2,int n2)const;//比较当前字符串从pos开始的n个字符组成的字符串与s2中从pos2开始的n个字符组成的字符串
compare在>时返回1，<时返回-1，==时返回0 

查找：
find() 
rfind() 
find_first_of() 
find_last_of() 
find_first_not_of() 
find_last_not_of()
所有函数中第一个参数是被查找的字符或字符串。第二个参数（可有可无）指出string内的搜寻起点索引，第三个参数（可有可无）指出搜寻的字符个数。
查找成功时返回所在位置，失败返回string::npos的值（超级大）。

替换：
s1.swap(s2);     //交换s1与s2的值
s.replace(1,2,”nternationalizatio”); //从索引1开始的2个替换成后面的 

插入：
s.insert(pos,str); //不能插入单个字符
s.insert(iterator it, int n, char c);//在it处插入n个字符c

删除：
iterator erase(iterator it);//删除it指向的字符，返回删除后迭代器的位置
string &amp;erase(int pos = 0, int n = npos);//删除pos开始的n个字符，返回修改后的字符串
s.clear();     //清空
s.pop_back()    //删除最后一个字符

迭代器：
s.begin(); s.end(); s.rbegin(); s.rend();
~~~

----------

**string与char[ ],char*之间的纠葛**

先看看C语言中的字符和字符串：[C语言的字符和字符串](https://yujunxie.github.io/2017/11/24/C%E8%AF%AD%E8%A8%80%E7%9A%84%E5%AD%97%E7%AC%A6%E5%92%8C%E5%AD%97%E7%AC%A6%E4%B8%B2.html)

区别：
char[ ], char*是用来表示字符和字符串这两种数据类型本身，而string是包含字符串元素的一种容器。
容器的内存管理是由系统处理，除非系统内存池用完，不然不会出现这种内存问题。
char[ ], char*的内存管理由用户自己处理，很容易出现内存不足的问题。

且，string类中的成员函数很多，存储的字符串能直接调用函数来操作。相比较，能用string的时候就不用char*,char[ ]了。

char *s="string"的内容是不可以改的；char s[10]="string"的内容是可以改的。

相互转换：

1. string 转换成 char *：

    	string s1 = "abcdeg";
    	const char *k = s1.c_str();
    	const char *t = s1.data();

	或者

    	int len = s1.length();
    	char *data = (char*)malloc((len+1)*sizeof(char));
    	s1.copy(data,len,0);

2. char[ ], char * 转换成string
	
		string s;
	   	char *p = "hello";
	   	s = p;
	   	printf("%s\n",s1.c_str());
	   //cout << s;

3. string转换成char[ ]

	    string pp = "dagah";
	    char p[8];
	    int i;
	    for(i=0;i<pp.length();i++)
	        p[i] = pp[i];
	    p[i] = '\0';
	    printf("%s\n",p);
	    //cout<<p;
