		优先级和结合律决定了运算对象的组合方式，但是并没有决定具体的运算顺序(个别运算符除外)。

```C++
//以如下表达式为例：
6 + 3 * 4 / 2 + 2;
//有加法，有乘除法，显然，后者优先级更高，具体组合如下：
((6 + ((3 * 4) / 2)) + 2)
```

​		算术运算符：满足左结合律，意味着，如果运算符的优先级相同，将按照从左向右的顺序组合运算对象。

​		由四种运算符规定了求值顺序：&&、||、,、?:

### 类型转换

#### 显式类型转换

​		C++命名的强制类型转换：cast-name<type>(expression);

​		其中type是转换的目标类型，而expression是要转换的值。如果type是引用类型，则结果是左值。

1. static_cast

   任何具有**明确定义的类型转换**，只要**不包含底层cons**t，都可以使用static_cast。

   ```C++
   //适用于基本的数据类型转换,类和基本数据类型、类类型之间的转换(明确定义)
   
   //转换失败
   const char *cp = "hello world";
   char *p = static_cast<char *p>(cp);	//static_cast 无法丢掉常量或其他类型限定符
   //也就是上述的不包含底层const
   ```

2. const_cast

   const_cast只能用于改变运算对象的底层const，只有const_cast能改变表达式的常量属性。
   用于函数重载时，将一个返回const的函数，使用const_cast去掉其常量特性。
	```
	const string &compare(const string &s1, const string &s2){
		...
		return XX;
	}
	string &compare(string &s1, string &s2){
		auto &r = compare(s1, s2);
		return const_cast<string &>(r);
	}

3. reinterpret_cast

   reinterpret_cast通常为运算对象的位模式提供较低层次上的重新解释。

   ```C++
   //将一个int类型的指针转换成char类型的指针
   int *iptr;
   char *cptr = reinterpret_cast<char *>(iptr);
   
   //关于这个的用处，在数组上面可以很好地体现出来
   int arr[2][3] = {{0,1,2}, {3,4,5}};
   //arr是一个指向内含3个元素的数组的指针，arr + 1将会指向{3,4,5}这个数组
   int *iptr = reinterpret_cast<int *>(arr);
   cout << "*(iptr + 1) = " << *(iptr + 1) << endl;
   //输出结果为1。两个原本类型不同的指针可以赋值，并且经过转换后，每次+1只会改变一个int大小
   ```

4. dynamic_cast

​		暂未详细介绍，和后面的继承有关。

​		C++ primer中不推荐使用强制类型转换，但是只要掌握的足够好，使用也是无可厚非的。而且C中其实是推荐使用强制类型转换的。
