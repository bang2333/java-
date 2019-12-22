### 剑指offer

利用俩个栈实现队列：

评论区的答案

![123](./imgs/垃圾代码1.png)

???

```java
Stack<Integer> stack1 = new Stack<Integer>();
Stack<Integer> stack2 = new Stack<Integer>();
int res = 0;

public void push(int node) {
    stack1.push(node);
}

public int pop(){
    if (stack1.empty()) throw  new RuntimeException("null");
    while (!stack1.empty()) res = stack2.push(stack1.pop());    stack2.pop();
    while (!stack2.empty()) stack1.push(stack2.pop());  return res;
}
```


