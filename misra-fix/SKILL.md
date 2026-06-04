# MISRA C 2012 合规修复

配合 Parasoft C++test 使用：先运行 Parasoft 扫描获取违规报告，再据此 skill 逐条修复代码。

## 工作流

1. 用户提供 Parasoft 扫描报告或指定源文件/目录
2. 识别报告中违反的 MISRA 规则
3. 参照下方规则库逐条修复
4. 修复后建议重新扫描验证

## 约束

- 不得修改代码逻辑，只做合规性调整
- 每个修复报告格式: `文件:行号: 修复内容`
- 修复完成后报告违规数量变化（修复前/修复后）
- 嵌入式环境：禁止使用动态内存（`malloc`/`free`），优先静态分配

---

## 必需规则（Required）

### Rule 8.4 — 函数定义前必须有兼容的原型声明

```c
// 违规
void foo(int x) { ... }  // 无先前声明

// 合规: 头文件中声明
void foo(int x);
void foo(int x) { ... }
```

### Rule 10.3 — 表达式值不应赋给更窄的类型

```c
// 违规
uint8_t x = 256;  // 溢出

// 合规
uint8_t x = 255U;
```

**本项目的具体规则**：enum 值赋值给 `uint16_t`/`uint32_t` 字段时必须加显式类型转换（MISRA RULE_10_3-b）：

```c
// 合规
.index = (uint32_t)SOUND_NO_AUDIO,
.priority = (uint16_t)SOUND_PRIO_VALUE_MAX,
// 违规
.index = SOUND_NO_AUDIO,
```

### Rule 10.4 — 运算符两侧应为相同基本类型

```c
// 违规
if (unsigned_var > signed_var)  // 混合符号比较

// 合规
if (unsigned_var > (uint32_t)signed_var)
```

**本项目附加规则**：所有无符号整数字面量统一使用大写 `U` 后缀：

```c
// 合规
0U, 1U, 100U, 2500U          // 十进制
0xFFU, 0xFFFEU, 0xFFFFFFFFU  // 十六进制
1U << 8U                       // 移位
```

### Rule 11.3 — 不应在不同对象指针类型之间直接转换

```c
// 违规
uint32_t *p = (uint32_t *)char_ptr;

// 合规: 通过 void* 中转或使用 memcpy
```

### Rule 12.2 — 移位量应在范围内

```c
// 违规
uint32_t x = 1U << 32;  // 未定义行为

// 合规
uint32_t x = 1U << 31;
```

### Rule 14.4 — if/while 条件应为布尔类型

```c
// 违规
if (ptr)  // 指针隐式转布尔

// 合规
if (ptr != NULL)
```

### Rule 15.7 — else if 链应以 else 结尾

```c
// 违规
if (x == 1) { ... }
else if (x == 2) { ... }
// 缺少 else

// 合规
if (x == 1) { ... }
else if (x == 2) { ... }
else { /* 默认处理 */ }
```

### Rule 17.7 — 函数返回值不应被丢弃

```c
// 违规
memcpy(dst, src, n);  // 返回值被忽略

// 合规
(void)memcpy(dst, src, n);
```

---

## 建议规则（Advisory）

### Rule 2.5 — 不应有未使用的宏定义

删除未引用的 `#define`，或添加注释说明保留原因。

### Rule 4.1 — 八进制和十六进制转义序列应有终止

```c
// 违规
char s[] = "\x41g";  // 编译器可能把 \x41g 当成一个转义

// 合规: 字符串拼接
char s[] = "\x41" "g";
```

### Rule 8.7 — 仅在一个翻译单元中引用的函数/对象应使用内部链接

```c
// 违规
void helper(void) { ... }  // 全局但仅本文件使用

// 合规
static void helper(void) { ... }
```

### Rule 8.9 — 仅在一个函数中引用的对象应在块作用域中定义

```c
// 违规
static int tmp;  // 文件作用域，仅一个函数使用
void f(void) { tmp = 1; }

// 合规
void f(void) { int tmp = 1; }
```

### Rule 11.5 — void 指针不应转换为对象指针

```c
// 违规
int *p = (int *)malloc(4);  // void* → int*

// 合规: 嵌入式场景使用静态内存池替代
```

### Rule 15.5 — 函数应在末尾有单一出口

```c
// 违规: 多个 return 分散在函数中间
int f(int x) {
    if (x < 0) return -1;
    if (x == 0) return 0;
    return 1;
}

// 合规: 单出口
int f(int x) {
    int ret = 1;
    if (x < 0) { ret = -1; }
    else if (x == 0) { ret = 0; }
    return ret;
}
```

### Rule 16.4 — switch 每个 case 应有 break

```c
// 违规
switch (x) {
case 0: do_a();
case 1: do_b(); break;  // case 0 穿透到 case 1
}

// 合规
switch (x) {
case 0: do_a(); break;
case 1: do_b(); break;
default: break;
}
```

### Rule 18.4 — 不应使用 +/- 运算符操作指针

```c
// 违规
int *p = arr + 5;  // 指针算术

// 合规
int *p = &arr[5];  // 数组下标
```

### Rule 20.7 — 宏参数应加括号

```c
// 违规
#define SQUARE(x) x * x  // SQUARE(a+b) = a+b*a+b

// 合规
#define SQUARE(x) ((x) * (x))
```

### Rule 21.3 — 不应使用 `<stdlib.h>` 的动态内存函数

嵌入式系统中 `malloc`/`free` 导致堆碎片。一律使用静态分配或 RTOS 内存池。

---

## 快速修复策略

1. **类型转换（Rule 10.x, 11.x）**：显式类型转换，确保不溢出，无符号量加 U 后缀
2. **缺少声明（Rule 8.x）**：外部函数在头文件中声明，内部函数加 `static`
3. **控制流（Rule 14.x, 15.x, 16.x）**：添加 else 分支、break、显式布尔比较
4. **宏安全（Rule 20.7）**：宏参数加括号
5. **返回值丢弃（Rule 17.7）**：使用 `(void)` 显式丢弃
