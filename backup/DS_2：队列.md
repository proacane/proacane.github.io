# 队列
只允许一端进入，另一端删除的线性表
基本操作：
1. 初始化
2. 释放内存
3. 入队
4. 出队
5. 获取队头元素
6. 判空
## 顺序存储实现队列
内部使用连续内存的数组，采用循环队列的格式
```C
#define MAX_SIZE 10
#define Type int
typedef struct {
    Type data[MAX_SIZE];
    // 队头指针
    int front;
    // 队尾指针
    int rear;
    // 添加 size，来维护队列的长度，用于判断队列是否已满
    int size;
} Queue;

void constructor(Queue *q) {
    q->front = 0;
    q->rear = 0;
}

bool isEmpty(Queue *q) {
    return q->front == q->rear;
}

bool isFull(Queue *q) {
    return (q->rear + 1) % MAX_SIZE == q->front;
}

void push(Queue *q, Type e) {
    // 如果队列已满则不能添加
    if (isFull(q)) {
        printf("Queue is full");
        return;
    }
    q->data[q->rear] = e;
    q->rear = (q->rear + 1) % MAX_SIZE;
}

Type pop(Queue*q){
    if(isEmpty(q)){
        return -1;
    }
    Type t = q->data[q->front];
    q->front = (q->front+1)%MAX_SIZE;
    return t;
}
```

> 在判断队列是否已满，或是否空的时候，可以采用不同的方式，一种就是上面代码所用的方式，也可以添加 size 成员，记录当前队列的有效元素数量，通过 size == MAX_SIZE 和 size == 0 来判断队列状态；
> 另一种有效的方式是添加 tag 标记，0和1分别表示上次成功的操作为出队和入队，当 front == rear && tag == 0，就代表队列空了， front == rear && tag == 1就代表队列已满
## 链式队列
链式存储依然分为有头结点和无头结点两种方式，链式存储没有判空、判满的必要
无头结点：
```C
#include <stdio.h>
#include <malloc.h>
#include <stdbool.h>

#define Type int

typedef struct Node {
    struct Node *next;
    Type data;
} Node;
typedef struct {
    Node *front;
    Node *rear;
} Queue;

void constructor(Queue *q) {
    (*q).front = NULL;
    (*q).rear = NULL;
}

bool isEmpty(Queue *q) {
    return q->front == NULL;
}

void destructor(Queue *q) {
    Node *p = (*q).front;
    while (p != NULL) {
        Node *temp = p;
        p = p->next;
        free(temp);
    }
    (*q).front = NULL;
    (*q).rear = NULL;  // 确保队列的 rear 也设置为 NULL
}

void push(Queue *q, Type e) {
    if (q == NULL) {
        printf("Queue is not initialized\n");
        return;
    }
    // 插入到队尾
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Can't allocate memory");
        return;
    }
    newNode->next = NULL;
    newNode->data = e;
    // 如果没有结点
    if (q->front == NULL) {
        q->front = newNode;
        q->rear = newNode;
        printf("Inserted %d as the first element\n", e);
    } else {
        q->rear->next = newNode;
        q->rear = newNode;
        printf("Inserted %d at the rear of the queue\n", e);
    }
}

Type pop(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue is empty, can't pop\n");
        return -1;
    }
    Node *popNode = q->front;
    q->front = q->front->next;
    Type v = popNode->data;
    if (q->front == NULL) {
        q->rear = NULL;
        printf("Queue is now empty after popping %d\n", v);
    } else {
        printf("Popped %d from the queue\n", v);
    }
    free(popNode);
    return v;
}
```
带头结点:
```C
#define Type int

typedef struct Node {
    struct Node *next;
    Type data;
} Node;

typedef struct {
    Node *front;
    Node *rear;
} Queue;

void constructor(Queue *q) {
    // 创建头结点
    q->front = q->rear = (Node *) malloc(sizeof(Node));
    q->front->next = NULL;
}

void destructor(Queue *q) {
    Node *p = q->front;
    while (p != NULL) {
        Node *temp = p;
        p = p->next;
        free(temp);
    }
    (*q).front = NULL;
    (*q).rear = NULL;  // 确保队列的 rear 也设置为 NULL
}

bool isEmpty(Queue *q) {
    return q->front == q->rear;
}

void push(Queue *q, Type e) {
//    printf("Pushing element: %d\n", e);  // 日志记录
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Can't allocate memory");
        return;
    }
    newNode->next = NULL;
    newNode->data = e;
    q->rear->next = newNode;
    q->rear = newNode;
    printf("Element pushed: %d\n", e);  // 日志记录
}

Type pop(Queue*q){
    if(isEmpty(q)){
        printf("Queue is empty, cannot pop.\n");  // 日志记录
        return -1;
    }
    Node* popNode = q->front->next;
    Type v = popNode->data;
//    printf("Popping element: %d\n", v);  // 日志记录
    q->front->next = popNode->next;
    // 如果只有一个结点
    if(q->rear == popNode){
        q->rear = q->front;
    }
    free(popNode);
    printf("Element popped: %d\n", v);  // 日志记录
    return v;
}
```