# 线性表
线性表是具有相同数据类型的n（n≥0）个数据元素的**有限序列**，其中n为表长，当n = 0时线性表是一个空表。
基本操作：
1. 初始化：分配内存空间
2. 销毁：释放内存
3. 插入：第i个位置插入指定元素e
4. 删除：删除第i个位置的元素，用参数e返回删除元素的值
5. 按值查找：查找值为e的元素的位置
6. 按位查找：查找第i个元素的值
7. 求表长
8. 输出
9. 判空

## 顺序表
用顺序存储的方式实现线性表，即把逻辑上相邻的元素存储在物理位置上也相邻的存储单元中，元素之间的关系由存储单元的邻接关系来体现。类似于数组
![image](https://github.com/user-attachments/assets/93c9e5f4-5257-48d9-8ede-c9a410369491)
### 顺序表实现
1. 静态分配：在初始化时就指定好顺序表的内存大小
C语言分配内存：malloc(分配的连续内存大小)，返回一个指针，需要强转为指定类型，free用来释放内存
2. 动态分配：初始化时内部存放指针，表示内存大小不定

静态分配C语言代码：
```c
#include <stdio.h>
#include <assert.h>

#define MAX_SIZE 10
#define Type int
typedef struct {
    Type data[MAX_SIZE];
    // 有效元素的个数
    int len;
} SeqTable;

// 初始化
void initTable(SeqTable *t) {
    t->len = 0;
    for (int i = 0; i < MAX_SIZE; ++i) {
        t->data[i] = 0;
    }
}

// 销毁
void freeTable(SeqTable *t) {
    // 无需手动回收空间
    for (int i = 0; i < t->len; ++i) {
        t->data[i] = 0;
    }
    t->len = 0;
}

/**
 * 元素e插入到索引i，i从0开始，到 t->len 为止
 * @param t
 * @param i
 * @param e
 * @return
 */
int insertInto(SeqTable *t, int i, Type e) {
    // i 的位置不合法
    if (i < 0 || i > t->len) {
        return -1;
    }
    // 存满了
    if (t->len > MAX_SIZE) {
        return -1;
    }
    for (int j = t->len; j > i; j--) {
        t->data[j] = t->data[j - 1];
    }
    t->data[i] = e;
    t->len++;
    return 1;
}

/**
 * 删除索引i的元素，e用来返回
 * @param t
 * @param i
 * @param e
 * @return
 */
int deleteEle(SeqTable *t, int i, Type *e) {
    if (i < 0 || i >= t->len) {
        return -1;
    }
    *e = t->data[i];
    for (int j = i; j < t->len - 1; j++) {
        t->data[j] = t->data[j + 1];
    }
    t->len--;
    return 1;
}

void print(const SeqTable *t) {
    printf("Length is :%d", t->len);
    printf("[");
    for (int i = 0; i < t->len; i++) {
        if (i == t->len - 1) {
            printf("%d]\n", t->data[i]);
            return;
        }
        printf("%d, ", t->data[i]);
    }
}

/**
 * 查找e的索引
 * @param t
 * @param e
 * @return
 */
int getElementPos(SeqTable *t, Type e) {
    if (t->len == 0) {
        return -1;
    }
    for (int i = 0; i < t->len; i++) {
        if (t->data[i] == e) {
            printf("Element %d position is: %d\n", e, i);
            return i;
        }
    }
    return -1;
}
/**
 * 查找i索引的元素
 * @param t
 * @param i
 * @return
 */
Type getElement(SeqTable *t, int i) {
    if (i > t->len || i < 0) {
        return -1;
    }
    printf("index %d element is %d\n", i, t->data[i]);
    return t->data[i];
}

int main() {
    SeqTable t;
    initTable(&t);
    assert(insertInto(&t, 0, 3) == 1);
    assert(insertInto(&t, 0, 5) == 1);
    assert(insertInto(&t, 1, 6) == 1);
    assert(insertInto(&t, 1, 10) == 1);
    print(&t);
    Type e;
    assert(deleteEle(&t, 1, &e) == 1);
    printf("Delete element is %d\n",e);
    assert(getElementPos(&t, 5) != -1);
    assert(getElement(&t, 3) != -1);
    print(&t);
}
```
动态分配C语言代码：
```c
#include <stdio.h>
#include <malloc.h>
#include <assert.h>

#define Type int
#define INITSIZE 10
typedef struct {
    Type *data;
    // 最大容量
    int max_size;
    // 已有元素个数
    int len;
} SeqTable;

void initTable(SeqTable *t) {
    // 分配内存
    t->data = (Type *) malloc(INITSIZE * sizeof(Type));
    t->max_size = INITSIZE;
    for (int i = 0; i < t->max_size; i++) {
        t->data[i] = 0;
    }
    t->len = 0;
}

void freeTable(SeqTable *t) {
    // 清理内存
    t->max_size = 0;
    t->len = 0;
    free(t->data);
    t->data = NULL;
}

int isFull(SeqTable *t) {
    if (t->len == t->max_size) {
        return 1;
    } else {
        return 0;
    }
}

int isEmpty(SeqTable *t) {
    if (t->len == 0 && t->max_size != 0) {
        return 1;
    } else {
        return 0;
    }
}

void increase(SeqTable *t, int len) {
    // 备份原数据
    Type *back = t->data;
    t->data = (Type *) (malloc((len + INITSIZE) * sizeof(Type)));
    for (int i = 0; i < t->len; i++) {
        t->data[i] = back[i];
    }
    printf("Before increase size, size is : %d\n", t->max_size);
    t->max_size += len;
    printf("After increase size, size is : %d", t->max_size);
    free(back);
    back = NULL;
}

/**
 * 插入e到i索引
 * @param t
 * @param i
 * @param e
 * @return
 */
int insertInto(SeqTable *t, int i, Type e) {
    if (i < 0 || i > t->len) {
        return -1;
    }
    if (isFull(t)) {
        return -1;
    }
    for (int j = t->len; j > i; j--) {
        t->data[j] = t->data[j - 1];
    }
    t->len++;
    t->data[i] = e;
    return 1;
}

/**
 * 删除i索引的元素
 * @param t
 * @param i
 * @param e
 * @return
 */
int deleteEle(SeqTable *t, int i, Type *e) {
    if(isEmpty(t)){
        return -1;
    }
    if (i < 0 || i > t->len) {
        return -1;
    }
    *e = t->data[i];
    for (int j = i; j < t->len - 1; j++) {
        t->data[j] = t->data[j + 1];
    }
    t->len--;
    return 1;
}

void print(const SeqTable *t) {
    printf("Length is :%d, max size is %d ", t->len,t->max_size);
    printf("[");
    for (int i = 0; i < t->len; i++) {
        if (i == t->len - 1) {
            printf("%d]\n", t->data[i]);
            return;
        }
        printf("%d, ", t->data[i]);
    }
}

/**
 * 查找e的索引
 * @param t
 * @param e
 * @return
 */
int getElementPos(SeqTable *t, Type e) {
    if(isEmpty(t)){
        return -1;
    }
    for (int i = 0; i < t->len; i++) {
        if (t->data[i] == e) {
            printf("Element %d position is: %d\n", e, i);
            return i;
        }
    }
    return -1;
}
/**
 * 查找i索引的元素
 * @param t
 * @param i
 * @return
 */
Type getElement(SeqTable *t, int i) {
    if(isEmpty(t)){
        return -1;
    }
    if (i > t->len || i < 0) {
        return -1;
    }
    printf("index %d element is %d\n", i, t->data[i]);
    return t->data[i];
}

int main() {
    SeqTable t;
    initTable(&t);
    assert(insertInto(&t, 0, 3) == 1);
    assert(insertInto(&t, 0, 5) == 1);
    assert(insertInto(&t, 1, 6) == 1);
    assert(insertInto(&t, 1, 10) == 1);
    print(&t);
    Type e;
    assert(deleteEle(&t, 1, &e) == 1);
    printf("Delete element is %d\n",e);
    assert(getElementPos(&t, 5) != -1);
    assert(getElement(&t, 3) != -1);
    print(&t);
    freeTable(&t);
}
```
## 单链表
![image](https://github.com/user-attachments/assets/06700b48-9440-435a-b084-29adae3a1b3b)
可以带头结点，也可以不带，带头结点对链表的操作会比较方便
### 单链表实现
1. 初始化
2. 插入：按位置插入（i从0开始），指定结点的后插，指定结点的前插
3. 删除：按位序删除，删除指定结点
4. 内存回收
5. 查找：按值、按位
6. 头插法、尾插法
7. 反转链表

不带头结点:
```c
#include <stdio.h>
#include <stdbool.h>
#include <malloc.h>

#define  Type int
typedef struct {
    struct Node *next;
    Type data;
} Node;
typedef Node *List;

bool initList(List *l) {
    *l = NULL;
    return true;
}

void destructor(List *l) {
    // 从后往前都得清理
    Node *p = *l;
    while (p != NULL) {
        Node *temp = p;
        p = p->next;
        free(temp);
        temp = NULL;
    }
    *l = NULL;
}

bool insert(List *l, int i, Type e) {
    if (i < 0) {
        return false;
    }
    if (i == 0) {
        // 插入到第一个
        Node *newNode = (Node *) malloc(sizeof(Node));
        if (newNode == NULL) {
            return false;
        }
        newNode->data = e;
        newNode->next = *l;
        *l = newNode;
        printf("Inserted %d at position 0\n", e);
        return true;
    }
    // 当前所在的结点
    Node *p = *l;
    int j = 0;
    // 找到第 i-1 个结点
    while (p != NULL && j < i - 1) {
        p = (Node *) p->next;
        j++;
    }
    if (p == NULL) {
        return false;
    }
    // 插到 j(*p) 的下一个
    Node *newNode = (Node *) (malloc(sizeof(Node)));
    if (newNode == NULL) {
        return false;
    }
    newNode->data = e;
    newNode->next = p->next;
    p->next = newNode;
    printf("Inserted %d at position %d\n", e, i);
    return true;
}

void print(List *l) {
    Node *p = *l;
    while (p != NULL) {
        printf("%d", p->data);
        p = p->next;
        if (p != NULL) {
            printf("->");
        }
    }
    printf("\n");
}
// p结点后面插入
bool insertAfterNode(Node *p, Type e) {
    if (p == NULL) {
        return false;
    }
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        return false;
    }
    newNode->data = e;
    newNode->next = p->next;
    p->next = newNode;
    printf("Inserted %d after %d\n", e, p->data);
    return true;
}
// p 结点前面插入，这是传入原链表的方法
bool insertBeforeNode1(List *l, Node *p, Type e) {
    if (p == NULL || *l == NULL) {
        return false;
    }
    // 插入到第一个结点
    if (*l == p) {
        return insert(l, 0, e);
    }
    Node *t = *l;
    while (t->next != p && t->next != NULL) {
        t = t->next;
    }
    if (t->next == NULL) {
        return false; // p不是链表的一部分
    }
    // 插到 t 的后面
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        return false;
    }
    newNode->data = e;
    newNode->next = p;
    t->next = newNode;
    printf("Inserted %d before %d\n", e, p->data);
    return true;
}
// p 结点前面插入，这是不使用原链表的方法
bool insertBeforeNode2(Node *p, Type e) {
    if (p == NULL) {
        return false;
    }
    Node *s = (Node *) malloc(sizeof(Node));
    if (s == NULL) {
        return false;
    }
    s->next = p->next;
    // 插到p后面元素交换
    p->next = s;
    s->data = p->data;
    p->data = e;
    return true;
}

bool removeEle(List *l, int i, Type *e) {
    if (i < 0 || *l == NULL) {
        return false;
    }
    Node *p = *l;
    if (i == 0) {
        *l = p->next;
        *e = p->data;
        free(p);
        printf("Removed element %d from position 0\n", *e);
        return true;
    }
    int j = 0;
    while (p != NULL && j < i - 1) {
        p = p->next;
        j++;
    }
    if (p == NULL || p->next == NULL) {
        return false;
    }
    // 删除p 的下一个
    Node *temp = p->next;
    *e = temp->data;
    p->next = temp->next;
    free(temp);
    printf("Removed element %d from position %d\n", *e, i);
    return true;
}

bool removeNode1(List *l, Node *p) {
    if (p == NULL || *l == NULL) {
        return false;
    }
    if (*l == p) {
        *l = p->next;
        free(p);
        printf("Removed Node from position 0\n");
        return true;
    }
    Node *q = *l;
    while (q != NULL && q->next != p) {
        q = q->next;
    }
    if (q == NULL) {
        return false;
    }
    q->next = p->next;
    free(p);
    printf("Removed Node with data\n");
    return true;
}

bool removeNode2(Node *p) {
    if (p == NULL) {
        return false;
    }
    Node *q = p->next;
    if (q == NULL) {
        free(q);
        printf("Removed Node and replaced data with next node\n");
        return true;
    }
    p->data = q->data;
    p->next = q->next;
    free(q);
    printf("Removed Node and replaced data with next node\n");
    return true;
}

Node *getElementByPos(List *l, int i) {
    if (i < 0 || *l == NULL) {
        return NULL;
    }
    int j = 0;
    Node *p = *l;
    while (p != NULL && j < i) {
        p = p->next;
        j++;
    }
    return p;
}

Node *getElementByValue(List *l, Type e) {
    if (*l == NULL) {
        return NULL;
    }
    Node *p = *l;
    while (p != NULL && p->data != e) {
        p = p->next;
    }

    return p;
}

int length(List*l){
    if(*l == NULL){
        return 0;
    }
    int res = 0;
    Node* p = *l;
    while(p!= NULL){
        res++;
        p = p->next;
    }
    return res;
}
// 尾插法创建单链表
bool insertTail(List *l) {
    int x;
    printf("Enter value (9999 to quit): ");
    scanf("%d", &x);

    // 初始化链表
    if (x == 9999) {
        return true; // 如果一开始就退出，不需要创建链表
    }
    Node *head = (Node *) malloc(sizeof(Node));
    if (head == NULL) {
        return false; // 内存分配失败
    }
    head->data = x;
    head->next = NULL;
    *l = head; // 将头结点赋给链表
    // q是最后一个结点
    Node *q = head;
    // 循环插入数据
    while (true) {
        printf("Enter value (9999 to quit): ");
        scanf("%d", &x);
        if (x == 9999) {
            break;
        }
        Node *newNode = (Node *) malloc(sizeof(Node));
        newNode->data = x;
        newNode->next = NULL;
        q->next = newNode;
        q = newNode;
    }
    return true;
}
// 头插法
bool insertHead(List *l) {
    Node *p;
    int x;
    printf("Enter value (9999 to quit): ");
    scanf("%d", &x);
    if (x == 9999) {
        return true ;
    }
    *l = (Node *) malloc(sizeof(Node));
    (*l)->next = NULL;
    p = *l;
    p->data = x;
    while(true){
        printf("Enter value (9999 to quit): ");
        scanf("%d", &x);
        if(x== 9999){
            break;
        }
        p = (Node *) malloc(sizeof(Node));
        p->data = x;
        p->next = *l;
        *l = p;
    }
    return true;
}
void reverse(List *l) {
    if (*l == NULL || (*l)->next == NULL) {
        return;
    }
    Node *cur = *l;
    Node *pre = NULL;
    Node *temp;
    while (cur != NULL) {
        temp = cur->next;
        cur->next = pre;
        pre = cur;
        cur = temp;
    }
    *l = pre;
}
```
带头结点:
```c
#include <stdio.h>
#include <stdbool.h>
#include <malloc.h>

#define  Type int
typedef struct {
    struct Node *next;
    Type data;
} Node;
typedef Node *List;

bool initList(List *l) {
    *l = (Node *) (malloc(sizeof(Node)));
    if (*l == NULL) {
        return false;
    } else {
        (*l)->next = NULL;
        return true;
    }
}

void destructor(List *l) {
    Node *p = (*l);
    while (p != NULL) {
        Node *temp = p;
        p = p->next;
        free(temp);
    }
    (*l) = NULL;
}

void print(List *l) {
    Node *p = (*l)->next;
    while (p != NULL) {
        printf("%d", p->data);
        p = p->next;
        if (p != NULL) {
            printf("->");
        }
    }
    printf("\n");
}

bool insert(List *l, int i, Type e) {
    if (i < 1) {
        return false;
    }
    Node *p = *l;
    int j = 0;
    while (p != NULL && j < i - 1) {
        p = p->next;
        j++;
    }
    if (p == NULL) {
        return false;
    }
    Node *newNode = (Node *) (malloc(sizeof(Node)));
    if (newNode == NULL) {
        return false;
    }
    newNode->data = e;
    newNode->next = p->next;
    p->next = newNode;
    printf("Inserted %d at position %d\n", e, i);
    return true;
}
// p结点后面插入
bool insertAfterNode(Node *p, Type e) {
    if (p == NULL) {
        return false;
    }
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        return false;
    }
    newNode->data = e;
    newNode->next = p->next;
    p->next = newNode;
    printf("Inserted %d after %d\n", e, p->data);
    return true;
}
// p 结点前面插入，这是传入原链表的方法
bool insertBeforeNode1(List *l, Node *p, Type e) {
    if (p == NULL || *l == NULL) {
        return false;
    }
    Node *t = *l;
    while (t->next != p) {
        t = t->next;
        if (t == NULL) {
            return false;
        }
    }
    // 插到 t 的后面
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        return false;
    }
    newNode->data = e;
    newNode->next = p;
    t->next = newNode;
    printf("Inserted %d before %d\n", e, p->data);
    return true;
}
// p 结点前面插入，这是不使用原链表的方法
bool insertBeforeNode2(Node *p, Type e) {
    if (p == NULL) {
        return false;
    }
    Node *s = (Node *) malloc(sizeof(Node));
    if (s == NULL) {
        return false;
    }
    s->next = p->next;
    // 插到p后面元素交换
    p->next = s;
    s->data = p->data;
    p->data = e;
    printf("Inserted %d before %d\n", e, s->data);
    return true;
}

bool removeEle(List *l, int i, Type *e) {
    if (i < 1) {
        return false;
    }
    Node *p = *l;
    int j = 0;
    while (p != NULL && j < i - 1) {
        p = p->next;
        j++;
    }
    if (p == NULL || p->next == NULL) {
        return false;
    }
    // 删除p 的下一个
    Node *temp = p->next;
    *e = temp->data;
    p->next = temp->next;
    free(temp);
    temp = NULL;
    printf("Removed element %d from position %d\n", *e, i);
    return true;
}

bool removeNode1(List *l, Node *p) {
    if (p == NULL || (*l)->next == NULL || p == *l) {
        return false;
    }

    Node *q = *l;
    while (q != NULL && q->next != p) {
        q = q->next;
    }
    if (q == NULL || q->next == NULL) {
        return false;
    }
    // 移除q的下一个，也就是p
    q->next = p->next;
    free(p);
    printf("Removed Node\n");
    return true;
}

bool removeNode2(Node *p) {
    if (p == NULL ) {
        return false;
    }
    Node *q = p->next;
    if(q == NULL){
        free(q);
        printf("Removed Node and replaced data with next node\n");
        return true;
    }
    p->data = q->data;
    p->next = q->next;
    free(q);
    printf("Removed Node and replaced data with next node\n");
    return true;
}

Node *getElementByPos(List *l, int i) {
    if (i < 1 || *l == NULL) {
        return NULL;
    }
    int j = 0;
    Node *p = *l;
    while (p != NULL && j < i) {
        p = p->next;
        j++;
    }
    return p;
}

Node *getElementByValue(List *l, Type e) {
    if (*l == NULL) {
        return NULL;
    }
    Node *p = (*l)->next;
    while (p != NULL && p->data != e) {
        p = p->next;
    }
    return p;
}

int length(List*l){
    if(*l == NULL){
        return 0;
    }
    int res = 0;
    Node* p = (*l)->next;
    while(p!= NULL){
        res++;
        p = p->next;
    }
    return res;
}
bool insertTail(List *l) {
    *l = (Node *) malloc(sizeof(Node));
    (*l)->next = NULL;
    int x;
    // q指向尾结点
    Node *p,*q = *l;
    printf("Enter value (9999 to quit): ");
    scanf("%d", &x);
    while(x!= 9999){
        p = (Node *) malloc(sizeof(Node));
        p->data = x;
        p->next = NULL;
        q->next = p;
        q = p;
        printf("Enter value (9999 to quit): ");
        scanf("%d", &x);
    }
    return true;
}

bool insertHead(List *l) {
    Node *p;
    int x;
    *l = (Node *) malloc(sizeof(Node));
    (*l)->next = NULL;
    printf("Enter value (9999 to quit): ");
    scanf("%d", &x);
    while (x != 9999) {
        p = (Node *) malloc(sizeof(Node));
        p->data = x;
        p->next = (*l)->next;
        (*l)->next = p;
        printf("Enter value (9999 to quit): ");
        scanf("%d", &x);
    }
    return true;
}
void reverse(List* l){
    if((*l)->next == NULL){
        return ;
    }
    Node* cur = (*l)->next;
    Node* pre = NULL;
    Node* temp;
    while(cur!= NULL){
        temp = cur->next;
        cur->next = pre ;
        pre = cur;
        cur = temp;
    }
    (*l)->next = pre    ;
}
```
## 双向链表
可以根据当前结点找到前一个结点，依然分为带头结点与不带头结点两部分来实现
### 双向链表实现
1. 初始化
2. 插入：按位置插入（i从0开始），指定结点的后插，指定结点的前插
3. 删除：按位序删除，删除指定结点
4. 内存回收
5. 查找：按值、按位
6. 头插法、尾插法
不带头结点：
```c
#include <stdio.h>
#include <malloc.h>

#define Type int

typedef struct Node {
    struct Node *next;
    struct Node *prev;
    Type data;
} Node;
typedef Node *LinkedList;

void initList(LinkedList *l) {
    *l = NULL;
}

void destructor(LinkedList *l) {
    Node *p = *l;
    while (p != NULL) {
        Node *temp = p;
        p = p->next;
        free(temp);
    }
    *l = NULL;
}

void insert(LinkedList *l, int i, Type e) {
    if (i < 0) {
        printf("Index cannot be negative\n");
        return;
    }

    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Can't allocate memory\n");
        return;
    }
    newNode->data = e;

    if (i == 0) {
        newNode->prev = NULL;
        newNode->next = *l;
        if ((*l) != NULL) {
            (*l)->prev = newNode;
        }
        *l = newNode;
        return;
    }

    int j = 0;
    Node *p = *l;
    while (j < i - 1 && p != NULL) {
        p = p->next;
        j++;
    }
    if (p == NULL) {
        printf("i > Length of list");
        free(newNode);
        return;
    }
    newNode->prev = p;
    newNode->next = p->next;
    p->next = newNode;
    if (newNode->next != NULL) {
        newNode->next->prev = newNode;
    }
}

void insertAfterNode(LinkedList *l, Node *p, Type e) {
    if (*l == NULL || p == NULL) {
        return;
    }
    Node *q = *l;
    while (q != p && q != NULL) {
        q = q->next;
    }
    if (q == NULL) {
        // p 不在链表中
        return;
    }
    // 插到q的后面
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Can't allocate memory\n");
        return;
    }
    newNode->data = e;
    newNode->prev = q;
    newNode->next = q->next;
    q->next = newNode;
    if (newNode->next != NULL) {
        newNode->next->prev = newNode;
    }
}

void insertBeforeNode(LinkedList *l, Node *p, Type e) {
    if (*l == NULL || p == NULL) {
        return;
    }
    Node *q = *l;
    while (q != NULL && q != p) {
        q = q->next;
    }
    if (q == NULL) {
        return;
    }
    // 插到q的前面
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Can't allocate memory\n");
        return;
    }
    newNode->data = e;
    newNode->next = q;
    newNode->prev = q->prev;
    q->prev = newNode;
    if (newNode->prev) {
        newNode->prev->next = newNode;
    } else {
        *l = newNode;
    }
}

void deleteByIndex(LinkedList *l, int i, Type *e) {
    if (*l == NULL || i < 0) {
        return;
    }
    Node *p = *l;
    if (i == 0) {
        *e = p->data;
        *l = p->next;
        if (*l != NULL) {
            (*l)->prev = NULL;
        }
        free(p);
        return;
    }
    int j = 0;
    while (j < i && p != NULL) {
        p = p->next;
        j++;
    }
    if (p == NULL) {
        return;
    }
    // 要删除的就是p结点
    *e = p->data;
    p->prev->next = p->next;
    if (p->next) {
        p->next->prev = p->prev;
    }
    p->prev = NULL;
    p->next = NULL;
    free(p);
}

void deleteNode(LinkedList *l, Node *p) {
    if (*l == NULL || p == NULL) {
        return;
    }
    Node *q = *l;
    while (q != p && q != NULL) {
        q = q->next;
    }
    if (q == NULL) {
        return;
    }
    // 删除q结点
    if (q->prev != NULL) {
        // q 不是第一个结点
        q->prev->next = q->next;
    } else {
        // 更新第一个结点
        *l = q->next;
    }
    if (q->next != NULL) {
        // q 不是最后一个结点
        q->next->prev = q->prev;
    }
    free(q);
}

Node *getElementByValue(LinkedList *l, Type e) {
    if (*l == NULL) {
        return NULL;
    }
    Node *p = *l;
    while (p != NULL && p->data != e) {
        p = p->next;
    }
    return p;
}

Node *getElementByIndex(LinkedList *l, int i) {
    if (i < 0 || *l == NULL) {
        return NULL;
    }
    int j = 0;
    Node *p = *l;
    while (p != NULL && j < i) {
        p = p->next;
        j++;
    }
    return p;
}

void initListHead(LinkedList *l) {
    int x;
    printf("Enter value (9999 to quit): ");
    scanf("%d", &x);
    if (x == 9999) {
        return;
    }
    *l = (Node *) malloc(sizeof(Node));
    if (*l == NULL) {
        return;
    }
    (*l)->prev = NULL;
    (*l)->next = NULL;
    (*l)->data = x;
    Node *p = *l;
    while (1) {
        printf("Enter value (9999 to quit): ");
        scanf("%d", &x);
        if (x == 9999) {
            break;
        }
        Node *newNode = (Node *) malloc(sizeof(Node));
        if (newNode == NULL) { // 检查内存分配
            break;
        }
        newNode->data = x;
        newNode->prev = NULL;
        newNode->next = p;
        p->prev = newNode;
        p = newNode;
    }
    *l = p;
}

void initListTail(LinkedList *l) {
    int x;
    printf("Enter value (9999 to quit): ");
    scanf("%d", &x);
    if (x == 9999) {
        *l = NULL;
        return;
    }
    *l = (Node *) malloc(sizeof(Node));
    if (*l == NULL) {
        return;
    }
    (*l)->prev = NULL;
    (*l)->next = NULL;
    (*l)->data = x;
    Node *p = *l;
    while (1) {
        printf("Enter value (9999 to quit): ");
        scanf("%d", &x);
        if (x == 9999) {
            break;
        }
        Node *newNode = (Node *) malloc(sizeof(Node));
        if (newNode == NULL) { // 检查内存分配
            break;
        }
        newNode->data = x;
        newNode->prev = p;
        newNode->next = NULL;
        p->next = newNode;
        p = newNode;
    }
}

int main() {
    LinkedList list;
    initList(&list);

    // 使用头插法初始化链表
    initListTail(&list);

    // 打印链表内容
    printf("Current list: ");
    Node *temp = list;
    while (temp != NULL) {
        printf("%d ", temp->data);
        temp = temp->next;
    }
    printf("\n");

    // 插入示例
    insert(&list, 0, 100);
    insert(&list, 1, 200);
    insert(&list, 2, 300);
    insert(&list, 10, 400); // 测试超出范围
    printf("After inserts: ");
    temp = list;
    while (temp != NULL) {
        printf("%d ", temp->data);
        temp = temp->next;
    }
    printf("\n");

    // 删除示例
    Type e;
    deleteByIndex(&list, 0, &e);
    printf("Deleted node with value %d from index 0\n", e);
    printf("After deletion: ");
    temp = list;
    while (temp != NULL) {
        printf("%d ", temp->data);
        temp = temp->next;
    }
    printf("\n");

    // 销毁链表
    destructor(&list);
    printf("List destroyed.\n");

    return 0;
}
```
带头结点:
```c
#include <stdio.h>
#include <malloc.h>

#define Type int

typedef struct Node {
    struct Node *next;
    struct Node *prev;
    Type data;
} Node;
typedef Node *LinkedList;

void initList(LinkedList *l) {
    *l = (Node *) malloc(sizeof(Node));
    if (*l == NULL) {
        printf("Can't allocate memory\n");
        return;
    }
    (*l)->next = NULL;
    (*l)->prev = NULL;
}

void destructor(LinkedList *l) {
    Node *p = (*l)->next;
    while (p != NULL) {
        Node *temp = p->next;
        p = p->next;
        free(temp);
    }
    free(*l);
    *l = NULL;
}

void insert(LinkedList *l, int i, Type e) {
    if (i < 1 || (*l) == NULL) {
        return;
    }
    Node *p = *l;
    int j = 0;
    while (p != NULL && j < i - 1) {
        p = p->next;
        j++;
    }
    // 插入到p的下一个
    if (p == NULL) {
        printf("Index out of bounds\n");
        return;
    }
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Can't allocate memory\n");
        return;
    }
    newNode->data = e;
    newNode->prev = p;
    newNode->next = p->next;
    if (p->next != NULL) {
        p->next->prev = newNode;
    }
    p->next = newNode;
}

void insertAfterNode(LinkedList *l, Node *p, Type e) {
    if (*l == NULL || p == NULL) {
        return;
    }
    Node *q = *l;
    while (q != NULL && q != p) {
        q = q->next;
    }
    // 插入到 q 后面
    if (q == NULL) {
        // p 不是链表的一部分
        return;
    }
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Can't allocate memory\n");
        return;
    }
    newNode->data = e;
    newNode->prev = q;
    newNode->next = q->next;
    if (q->next != NULL) {
        q->next->prev = newNode;
    }
    q->next = newNode;
}

void insertBeforeNode(LinkedList *l, Node *p, Type e) {
    if (*l == NULL || p == NULL || *l == p) {
        // p 不能是头结点
        return;
    }
    Node *q = *l;
    while (q != NULL && q != p) {
        q = q->next;
    }
    // 插入到 q 前面
    if (q == NULL) {
        // p 不是链表的一部分
        return;
    }
    Node *newNode = (Node *) malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Can't allocate memory\n");
        return;
    }
    newNode->data = e;
    newNode->prev = q->prev;
    newNode->next = q;
    q->prev->next = newNode;
}

void deleteByIndex(LinkedList *l, int i, Type *e) {
    if (*l == NULL || (*l)->next == NULL || i < 1) {
        return;
    }
    int j = 0;
    Node *p = *l;
    while (p != NULL && j < i) {
        p = p->next;
        j++;
    }
    if (p == NULL) {
        return;
    }
    *e = p->data;
    p->prev->next = p->next;
    if (p->next != NULL) {
        p->next->prev = p->prev;
    }
    free(p);
}

void deleteNode(LinkedList *l, Node *p) {
    if (*l == NULL || *l == p) {
        return;
    }
    Node *q = (*l)->next;
    while (q != NULL && q != p) {
        q = q->next;
    }
    if (q == NULL) {
        return;
    }
    // 删除q
    q->prev->next = q->next;
    if (q->next != NULL) {
        q->next->prev = q->prev;
    }
    free(q);
}

Node *getElementByValue(LinkedList *l, Type e) {
    if (*l == NULL) {
        return NULL;
    }
    Node *p = (*l)->next;
    while (p != NULL && p->data != e) {
        p = p->next;
    }
    return p;
}

Node *getElementByIndex(LinkedList *l, int i) {
    if (i < 1 || *l == NULL) {
        return NULL;
    }
    int j = 1;
    Node *p = (*l)->next;
    while (p != NULL && j < i) {
        p = p->next;
        j++;
    }
    return p;
}

void initListHead(LinkedList *l) {
    int x;
    printf("Enter value (9999 to quit): ");
    scanf("%d", &x);
    if (x == 9999) {
        return;
    }
    Node *q = (Node *) malloc(sizeof(Node));
    if (q == NULL) {
        return;
    }
    q->prev = *l;
    q->next = NULL;
    q->data = x;
    (*l)->next = q;
    while (1) {
        printf("Enter value (9999 to quit): ");
        scanf("%d", &x);
        if (x == 9999) {
            break;
        }
        Node *newNode = (Node *) malloc(sizeof(Node));
        if (newNode == NULL) { // 检查内存分配
            break;
        }
        newNode->data = x;
        newNode->prev = *l;
        newNode->next = (*l)->next;
        if ((*l)->next != NULL) {
            (*l)->next->prev = newNode;
        }
        (*l)->next = newNode;
    }
}

void initListTail(LinkedList *l) {
    int x;
    printf("Enter value (9999 to quit): ");
    scanf("%d", &x);
    if (x == 9999) {
        return;
    }
    // q 指向链表末尾的结点
    Node *q = (Node *) malloc(sizeof(Node));
    if (q == NULL) {
        return;
    }
    q->prev = *l;
    q->next = NULL;
    q->data = x;
    (*l)->next = q;
    while (1) {
        printf("Enter value (9999 to quit): ");
        scanf("%d", &x);
        if (x == 9999) {
            break;
        }
        Node *newNode = (Node *) malloc(sizeof(Node));
        if (newNode == NULL) { // 检查内存分配
            break;
        }
        newNode->data = x;
        newNode->prev = q;
        newNode->next = NULL;
        q->next = newNode;
        q = newNode;
    }
}

int main() {
    LinkedList l;
    initList(&l);

//    initListHead(&l); // 测试头插法
//    printf("List after head insertion:\n");
//    for (Node *p = l->next; p != NULL; p = p->next) {
//        printf("%d ", p->data);
//    }
//    printf("\n");
//    destructor(&l);

//    initList(&l);
    initListTail(&l); // 测试尾插法
    printf("List after tail insertion:\n");
    for (Node *p = l->next; p != NULL; p = p->next) {
        printf("%d ", p->data);
    }
    printf("\n");

    Type e;
    deleteByIndex(&l, 2, &e); // 删除第二个节点
    printf("List after deleting index 2:\n");
    for (Node *p = l->next; p != NULL; p = p->next) {
        printf("%d ", p->data);
    }
    printf("\n");

    destructor(&l); // 清理链表
    return 0;
}
```
## 循环链表
就是在普通链表的基础上，增加了第一个结点和最后一个结点的互相指向
![image](https://github.com/user-attachments/assets/5f38a32b-a0d9-4428-b1d6-57d7f971dc03)
![image](https://github.com/user-attachments/assets/b9183338-f0d7-4518-9d63-8cbeaa746362)
## 静态链表
给链表分配一片连续的内存空间，本质上跟数组没啥区别，就是将数组作为了链表使用
```C
typedef struct Node{
    int data;
    int next;
};
```
代码地址：https://github.com/proacane/DataStructure