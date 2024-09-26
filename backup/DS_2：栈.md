# 栈
定义：只允许在一端进行操作（插入、删除）的线性表
基本操作：
1. 初始化栈
2. 释放内存
3. 进栈
4. 出栈
5. 获取栈顶元素
6. 判空
## 顺序栈
实现:静态数组中存放栈元素
```C
#define Type int
#define MAX_SIZE 10
typedef struct Stack {
    // 存放栈元素
    Type data[MAX_SIZE];
    // 栈顶指针
    int top;
} Stack;

void initStack(Stack *s) {
    // 栈为空
    s->top = -1;
}

void destructorStack(Stack *s) {
    s->top = -1;
}

bool isEmpty(Stack *s) {
    return s->top == -1;
}

bool push(Stack *s, Type e) {
    if (s->top == MAX_SIZE - 1) {
        printf("Stack is full");
        return false;
    }
    s->data[++s->top] = e;
    return true;
}

bool pop(Stack *s) {
    if (isEmpty(s)) {
        printf("Stack is empty");
        return false;
    }
    s->top--;
    return true;
}

Type front(Stack*s){
    if (isEmpty(s)) {
        printf("Stack is empty");
        return false;
    }
    return s->data[s->top];
}
```
也可以将top初始化为0，这样top指向的就是下一个入栈的位置
## 共享栈
就是在顺序栈的基础上，再添加一个top标记，将一整片数组空间分为两个栈使用，top0是从数组的索引0向上，top1从数组的最大索引向下；相交的时候就是满了

## 链式栈
每个元素就是一个链表结点，实现栈的功能就行，也是分为带头结点和不带头结点
不带头结点:
```C
#define Type int
typedef struct Node {
    Type data;
    struct Node *next;
} Node;
typedef Node *stack;

void initStack(stack *s) {
    (*s) = NULL;
}

void destructor(stack *s) {
    Node *p = *s;
    while (p != NULL) {
        Node *temp = p;
        p = p->next;
        free(temp);
    }
    (*s) = NULL;
}

bool push(stack *s, Type e) {
    Node *p = *s;
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Failed to allocate memory");
        return false;
    }
    newNode->data = e;
    newNode->next = p;
    *s = newNode;
    return true;
}

bool pop(stack *s) {
    if (*s == NULL) {
        printf("Stack is empty\n");
        return false;
    }
    Node *p = *s;
    (*s) = (*s)->next;
    free(p);
    return true;
}

Type front(stack *s) {
    if (*s == NULL) {
        printf("Stack is empty\n");
        return -1;
    }
    return (*s)->data;
}
```
带头结点：
```C
#define Type int
typedef struct Node {
    Type data;
    struct Node *next;
} Node;
typedef Node *stack;

void constructor(stack *s) {
    // 分配头结点
    (*s) = (Node *) malloc(sizeof(Node));
    if (*s == NULL) {
        printf("Failed to allocate memory");
        return;
    }
    (*s)->data = -1;
    (*s)->next = NULL;
}

void destructor(stack *s) {
    if (*s == NULL) {
        return;
    }
    Node *p = *s;
    while (p != NULL) {
        Node *temp = p;
        p = p->next;
        free(temp);
    }
    (*s) = NULL;
}

bool push(stack *s, Type e) {
    if (*s == NULL) {
        printf("stack is not initialized");
        return false;
    }
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Failed to allocate memory");
        return false;
    }
    newNode->data = e;
    newNode->next = (*s)->next;
    (*s)->next = newNode;
    return true;
}

bool pop(stack *s) {
    if ((*s) == NULL || (*s)->next == NULL) {
        printf("Stack is empty\n");
        return false;
    }
    Node *p = (*s)->next;
    (*s)->next = p->next;
    free(p);
    return true;
}

Type front(stack *s) {
    if ((*s) == NULL || (*s)->next == NULL) {
        printf("Stack is empty\n");
        return -1;
    }
    return (*s)->next->data;
}
```
代码地址：https://github.com/proacane/DataStructure