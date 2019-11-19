# 数论

## 米勒拉宾

  - 可以调节`const int S=20;`来设置不同的精度.

```c++
const int S=20;
LL mult_mod(LL a,LL b,LL c){
	a%=c;
	b%=c;
	long long ret=0;
	while(b){
		if(b&1){ret+=a;ret%=c;}
		a<<=1;
		if(a>=c)a%=c;
		b>>=1;
	}
	return ret;
}
LL pow_mod(LL x,LL n,LL mod){
	if(n==1)return x%mod;
	x%=mod;
	LL tmp=x;
	LL ret=1;
	while(n){
		if(n&1) ret=mult_mod(ret,tmp,mod);
		tmp=mult_mod(tmp,tmp,mod);
		n>>=1;
	}
	return ret;
}
bool check(LL a,LL n,LL x,LL t){
	LL ret=pow_mod(a,x,n);
	LL last=ret;
	range(i,1,t){
		ret=mult_mod(ret,ret,n);
		if(ret==1&&last!=1&&last!=n-1) return true;
		last=ret;
	}
	if(ret!=1) return true;
	return false;
}
bool Miller_Rabin(LL n){
	if(n<2)return false;
	if(n==2)return true;
	if((n&1)==0) return false;
	LL x=n-1;
	LL t=0;
	while((x&1)==0){x>>=1;t++;}
	range(i,0,S-1){
		LL a=rand()%(n-1)+1;
		if(check(a,n,x,t))return false;
	}
	return true;
}
LL factor[100];
int tol;
LL gcd(LL a,LL b){
	if(a==0)return 1;
	if(a<0) return gcd(-a,b);
	while(b){
		long long t=a%b;
		a=b;
		b=t;
	}
	return a;
}
LL Pollard_rho(LL x,LL c){
	LL i=1,k=2;
	LL x0=rand()%x;
	LL y=x0;
	while(1){
		i++;
		x0=(mult_mod(x0,x0,x)+c)%x;
		LL d=gcd(y-x0,x);
		if(d!=1&&d!=x) return d;
		if(y==x0) return x;
		if(i==k){y=x0;k+=k;}
	}
}
void findfac(LL n){
	if(Miller_Rabin(n)){
		factor[tol++]=n;
		return;
	}
	LL p=n;
	while(p>=n)p=Pollard_rho(p,rand()%(n-1)+1);
	findfac(p);
	findfac(n/p);
}
```

## 快速幂

```c++
template <class T>
T quick_pow(T a, unsigned int n, int mod = 0) {
    T res = 1;
    while (n) {
        if (n & 1)mod ? res = res * a % mod : res = res * a;
        mod ? a = a * a % mod : a = a * a;
        n >>= 1;
    }
    return res;
}
```

## gcd

```c++
int gcd(int a,int b) {
    return b ? gcd(b, a % b) : a;
}
```

## exgcd

```c++
//拓展欧几里德求解ax+by=gcd(a,b)的一组解
LL ex_gcd(LL a,LL b,LL &x,LL &y){
    if (b==0) {
        x=1,y=0;
        return a;
    }
    else{
        LL r=ex_gcd(b,a%b,y,x);
        y-=x*(a/b);
        return r;
    }
}
//返回的bool值表示该方程是否有解
bool linear_equation(int a,int b,int c,int &x,int &y){
    int d=ex_gcd(a,b,x,y);
    if (c%d) return false;
    int k=c/d;
    x*=k;y*=k;
    return true;
}
```