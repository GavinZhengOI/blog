# [算法模版]FWT-快速沃尔什变换

## FWT有啥用啊

我们知道，`FFT`可以解决多项式的卷积，即
$$
C_k=\sum_{i+j=k}A_i*B_j
$$
如果将操作符换一下，换成集合运算符

比如
$$
C_k=\sum_{i|j=k}A_i*B_j\\\
C_k=\sum_{i\&j=k}A_i*B_j\\\
C_k=\sum_{i\oplus j=k}A_i*B_j
$$
这时就不能使用`FFT`了

但是`FFT`使我们产生了一种想法

我们能不能用一种类似`FFT`的方法，用另一个多项式来表示$A,B$，然后再对应相乘，最后再变换回来呢

答案是可以的，这就是`FWT`，即快速沃尔什变换

## 咋搞啊

我们以或运算举例：

我们按照定义，显然可以构造 $FWT[A] = A' = \sum_{i=i|j}A_{j}$ ，来表示 $j$ 满足二进制中 $1$ 为 $i$ 的子集。

那么显然会有 $C_{i} = \sum_{i=j|k}A_{j}*B_{k} \Rightarrow FWT[C] = FWT[A] * FWT[B]$ 

至于上面这个是怎么来的：
$$
\begin{aligned}
FWT[C][i]&=FWT[A][i]*FWT[B][i]\\
\sum_{j|i}C_j&=(\sum_{j|i}A_j)*(\sum_{j|i}B_j) \\
\sum_{j|i}C_j&=\sum_{j|i,k|i} A_jB_k\\
\sum_{j|i}C_j&=\sum_{j|i}\sum_{a|b=j}A_aB_b\\
C_j&=\sum_{a|b=j}A_aB_b
\end{aligned}
$$
这样就和上面我们想要的式子一样了。

## 一堆定义/结论

别问我怎么推的，我也不知道。

在[这里](https://0x131cc05.github.io/2019/04/02/FWT%E5%9F%BA%E7%A1%80/)有详细的证明。

### 通用性质
性质1：
$$
FWT(A\pm B)=FWT(A)\pm FWT(B)
$$
性质2：

定义$\oplus$为任意集合运算
$$
FWT(A\oplus B)=FWT(A)*FWT(B)\ \ (对应相乘)
$$
### 或运算

定义：
$$
FWT(A)[i]=\sum_{j|i=i}A_j
$$
正向运算：
$$
FWT(A)=\begin{cases}(FWT(A_0),FWT(A_1)+FWT(A_0))& n>1\\\ A & n=0\end{cases}
$$
逆向运算：
$$
IFWT(A)=\begin{cases}(IFWT(A_0),IFWT(A_1)-IFWT(A_0))&n>1\\\ A&n=0\end{cases}
$$

### 与运算

定义：
$$
FWT(A)[i]=\sum_{i\&j=j}A_i
$$

正向运算：
$$
FWT(A)=\begin{cases}(FWT(A_0)+FWT(A_1),FWT(A_1))&n>1\\\ A &n=0\end{cases}
$$
逆向运算：
$$
IFWT(A)=\begin{cases}(IFWT(A_0)-IFWT(A_1),IFWT(A_1))&n>1\\\ A&n=0\end{cases}
$$

### 异或运算

定义：

令$d(x)$为$x$**在二进制下**拥有的1的数量
$$
FWT(A)[i]=\sum_{d(j\&i)为偶数}A_j-\sum_{d(k\&i)为奇数}A_k
$$

正向运算：
$$
FWT(A)=\begin{cases}(FWT(A_0)+FWT(A_1),FWT(A_0)-FWT(A_1))&n>0\\\ A&n=0\end{cases}
$$
逆向运算：
$$
IFWT(A)=\begin{cases}(\frac{IFWT(A_0+A_1)}{2},\frac{IFWT(A_0-A_1)}{2})&n>1\\\ A&n=0\end{cases}
$$

## 板子

1. 按位或
2. 按位与
3. 按位异或

```cpp
//by Harry_bh
void FWT1(long long a[],int len)
{
	for(int mid=2;mid<=len;mid<<=1)
	for(int i=0;i<len;i+=mid)
	for(int j=i;j<i+(mid>>1);j++)a[j+(mid>>1)]+=a[j];
}
void IFWT1(long long a[],int len)
{
	for(int mid=2;mid<=len;mid<<=1)
	for(int i=0;i<len;i+=mid)
	for(int j=i;j<i+(mid>>1);j++)a[j+(mid>>1)]-=a[j];
}
void FWT2(long long a[],int len)
{
	for(int mid=2;mid<=len;mid<<=1)
	for(int i=0;i<len;i+=mid)
	for(int j=i;j<i+(mid>>1);j++)a[j]+=a[j+(mid>>1)];
}
void IFWT2(long long a[],int len)
{
	for(int mid=2;mid<=len;mid<<=1)
	for(int i=0;i<len;i+=mid)
	for(int j=i;j<i+(mid>>1);j++)a[j]-=a[j+(mid>>1)];
}
void FWT3(long long a[],int len)
{
	for(int mid=2;mid<=len;mid<<=1)
	for(int i=0;i<len;i+=mid)
	for(int j=i;j<i+(mid>>1);j++)
	{
		long long x=a[j],y=a[j+(mid>>1)];
		a[j]=x+y,a[j+(mid>>1)]=x-y;
	}
}
inline void IFWT3(long long a[],int len)
{
	for(int mid=2;mid<=len;mid<<=1)
	for(int i=0;i<len;i+=mid)
	for(int j=i;j<i+(mid>>1);j++)
	{
		long long x=a[j],y=a[j+(mid>>1)];
		a[j]=(x+y)>>1,a[j+(mid>>1)]=(x-y)>>1;
	}
}
```



## 参考资料

[LSJ-FWT](https://0x131cc05.github.io/2019/04/02/FWT%E5%9F%BA%E7%A1%80/)

[OI-WIKI](https://oi-wiki.org/math/poly/fwt/)

