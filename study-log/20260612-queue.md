# Day 3 — 中缀转后缀代码 + 队列

---

## Step 1: 中缀转后缀（代码实现）

### 函数调用速查表

| 函数 | 怎么调用 | 返回什么 |
|------|---------|---------|
| `push` | `push(&s, ch)` | 无返回值 |
| `pop` | `pop(&s)` | 栈顶的值 |
| `top` | `top(&s)` | 栈顶的值（不删除） |
| `isEmpty` | `isEmpty(&s)` | 1=空, 0=非空 |
| `isp(ch)` | `isp(ch)` | 栈内优先级 |
| `icp(ch)` | `icp(ch)` | 栈外优先级 |

### 最终代码

```c
void infixToPostfix(char* out) {
    Stack s; init(&s);
    char ch;

    while ((ch = getchar()) != '\n') {
        if (ch == ' ') continue;

        if (ch >= '0' && ch <= '9') {
            printf("%c", ch);                          // 数字直接输出
        }
        else if (ch == '(') {
            push(&s, ch);                              // ( 入栈
        }
        else if (ch == ')') {
            while (top(&s) != '(')                     // 弹到 ( 为止
                printf("%c", pop(&s));
            pop(&s);                                   // 弹掉 (
        }
        else {  // + - * /
            while (!isEmpty(&s) && isp(top(&s)) >= icp(ch))
                printf("%c", pop(&s));                 // 优先级高的先出
            push(&s, ch);                              // 当前运算符入栈
        }
    }
    while (!isEmpty(&s))                               // 弹空栈
        printf("%c", pop(&s));
}
```

### BUG 记录

- **BUG1**: 只写 `push(ch)` 忘了 `&s` → 应该是 `push(&s, ch)`
- **BUG2**: 不熟悉每个函数的参数和返回值 → 对照速查表

> ✅ 已掌握

---

## Step 2: 简单队列 (Simple Queue)

### 核心概念

| | 栈 | 队列 |
|------|------|------|
| 原则 | LIFO（后进先出） | FIFO（先进先出） |
| 插入操作 | Push（入栈） | Enqueue（入队） |
| 删除操作 | Pop（出栈） | Dequeue（出队） |
| 操作位置 | 都在栈顶 | 入队动 rear，出队动 front |

### 最终代码

```c
typedef struct {
    int data[100];
    int front;
    int rear;
} Queue;

void init(Queue* q) {
    q->front = 0;
    q->rear = -1;
}

void enqueue(Queue* q, int x) {
    q->data[++(q->rear)] = x;
    // BUG记录1: 第一次写的 q->data[q->rear++] = x, rear++ 是后自增
    //          第一次 rear=-1, data[-1] 直接越界。必须用 ++(q->rear)
    // BUG记录2: 写了 q->front++，入队不该动 front
}

int dequeue(Queue* q) {
    if (isEmpty(q)) return -1;
    return q->data[(q->front)++];
}

int isEmpty(Queue* q) {
    return q->front > q->rear;
}
```

> ✅ 已掌握

---

## Step 3: 循环队列 (Circular Queue)

### 为什么需要循环队列？

简单队列的问题：出队后 front 前面的空间永远浪费。

```
enqueue 3次 dequeue 3次后：front=3, rear=2
[已用][已用][已用][  ][  ]   ← 0~2号位浪费
```

循环队列把数组当环，rear 走到末尾绕回 0。

### 空 vs 满的区分

```
空：front == rear
满：(rear + 1) % MAX == front
```

浪费一个格子来区分空和满，最多存 MAX-1 个元素。

### 最终代码

```c
#define MAX 100

typedef struct {
    int data[MAX];
    int front;
    int rear;
} Queue;

void init(Queue* q) {
    q->front = 0;
    q->rear = 0;
    // BUG记录1: 第一次写成 q->rear = -1，循环队列从 0 开始
}

void enqueue(Queue* q, int x) {
    if ((q->rear + 1) % MAX == q->front) return;  // 先判满
    // BUG记录2: 第一次先 q->rear = (...) 再判满，万一满的 rear 已经动了
    q->rear = (q->rear + 1) % MAX;
    q->data[q->rear] = x;
}

int dequeue(Queue* q) {
    if (isEmpty(q)) return -1;
    // BUG记录3: 第一次写成 q->front = (...) % MAX; return data[front];
    //          front 先移了，取到的是下一个的值。必须先取再移
    // BUG记录4: return data[front] 放在 front 移动前，return 后面的代码不执行
    int x = q->data[q->front];
    q->front = (q->front + 1) % MAX;
    return x;   // return 放最后
}

int isEmpty(Queue* q) {
    return q->front == q->rear;
    // BUG记录5: 写的 q->front > q->rear，那是简单队列的判空
}

int isFull(Queue* q) {
    return (q->rear + 1) % MAX == q->front;
}
```

### Q&A: 为什么判满和判空看起来一样？

如果 MAX 个槽全用完，`front == rear` 同时是空和满，分不清。所以循环队列浪费一个格子，用 `(rear+1)%MAX == front` 判满。

---

## 本日小结

今日完成：中缀转后缀代码 + 简单队列 + 循环队列。线性结构全部结束。
