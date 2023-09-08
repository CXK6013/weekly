# ClassLoader学习笔记(一) 基本概念与基本特性



## 前言

最近打算学习一下Unsafe，然后看了一下其中的方法之后，然后轻车熟路的写下以下代码:

```java
public static void main(String[] args) {
    Unsafe unsafe = Unsafe.getUnsafe();
    unsafe.allocateMemory(100);
}
```

然后报了下面这个错:

```java
Exception in thread "main" java.lang.SecurityException: Unsafe
	at sun.misc.Unsafe.getUnsafe(Unsafe.java:90)
	at com.example.quicktest.generic.MyNodeTest.main(MyNodeTest.java:27)
```

于是看Unsafe的源码:

```java
@CallerSensitive
public static Unsafe getUnsafe() {
   Class var0 = Reflection.getCallerClass(); // 语句一
  if (!VM.isSystemDomainLoader(var0.getClassLoader())) {
       throw new SecurityException("Unsafe");
   } else {
      return theUnsafe;
   }
}
```

语句一我们通过方法名推断，是获取调用这个方法的类，我们调试一下验证一下我们的猜想:

