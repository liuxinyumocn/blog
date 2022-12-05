# 计时攻击

​		今天分享一个很有趣的攻击手段，先看如下代码：

```java
private boolean safeEqual(String a,String b){
  if(a == null || b == null){
    return a == b;
  }
  if(a.length() != b.length()){		// ①
    return false;
  }
  int equal = 0;		// ②
  for (int i = 0; i < a.length(); i++){
    equal |= a.charAt(i) ^ b.charAt(i);
  }
  return equal == 0;
}
```

​		一个字符串比较的函数写的如此复杂，①先判断字符串长度比较，然后②开始遍历字符串对每一位使用异或操作来比较，将每次的结果与equal进行或运算，如果两个字符串相等，那么最终存储的equal一定等于0，否则为1，整体逻辑上是没什么问题，但是聪明的你可能会发现效率不够好，完全可以进一步优化，如果遍历过程中如果发现有一位不同，那么就可以提前结束了是不是！

​		于是“优化”后的代码变成了（并感慨这位程序猿的代码功底不过如此……）：

```java
private boolean safeEqual(String a,String b){
  if(a == null || b == null){
    return a == b;
  }
  if(a.length() != b.length()){
    return false;
  }
  int equal = 0;
  for (int i = 0; i < a.length(); i++){
    equal |= a.charAt(i) ^ b.charAt(i);
    if(equal != 0)
      return false;
  }
  return true;
}
```

​		等等，注意到这个函数方法名字叫 safe Equal 而不是 equal，难道这个方法别有用意？

​		无独有偶，在JDK、PHP中都发现了相似的写法，所以真的是这位程序猿的功底不够吗？

## 计时攻击

​		计时攻击的方式是利用功耗、时序、电磁泄漏等方式达到破解目的。

​		假设当前使用的Equal方法是不安全的，黑客在破解口令的时候就可以不断的枚举，从第一位开始计算耗时：

```
Password：	aXXXXXXX	3ms
Password：	bXXXXXXX	5ms
Password：	cXXXXXXX	2ms
Password：	dXXXXXXX	4ms
Password：	eXXXXXXX	16ms
……
```

​		由此发现 eXXXXXXX 密码比其他密码都多了1位耗时，那么口令的第一位就是 e ！通过此类方式不断的迭代下去，将破解出完整的口令。

​		实际的应用中受限网络、硬件吞吐能力，这一延迟可能表现并不那么明显，甚至密码也并非明文存储，但对于一些本地的应用场景仍然是这种策略的主要破译手段。
