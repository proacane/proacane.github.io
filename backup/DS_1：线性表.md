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

# 顺序表
用顺序存储的方式实现线性表，即把逻辑上相邻的元素存储在物理位置上也相邻的存储单元中，元素之间的关系由存储单元的邻接关系来体现。类似于数组
![image](https://github.com/user-attachments/assets/93c9e5f4-5257-48d9-8ede-c9a410369491)
## 实现
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
代码地址：https://github.com/proacane/DataStructure