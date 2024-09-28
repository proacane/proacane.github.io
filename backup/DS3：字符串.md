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