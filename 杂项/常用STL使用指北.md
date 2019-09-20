# 常用STL使用指北

## set和multiset

set和multiset都是基于红黑树（显然是一个二叉搜索树）的STL。

### 定义

我们可以使用`(multi)set<元素类型>名称`来定义一个`(multi)set`。

### 自定义排序

默认排序方式都是从小到大。因为结构体之间没有定义`<`，所以我们需要自定义一个比较函数。

如果元素不是结构体：

```cpp
//自定义比较函数myComp,重载“（）”操作符
struct myComp
{
	bool operator()(const int &a,const int &b)
	{
		return a > b;//从大到小排序
	}
}
set<int,myComp>s;
set<int,myComp>::iterator it;
 

```



如果元素是结构体：

```cpp
struct Info
{
	string name;
	float score;
	//重载“<”操作符，自定义排序规则
	bool operator < (const Info &a) const
	{
		//按score从大到小排列
		return a.score<score;
	}
}
 
set<Info> s;
set<Info>::iterator it;
```

或者

```cpp
//自定义比较函数myComp,重载“（）”操作符
struct myComp
{
	bool operator()(const your_type &a,const your_type &b)
	{
		return a.data > b.data;//按照data从大到小排序
	}
}
set<your_type,myComp>s;
set<your_type,myComp>::iterator it;
```