<fstream>

​		当一个fstream对象被销毁时，close会自动调用，与之关联的文件会自动关闭。

**文件模式**

- in	以读方式打开
- out  以写方式打开
- app 写操作前定位到文件尾(追加写)
- ate 打开文件后立刻定位到文件尾
- trunc 截断
- binary 二进制				

指定文件模式限制：

1. 只可以对ofstream或fstream对象设置out模式
2. 只可以对ifstream或fstream对象设置in模式
3. 只有当out被设定时，才可以设置trunc
4. 只要trunc没有被设定，就可以设定app模式。在app模式下，即使没有显式指定out模式，文件也以写模式打开
5. 默认情况下即使没有显式指定trunc模式，使用out模式打开的文件也会被截断，如果我们想要追加数据，必须显式指定app模式。对于fstream对象，还可以指定in模式来避免out模式对文件的截断。
6. ate和binary模式可以用于任何类型的文件流对象，且可以与其他任何文件模式组合使用。

**默认的文件模式**

每个文件流类型都定义了一个默认的文件模式：

1. ifstream----in
2. ofstream----out
3. fstream----in|out

**使用fstream进行文件读写时的问题**

​		ofstream默认的模式为ios::in | ios::out；这意味着它既可以读取，也可以写入，因此他不会对文件进行截断(但是此时文件指针处于文件开始位置，此时直接写会覆盖之前的内容)。

```c++
#include <iostream>
#include<fstream>
#include<string>
using namespace std;
#define FILENAME "E:\\test.txt"

int main() {
		
	fstream fstr(FILENAME, ios::out|ios::in);
	if(fstr.is_open()){
		cout << "file opened successfully" << endl;
	}
	else{
		cout << "file not opened successfully" << endl;
	}
	fstr << "you are so stupid" << endl;
	string s1;
	fstr >> s1;
	cout << "s1 = " << s1 << endl;
	return 0;
}
```

​		对于上述程序，使用fstream对文件进行写入，然后读出。但是由于写入后文件指针位于文件尾，因此，无法读取写入的内容，必须偏移文件指针才行。

- ifstream和fstream有成员函数seekg，可以设置文件读指针的位置。
- ofstream和fstream有成员函数seekp，可以设置文件写指针的位置。

函数原型如下

```C++
ostream &seekp(int offset, int mode);	//p是put的意思，用于输出流
istream &seekg(int offset, int mode);	//g是get的意思，用于输入流
```

mode代表文件读写指针的设置模式：

- iso::beg  :让文件读指针(或写指针)指向从文件开始向后的offset字节处。offset等于0即代表文件开头，并且offset非负数。
- ios::cur  :在此情况下offset 为负数则表示将读指针（或写指针）从当前位置朝文件开头方向移动 offset 字节，为正数则表示将读指针（或写指针）从当前位置朝文件尾部移动 offset字节，为 0 则不移动。
- ios::end：让文件读指针（或写指针）指向从文件结尾往前的 |offset|（offset 的绝对值）字节处。在此情况下，offset 只能是 0 或者负数。

```C++
#include <iostream>
#include<fstream>
#include<string>
using namespace std;
#define FILENAME "E:\\test.txt"

int main() {
		
	fstream fstr(FILENAME, ios::out|ios::in);
	if(fstr.is_open()){
		cout << "file opened successfully" << endl;
	}
	else{
		cout << "file not opened successfully" << endl;
	}
	fstr << "you are so stupid" << endl;
	//偏移文件指针
	fstr.seekp(0);	//fstr.seekp(0, ios::beg);
	string s1;
	fstr >> s1;
	fstr << "you are so stupid!!!";
	cout << "s1 = " << s1 << endl;	
	return 0;
}
```

​		对于fstream，似乎seekp和seekg都可以。

还可以得到当前读写指针的具体位置

```C++
int tellg();
int tellp();
```

​		想要获取文件长度，可以利用上述seekp或者seekg将文件指针偏移至文件结尾，再使用tellg/tellp获取长度。

**文件结尾问题**

```C++
#include <iostream>
#include<fstream>
#include<string>
using namespace std;
#define FILENAME "E:\\test.txt"

int main() {
		
	ifstream ifstr(FILENAME, ios::in);	//文件内容:you are so stupid
	if(ifstr.is_open()){
		cout << "file opened successfully" << endl;
	}
	else{
		cout << "file not opened successfully" << endl;
	}
	string s1;
	while(!ifstr.eof()){
		ifstr >> s1;
		cout << s1 << endl;
	}
	return 0;
}
//按理来说，读取的结果应该和文件内容相同，但是实际结果如下：
file opened successfully       
you
are
so
stupid
stupid	//stupid被输出两次！！！
```

​		问题产生的原因：eof()函数是检测是否读到EOF，如果读到EOF，则返回TRUE，而不是读到文件最后一个字符时返回TRUE。EOF在最后一个字符之后(人们经常误认为 EOF 是从文件中读取的一个字符(牢记)其实，EOF 不是一个字符，它被定义为是 int 类型的一个负数(比如 -1)，**EOF 也不是文件中实际存在的内容**) 。   

​		 如何解决这个问题？？？

```c++
#include <iostream>
#include<fstream>
#include<string>
using namespace std;
#define FILENAME "E:\\test.txt"

int main() {
		
	ifstream ifstr(FILENAME, ios::in);	//文件内容:you are so stupid
	if(ifstr.is_open()){
		cout << "file opened successfully" << endl;
	}
	else{
		cout << "file not opened successfully" << endl;
	}
	string s1;
	
	while(ifstr >> s1){		//先读取，然后判断是否到达文件结尾
		if(!ifstr.eof())
			cout << s1 << endl;
	}
	return 0;
}
```

