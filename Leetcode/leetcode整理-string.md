---
title: 【string】
tags: []
notebook: 0. 精华整理贴
---

# 1. leetcode

  


leetcode中关于string的题目有：

  


  *   


  


  


## Leetcode题目解析：

  


### String to Integer (atoi)

  


这个题有很多的坑，不是那么容易可以写出来的。

  


关键步骤：`base = base*10 + (str[i++] - '0')` // i 从高位开始算

  


边界检查：

* 最大的int（INT_MAX）：2147483647

* 最小的int（INT_MIN）：-2147483648

* 开头是否为空格

* 是否有符号，并将符号存在 int sign 中，1代表正，-1代表负

* 后面的数位是否在['0', '9']之间

* \--- 限制int越界 ---

* base由于后面需要乘10，所以base>INT_MAX / 10 就应该报错

* 如果base=INT_MAX / 10，即base=214728364，下一步乘以10，2147283640，限制末位，如果末位>7，那么就超过限制，还是报错

* 下界也考虑了，因为下界的绝对值比上界大1，因此考虑末位为7就可以，返回INT_MAX或者INT_MIN

* \--- 限制int越界 ---

* 如果在中途出现另一个不是‘0’~‘9’之间的数，那就应该return -1；// 这个没有考虑到

* 题目意思：不考虑多个符号的情况

*  leetcode不支持抛出异常的判断，因此在代码中注释了相应的代码段

  


```

class Solution {

public:

int myAtoi(const string str) {

int sign = 1, base = 0, i = 0;

while (str[i] == ' ') { i++; }

if (str[i] == '+') {

sign = 1;

i++;

}

else if (str[i] == '-') {

sign = -1;

i++;

}

while (str[i] >= '0' && str[i] <= '9') {

if (base > INT_MAX / 10 || (base == INT_MAX / 10 && str[i] - '0' > 7)) {

if (sign == 1) return INT_MAX;

else return INT_MIN;

}

base = 10 * base + (str[i++] - '0');

}

/*if (i != str.length()) {

cerr << "error str" << endl;

//exit(1);

throw;

}*/

return base * sign;

}

};

```

  


简化版：可以把符号检查的判断写为：

  


```

if (str[i] == '-' || str[i] == '+') {

sign = 1 - 2 * (str[i++] == '-');

}

```

