---
title: Java的import与package
date: 2018-06-06 11:30:01
tags: [Java,import,package]
---
*最近又开始炒现饭算法了，“Algorithms 4th”这本书买了几年了都还没翻完，重新开始学时Java的基本编译知识都忘了，从CSDN找到自己以前写的“[import与package](https://blog.csdn.net/genuineness/article/details/40947065)”，将原文搬到这里，重新学习一下。*

最近在通过《Algorithms》的第4版来学习算法，这本书用的语言是Java，它的示例代码中的输入输出经过了作者的封装，在stdlib.jar这个jar包中，为了目录结构清楚，我建立了一个目录lib存放stdlib.jar，每章自己敲的代码依次放到ch01、ch02...中。

假设在test目录中有一个test.java，用到了stdlib.jar中的StdOut：
```
	import java.util.Arrays;
    
	public class test {
    	public static void main(String[] args)
        {     
			int[] testArr = {4, 2, 3, 1};
			Arrays.sort(testArr);
            
            for (int i : testArr) {
            	StdOut.print(i + " ");
            }
			StdOut.print("\n");
		}
	}
```

在test目录下编译：javac -cp ../lib/stdlib.jar test.java，运行：java -cp .:../lib/stdlib.jar test，输出：1 2 3 4。为什么Arrays要用import导入，而StdOut不需要import？Arrays前的util和java分别代表什么？

我的jdk版本为1.8，安装好后在jdk1.8.0下有一个src.zip的压缩包，这里面有Arrays的源码，解压后可看到目录结构：
{% asset_img java_src_d.png Java源码目录 %}

在Arrays.java的开头中有：
`package java.util;`

如果在test.java的开头加一行“package test;”，结果会怎样？编译时找不到StdOut了。
看一下StdOut.java的源码，其开头没用package。package的作用是什么？
[Oracle文档](https://docs.oracle.com/javase/tutorial/java/package/packages.html)的解释:
To make types easier to find and use, to avoid naming conflicts, and to control access, programmers bundle groups of related types into packages.

为了使类型易于找到和使用，避免命名冲突和控制访问，将相关的类型归组并放到package中。
Arrays这个类属于java.util这个package，可将java看作util的父package，与父子目录对应。

在不加package语句的情况下，一个类就属于默认package，使用同一package的类时无需import。在StdOut.java和test.java都不加package时，StdOut类和test类同属于默认package，所以StdOut不需import，加了编译时会报错。

当在test.java中加入“package test;”后，test类属于test这个package，与StdOut不属于同一package，所以需要将添加import语句，import的语法如下：
```
import package.class_name; // package可包含多个层次
```
在StdOut.java中开头添加：
`package stdlib;`
在test.java的开头添加：
```
	package test;
	import stdlib.StdOut;
```

重新编译后在test目录的父目录中运行：java -cp .:./lib/stdlib.jar test.test 