# 字符串
由零个或多个字符组成的有限序列
1. 子串：串中任意个连续的字符组成的子序列
2. 主串：包含子串的串
3. 字符在主串中的位置：字符在串中的序号
4. 子串在主串中的位置：子串的第一个字符在主串中的位置
基本操作：
1. 赋值：assign(str*t,str*s)：t赋值为s
2. 复制：copy(str*t,str*s)：s复制给t
3. 判空
4. 长度
5. 清空
6. 释放内存
7. 拼接
8. 子串：substr(str*s,int pos,int len)
9. 定位：若主串S中存在与串T值相同的子串，则返回它在主串S中第一次出现的位置；否则函数值为0
10. 比较大小
实现依然是分为顺序存储和链式存储
## 顺序存储实现
顺序存储内部的 char 数组，可以使用静态数组，也可以使用动态分配，这里使用静态数组
顺序存储有几种不同的实现方案:
1. char数组从索引0开始存放字符，使用单独的变量存储字符串长度
2. char数组索引0存储长度，后面存放字符
3. 没有长度变量，以\0作为字符串结尾
4. char数组索引0不使用，使用单独的变量存储字符串长度
我采用的是：最后一位是'\0'，同时使用len变量
```C
#define MAX_LEN 10

typedef struct {
    char data[MAX_LEN];
    int length;
} string;

void constructor(string *s) {
    s->length = 0;
    s->data[0] = '\0';
}

bool isEmpty(string *s) {
    return s->length == 0;
}

// 赋值
void assign(string *s, const char *t, int len) {
    if (t[0] == '\0') {
        return;
    }
    if (len >= MAX_LEN ) {
        len = MAX_LEN - 1;
        printf("t is too long, use %d chars\n", len);
    }
    for (int i = 0; i < len; i++) {
        s->data[i] = t[i];
    }
    s->length = len;
    s->data[len] = '\0';
}

void copy(string *s, string *t) {
    memcpy(s->data, t->data, t->length);
    s->length = t->length;
    s->data[s->length] = '\0';
}

void clear(string *s) {
    memset(s->data, '\0', s->length);
    s->length = 0;
}

void destructor(string *s) {

}

bool concat(string *dest, string *s, string *t) {
    if (s->length + t->length > MAX_LEN) {
        return false;
    }
    for (int i = 0; i < s->length; i++) {
        dest->data[i] = s->data[i];
    }
    for(int j = 0;j< t->length;j++){
        dest->data[j + s->length] = t->data[j];
    }
    dest->length = s->length + t->length;
    dest->data[dest->length] = '\0';
    return true;
}

bool substr(string *dest, string *s, int pos, int len) {
    if (pos + len > s->length) {
        return false;
    }

    for (int i = pos; i < pos + len; i++) {
        dest->data[i - pos] = s->data[i];
    }
    dest->length = len;
    dest->data[len] = '\0';
    return true;
}

// s1>s2 返回值大于0
int compare(string*s1,string*s2){
    for(int i = 0; i< s1->length && i < s2->length;i++){
        if(s1->data[i] != s2->data[i]){
            return s1->data[i] - s2->data[i];
        }
    }
    // 长度长的串大
    return s1->length -s2->length;
}
// 返回t子串在 s 中第一次出现的位置
int index(string*s,string* t){
    int i = 0;
    int n = s->length;
    int m = t->length;
    if(m > n){
        return -1;
    }
    // 截取子串
    string sub;
    constructor(&sub);
    while(i <= n-m){
        substr(&sub,s,i,m);
        // 相等直接返回
        if(compare(&sub,t) == 0){
            return i;
        }
        i++;
    }
    return -1;
}
```
## 链式存储实现
链式存储的每个节点可以放1个或多个字符，这里存放4个字符
```C
#define MAX_CHARS 4
typedef struct Node {
    // 每个节点存放4个字符
    char data[MAX_CHARS];
    struct Node *next;
} Node;
typedef Node *string;

void constructor(string *s) {
    *s = (Node *) malloc(sizeof(Node));
    if (*s == NULL) {
        printf("Failed to allocate memory\n");
        return;
    }
    (*s)->next = NULL;
    for (int i = 0; i < MAX_CHARS; ++i) {
        (*s)->data[i] = '\0';
    }
}

void destructor(string *s) {
    Node *current = *s;
    while (current != NULL) {
        Node *temp = current;
        current = current->next;
        free(temp);
    }
    *s = NULL;
}

int size(string *s) {
    Node *current = *s;
    int totalLen = 0;
    while (current != NULL) {
        for (int i = 0; i < MAX_CHARS; i++) {
            if (current->data[i] == '\0') {
                return totalLen;
            }
            totalLen++;
        }
        current = current->next;
    }
    return totalLen;
}

bool isEmpty(string *s) {
    return (*s == NULL || (*s)->data[0] == '\0');
}

void assign(string *s, const char *t, int len) {
    if (t[0] == '\0') {
        return;
    }
    destructor(s);
    constructor(s);
    Node *current = *s;
    for (int i = 0; i < len; i++) {
        if (i != 0 && i % MAX_CHARS == 0) {
            // 创建新节点
            Node *newNode = (Node *) malloc(sizeof(Node));
            if (newNode == NULL) {
                printf("Failed to allocate memory\n");
                return;
            }
            newNode->next = NULL;
            for (int k = 0; k < MAX_CHARS; ++k) {
                newNode->data[k] = '\0';
            }
            current->next = newNode;
            current = newNode;
        }
        current->data[i % MAX_CHARS] = t[i];
    }
}

void printStr(string *s) {
    Node *current = *s;
    while (current != NULL) {
        for (int i = 0; i < MAX_CHARS; i++) {
            if (current->data[i] != '\0') {
                printf("%c", current->data[i]);
            }
        }
        current = current->next;
    }
    printf("\n");
}

bool concat(string *dest, string *s, string *t) {
    destructor(dest);
    constructor(dest);
    Node *current = *s;
    Node *destCurrent = *dest;
    int i = 0;
    // s 拼到 dest
    while (current != NULL) {
        for (int j = 0; j < MAX_CHARS; j++) {
            if (current->data[j] == '\0') {
                break;
            }
            destCurrent->data[i++] = current->data[j];
            if (i == MAX_CHARS) {
                // 创建新节点
                Node *newNode = (Node *) malloc(sizeof(Node));
                if (newNode == NULL) {
                    printf("Failed to allocate memory\n");
                    return false;
                }
                newNode->next = NULL;
                for (int k = 0; k < MAX_CHARS; ++k) {
                    newNode->data[k] = '\0';
                }
                destCurrent->next = newNode;
                destCurrent = newNode;
                i = 0;
            }
        }
        current = current->next;
    }

    // 拼接完了 s，从 dest当前节点的第 i位拼接 t
    current = *t;
    while (current != NULL) {
        for (int j = 0; j < MAX_CHARS; j++) {
            if (current->data[j] == '\0') {
                break;
            }
            destCurrent->data[i++] = current->data[j];
            // dest的节点满了，创建新节点
            if (i == MAX_CHARS) {
                Node *newNode = (Node *) malloc(sizeof(Node));
                if (newNode == NULL) {
                    printf("Failed to allocate memory\n");
                    return false;
                }
                newNode->next = NULL;
                for (int k = 0; k < MAX_CHARS; ++k) {
                    newNode->data[k] = '\0';
                }
                destCurrent->next = newNode;
                destCurrent = newNode;
                i = 0;
            }
        }
        current = current->next;
    }
    return true;
}

bool substr(string *dest, string *s, int pos, int len) {
    destructor(dest);
    constructor(dest);
    int strLen = size(s);
    if (pos + len > strLen) {
        return false;
    }
    // 从第几个节点开始截取
    int i = pos / MAX_CHARS;
    // 从节点的第几位开始截取
    int j = pos % MAX_CHARS;
    Node *current = *s;
    // 移动到第 i 个节点
    while (i > 0 && current != NULL) {
        current = current->next;
        i--;
    }

    if (current == NULL) {
        return false; // 如果没有足够的节点
    }

    Node *destCurrent = *dest;
    int destIndex = 0;

    // 开始向 dest 中添加字符
    for (int k = 0; k < len; k++) {
        // 截取完了一个节点
        if (j == MAX_CHARS) {
            current = current->next;
            j = 0;
        }
        // dest的节点放满了
        if (destIndex == MAX_CHARS) {
            // 创建新节点
            Node *newNode = (Node *) malloc(sizeof(Node));
            if (newNode == NULL) {
                printf("Failed to allocate memory\n");
                return false;
            }
            newNode->next = NULL;
            for (int l = 0; l < MAX_CHARS; ++l) {
                newNode->data[l] = '\0';
            }
            destCurrent->next = newNode;
            destCurrent = newNode;
            destIndex = 0;
        }
        destCurrent->data[destIndex++] = current->data[j++];
    }
    return true;
}

// s1>s2 返回值大于0
int compare(string *s1, string *s2) {
    Node *current1 = *s1;
    Node *current2 = *s2;
    while (current1 != NULL && current2 != NULL) {
        for (int i = 0; i < MAX_CHARS; i++) {
            if (current1->data[i] == '\0' || current2->data[i] == '\0') {
                return current1->data[i] - current2->data[i];
            }

            if (current1->data[i] != current2->data[i]) {
                return current1->data[i] - current2->data[i];
            }
        }
        current1 = current1->next;
        current2 = current2->next;
    }
    return size(s1) - size(s2);
}

int indexOfString(string *s, string *t) {
    int sSize = size(s);
    int tSize = size(t);
    if (tSize > sSize) {
        return -1;
    }

    string sub = NULL;
    int i = 0;
    while (i <= sSize - tSize) {
        if (!substr(&sub, s, i, tSize)) {
            destructor(&sub);
            return -1;
        };
        if (compare(&sub, t) == 0) {
            destructor(&sub);
            return i;
        }
        i++;
    }
    destructor(&sub);
    return -1;
}
```
初始化的时候可能初始化为NULL比较好，这里就不作更改了