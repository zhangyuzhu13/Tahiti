title: Exponentiating by Squaring(快速幂)
date: 2018-08-23 14:16:00
categories: Algorithm
mathjax: true

---



###  Composition Mod

##### power (a , b) % m in O(n)

For every number b, we have the representation of b in this way: 

$$\sum _ { i = 0 } ^ { n - 1 } b _ { i } 2 ^ { i } = b _ { 0 } 2 ^ { 0 } + b _ { 1 } 2 ^ { 1 } + b _ { 2 } 2 ^ { 2 } + \cdots + b _ { n - 1 } 2 ^ { n - 1 }$$

Where bi represent the binary number of b at index i. 

$a ^ { b } = a ^ { \sum _ { i = 0 } ^ { n - 1 } b _ { i } 2 ^ { i } } = a ^ { b _ { 0 } \times 2 ^ { 0 } } \times a ^ { b _ { 1 } \times 2 ^ { 1 }}  \times a ^ { b _ { 2 } \times 2 ^ { 2 }}  \dots a ^ { b _ { n - 1 } \times 2 ^ { n - 1 }} $  

And for each adjacent pair, they have relation like:

$ a ^ { 2 ^ { i + 1 } } = \left( a ^ { 2 ^ { i } } \right) ^ { 2 }$ 

law:

$ a ^ { b } \% c = ( a \% c ) ^ { b } \% c$ 

如果x无法被p整除，还有一个规律成立, $x ^ { p - 1 } \equiv 1 ( \bmod p )$, 利用这条性质，也可以求一个数的逆元。等式两边都乘以$x ^ {-1}$, 得到$x ^ {-1} =x ^ {p−2} (mod p)$, 所以在p是素数的时候（很多情况下都是如此），一个数的逆元，就等于这个数的p-2次方(mod p), 所以，使用快速幂运算就能求出逆元。 

```java
int quick_power_mod(int a,int b,int m){ 
	int result = 1; // only record the mod when bi == 1
	int base = a; // record the mod of each bi
	while (b > 0) { 
		if (b & 1 == 1) {
        	result = (result*base) % m; 
        } 
        base = (base*base) %m; 
        b >>=1; 
    } 
    return result; 
}
// 递归写法，求逆元
int inv(int a) {  
    //return fpow(a, MOD-2, MOD);  
    return a == 1 ? 1 : (long long)(MOD - MOD / a) * inv(MOD % a) % MOD;  
}
```

对于$( m ! ( n - m ) ! )$ , 逆元为$(m!(n-m)!) ^ {p-2}$  

费马小定理

 ```java
public int C(long n,long m, long MOD)  
{  
    if(m < 0)return 0;  
    if(n < m)return 0;  
    if(m > n-m) m = n-m;  

    long up = 1, down = 1;  
    for(long i = 0 ; i < m ; i ++){  
        up = up * (n-i) % MOD;  
        down = down * (i+1) % MOD;  
    }  
    return up * inv(down) % MOD;  
}

 ```

Lucas定理：

A、B是非负整数，p是质数。A B写成p进制：A=a[n]a[n-1]…a[0]，B=b[n]b[n-1]…b[0]。   

则组合数C(A,B)与C(a[n],b[n])C(a[n-1],b[n-1])…*C(a[0],b[0]) mod p同余   即：Lucas(n,m,p)=C(n%p,m%p)\*Lucas(n\p,m\p,p) 
