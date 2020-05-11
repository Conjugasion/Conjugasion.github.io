---
title: 不要在MySql中使用utf8
tag: [Sql]
category: Sql
excerpt: MySql编码格式、排序规则和主键长度约束
cover: /image15.jpg
---
### **Mysql编码格式**
```bash
Incorrect string value: ‘ð <…’ for column ‘summary’ at row 1
```
我用的是UTF-8编码的客户端，服务器也是UTF-8编码的，数据库也是，就连要保存的这个字符串“😃”也是合法的UTF-8。
**问题的症结在于，MySQL的“utf8”实际上不是真正的UTF-8。**
MySql的"utf8"只支持每个字符最多三个字节，而真正的UTF-8是每个字符最多四个字节。

**总结**
1.MySQL的“utf8mb4”是真正的“UTF-8”。
2.MySQL的“utf8”是一种“专属的编码”，它能够编码的Unicode字符并不多。

### **Mysql编码的排序规则**
使用者首先要关心字符集与排序规则在db里的统一。
**准确性**：
utf8mb4_unicode_ci 是基于标准的Unicode来排序和比较，能够在各种语言之间精确排序；
utf8mb4_general_ci 没有实现Unicode排序规则，在遇到某些特殊语言或者字符集，排序结果可能不一致。但是在绝大多数情况下，这些特殊字符的顺序并不需要那么精确。

**性能**：
utf8mb4_general_ci 在比较和排序的时候更快；
utf8mb4_unicode_ci 在特殊情况下，Unicode排序规则为了能够处理特殊字符的情况，实现了略微复杂的排序算法。但是在绝大多数情况下，不会发生此类复杂比较。

### **Mysql表主键长度约束**
varchar格式的主键长度不超过767字节，所以在"utf8"格式下，主键长度最多767/3=255，在"utf8mb4"格式下，主键长度最多767/4=191；
int格式的主键长度表示的是"显示长度"，不代表占用空间大小，所以理论上没限制，但是Mysql规定最多显示长度255。

