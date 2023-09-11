## Stack ADT
- 栈（stack）是受限的序列
	- 只能在栈顶（top）插入和删除
	- 栈底（bottom）为盲端

- 基本接口
	- size () / empty ()
	- push () 入栈
	- pop () 出栈
	- top () 查顶

- 后进先出（LIFO）

### 扩展接口 ：getMax ()
使用一个辅助栈来跟踪最大元素：
1. 维护两个栈，一个是主栈用于正常的 push 和 pop 操作，另一个是辅助栈用于跟踪最大元素。
2. 当往主栈中压入一个元素时，首先检查辅助栈是否为空。如果为空，就将这个元素同时压入辅助栈。
3. 如果辅助栈不为空，就比较要压入主栈的元素与辅助栈栈顶元素的大小。如果新元素大于等于栈顶元素，就将新元素压入辅助栈。否则，不将新元素压入辅助栈，保持辅助栈栈顶元素不变。
4. 当你主栈中弹出元素时，同时也从辅助栈中弹出元素。这样，辅助栈始终反映了主栈中的最大元素。
5. 如果你需要查找栈中的最大元素，只需查看辅助栈的栈顶元素，它将是当前主栈中的最大元素。

```
class MaxStack {
public:
    void push(int x) {
        // 压入主栈
        mainStack.push(x);

        // 检查辅助栈是否为空，或者新元素大于等于辅助栈的栈顶元素
        if (maxStack.empty() || x >= maxStack.top()) {
            maxStack.push(x);
        }
    }

    void pop() {
        if (!mainStack.empty()) {
            // 如果主栈的栈顶元素等于辅助栈的栈顶元素，同时从辅助栈弹出元素
            if (mainStack.top() == maxStack.top()) {
                maxStack.pop();
            }
            mainStack.pop();
        }
    }

    int getMax() {
        if (!maxStack.empty()) {
            return maxStack.top();
        }
        // 如果辅助栈为空，栈中没有元素，可以根据需要返回一个默认值或抛出异常
        // 这里简单地返回0，你可以根据实际需求进行更改
        return 0;
    }

private:
    std::stack<int> mainStack;  // 主栈用于正常的push和pop操作
    std::stack<int> maxStack;   // 辅助栈用于跟踪最大元素
};

```

## 函数调用栈
### 原理
![[40-Stack_Queue-func-call-stack1.png]]
![[40-Stack_Queue-func-call-stack.png]]

### 深度与空间
#### 递归法求阶乘
```
int fac(int n){
	return (n<2)?1:n*fac(n-1);}
}
```

![[40-Stack_Queue-factorial.png]]

#### 递归求 fib 序列
```
int fib(int n){
	return (n<2)?n:fib(n-1)+fib(n-2);
}
```

![[40-Stack_Queue-fib.png]]

#### 空间复杂度
递归算法所需的空间，主要取决于递归深度，而非递归实例总数：
```
hailstone(int n) {
	if ( 1 < n )
	n % 2 ? odd( n ) : even( n );
}
even( int n ) { hailstone( n / 2 ); }
odd( int n ) { hailstone( 3*n + 1 ); }

main( int argc, char* argv[] )
{ hailstone( atoi( argv[1] ) ); }

```

![[40-Stack_Queue-hailstone.png]]

### 消除递归
递归函数的空间复杂度 
- 主要取决于最大递归深度 
- 而非递归实例总数 

为隐式地维护调用栈，需花费额外的时间、空间——为节省空间，可 
- 显式地维护调用栈 
- 将递归算法改写为迭代版本

```
// version: recurrence
int fac( int n ) {
	int f = 1; //O(1)空间
	while ( n > 1 )
		f *= n--;
	return f;
}

int fib( int n ) { //O(1)空间
	int f = 0, g = 1;
	while ( 0 < n-- )
		{ g += f; f = g - f; }
	return f;
}

void hailstone( int n ) { //O(1)空间
	while ( 1 < n )
		n = n % 2 ? 3*n + 1 : n/2;
}
```

### 尾递归
#### 定义
- 尾递归指递归调用在函数体最后一步的递归调用。
- 递归求 factorial 的最后一步是乘法与函数的自我调用。
![[40-Stack_Queue-recursion-types.png]]

#### 特点
尾递归是最简单的递归模式：一旦抵达递归基，便会引发一连串的 return（且返回地址相同），调用栈相应地连续 pop。

故不难改写为迭代形式，越来越多的编译器可以自动识别并代为改写

- 时间复杂度有常系数改进
- 空间复杂度或有渐近改进

#### 消除尾递归
![[40-Stack_Queue-fac-recursion-to-recurrence.png]]

## 进制转换
λ进制的数可以表示为：
$n=(d_{m}...d_{2}d_{1}d_{0})_{\lambda}=d_{m}\cdot\lambda^{m}+...+d_{2}\cdot\lambda^{2}+d_{1}\cdot\lambda^{1}+d_{0}\cdot\lambda^{0}$

则令：$n_{i}=(d_{m}...d_{i+2}d_{i+1}d_{i})_{\lambda}$

有：$n_{i+1}=n_{i}/\lambda$ 和 $d_{i}=n_{i} \% \lambda$
如此对λ反复整除、取余，即可自低而高得出λ进制的各位。
## 栈混洗

## 中缀表达式

## 逆波兰表达式

## Queue ADT

## 直方图最大矩形

## 双栈当队