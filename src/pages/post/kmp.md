---
layout: ../../layouts/post.astro
title: KMP Algorithm
description: This is a blog about kmp algorithm and its implement.
dateFormatted: Apr 17th, 2024
---

# 1.KMP原理
## 1.1为什么叫KMP(Why)
仅仅只是因为该算法的三个创始人名字首字母为K,M,P，以此命名。可能大家认为是这样的：快速(K)模式(M)匹配(P)算法
## 1.2什么时候用KMP(When)
当在一个字符串中寻找某个子串是否出现过时。主要思想是当出现字符串不匹配时，可以知道一部分之前已经匹配的文本内容，可以利用这些信息避免从头再去做匹配了（暴力解法）。
## 1.3怎么用KMP（How）
### 1.3.1基础知识之前缀表
前缀表：是用来回退的，它记录了模式串与主串(文本串)不匹配的时候，模式串应该从哪里开始重新匹配。
**前缀表中的值为最长相等前后缀的值**
前缀：指不包含最后一个字符的所有以第一个字符开头的连续子串
后缀：指不包含第一个字符的所有以最后一个字符结尾的连续子串
举例说明：对于字符串aabaaf,

 1. 对于下标为0结尾的子串a,其最长相等前后缀为0[a既是首字母也是尾字母]
 2. 对于下表为1结尾的子串aa,其最长相等前后缀为1，即a
 3. 对于下表为2结尾的子串aab,其最长相等前后缀为0[前缀：a,aa 后缀：b,ab]
 4. 对于下表为3结尾的子串aaba,其最长相等前后缀为1，即a
 5. 对于下表为4结尾的子串aabaa,其最长相等前后缀为2，即aa[前缀：a,aa,aab,aaba 后缀：a,aa,baa,abaa]
 6. 对于下表为5结尾的子串aabaaf,其最长相等前后缀为0[前缀：a,aa,aab,aaba,aabaa 后缀：尾字母均含f]

字符串下标|0  | 1 |  2  |  3|4|5|
---|---|---|---|---|---|---|
字符串| a | a|b|a|a|f
前缀表|0|1|0|1|2|0
### 1.3.2基础知识之模式串与文本串
上述的例子aabaaf就是模式串，而要寻找该模式串是否在另一个字符串中，这个字符串就是文本串。
举例说明：文本串---aabaabaaf
				  模式串---aabaaf
				  即查看文本串中是否包含模式串，进行字符串的匹配
### 1.3.3基础知识之next数组
next数组中存的就是前缀表的值
**有些代码中的next数组可能为前缀表的值减1或者前缀表的值整体右移1位。我们现在讨论的都不是前面两种情况**
## 1.4过程模拟
举例说明：文本串---aabaabaaf
				  模式串---aabaaf
字符串下标|0  | 1 |  2  |  3|4|5|  6|7|8|
---|---|---|---|---|---|---|---|---|---|
文本串t[i]| a | a|b|a|a|b|a|a|f
模式串s[j]| a | a|b|a|a|f
前缀表next[j]|0|1|0|1|2|0|		
		  
当匹配至t[5]和s[5]时，发现t[5]!=s[5]，那么需要查看next[4]，发现指向的下标为2，表明下次匹配从s[2]开始，与t[5]进行匹配；发现恰好s[2]==t[5]			 
# 2.KMP代码实现
我们定义i表示后缀的末尾下标，j表示前缀的末尾下标，同时j也是最长相等前后缀长度
## 2.1求取next数组值的函数[next数组为前缀表的值]
构造next数组其实就是计算模式串s，前缀表的过程。 主要有如下三步：

 1. 初始化
 2. 处理前后缀不相同的情况
 3. 处理前后缀相同的情况
```cpp
void getNext(string& s, vector<int>& next){
	int j = 0;
	next[0] = j;
	for(int i = 1;i<s.size();i++){
		while(j>0 && s[i]!=s[j]) j = next[j-1];//见1.4
		if(s[i] == s[j]) j++;
		next[i] = j;
	}
}
```
## 2.2匹配函数
```cpp
 int strStr(string haystack, string needle) {
	//KMP 求next数组，即前缀表;next数组不右移，直接代表j下标下的最长相等前后缀长度
	 if (needle.size() == 0) return 0;
	 vector<int> next(needle.size());
	 getNext(needle,next);//next数组值函数
	 int j = 0;
	 for(int i = 0;i < haystack.size();i++){
	     while(j>0 && haystack[i] != needle[j]) j = next[j-1];
	     if(haystack[i] == needle[j]) j++;
	     if(j == needle.size()) return i - needle.size() + 1;
	 }//当j == needle.size()时恰好遍历完模式串，即needle
	 return -1;
    }
```
# 3.Leetcode题目与知识参考
## 3.1 [28. 找出字符串中第一个匹配项的下标](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)
## 3.2 [459.重复的子字符串](https://leetcode.cn/problems/repeated-substring-pattern/description/)
## 3.3 来自代码随想录及视频 [代码随想录](https://www.programmercarl.com/0028.%E5%AE%9E%E7%8E%B0strStr.html)
                            