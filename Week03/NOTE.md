学习笔记

# 学习心得<br>

## 课程内容所得<br>
递归本质上类似循环,只不过是通过循环体调用自己,因为汇编没有所谓的循环嵌套这一说,递归和循环编译出来的汇编代码其实异曲同工,所以递归和循环它们没有明显的边界<br>
<br>

## 递归<br>
1.向下进入到不同的递归层,向上又回到原来的一层<br>
2.通过参数进行不同层之间的传递变量<br>
3.每一层都是一个递归栈,可以对函数的参数和全局变量发生变化<br>


<br>

### 递归模板<br>
自顶向下编程<br>
1.recursion terminator 递归终止条件<br>
2.process current level logic<br>
3.dril down 下探<br>
4.reverse current states if needed 清理当前层<br>

### 递归思维要点<br>
**找最近重复性**<br>
1.抵制人肉递归 不要人肉进行递归(最大误区)<br>
2.找最近重复性 找到最近最简方法,将其拆解成可重复解决的问题(重复子问题)<br>
3.数学归纳法思维 数据归纳法思维<br>
4.检查mutual exclusive和complete exhaustive,分享互斥且不重复,还有避免遗漏<br>


**为什么要找最近重复子问题**<br>
因为写的程序的指令只包括if else/for while loop/recursion这三部分,所以最后程序肯定有所谓的重复性<br>


## 分治回溯<br>
#### 1.分治回溯的本质是一种递归,找重复性以及分解问题和最后组合每个子问题的结果<br>
#### 2.自顶向下的编程思想<br>
#### 3.创新的源泉来自对事物本质的认识。无数优秀架构设计的思想来源都是基础的数据结构和算法，这本身就是算法的一个魅力所在。<br>
<br>

## 相关链接<br>
递归代码模板:https://shimo.im/docs/EICAr9lRPUIPHxsH/read<br>
分治代码模板:https://shimo.im/docs/zvlDqLLMFvcAF79A/read<br>
如何优雅地计算斐波那契数列:https://time.geekbang.org/dailylesson/detail/100028406<br>



