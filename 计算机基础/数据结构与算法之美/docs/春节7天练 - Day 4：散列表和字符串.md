你好，我是王争。初四好！

为了帮你巩固所学，真正掌握数据结构和算法，我整理了数据结构和算法中，必知必会的30个代码实现，分7天发布出来，供你复习巩固所用。今天是第四篇。

和昨天一样，你可以花一点时间，来完成测验。测验完成后，你可以根据结果，回到相应章节，有针对性地进行复习。

前几天的内容。如果你错过了，点击文末的“上一篇”，即可进入测试。

* * *

## 关于散列表和字符串的4个必知必会的代码实现

### 散列表

- 实现一个基于链表法解决冲突问题的散列表
- 实现一个LRU缓存淘汰算法

### 字符串

- 实现一个字符集，只包含a～z这26个英文字母的Trie树
- 实现朴素的字符串匹配算法

## 对应的LeetCode练习题（@Smallfly 整理）

### 字符串

- Reverse String （反转字符串）

英文版：[https://leetcode.com/problems/reverse-string/](https://leetcode.com/problems/reverse-string/)

中文版：[https://leetcode-cn.com/problems/reverse-string/](https://leetcode-cn.com/problems/reverse-string/)

- Reverse Words in a String（翻转字符串里的单词）

英文版：[https://leetcode.com/problems/reverse-words-in-a-string/](https://leetcode.com/problems/reverse-words-in-a-string/)
<div><strong>精选留言（21）</strong></div><ul>
<li><img src="https://static001.geekbang.org/account/avatar/00/11/40/10/b6bf3c3c.jpg" width="30px"><span>纯洁的憎恶</span> 👍（0） 💬（1）<div>1.从两端向中间两两对调，时间复杂度O（n）。

2.先去空格，O（n^2）。从两端向中间查找单词，找到一对单词s、t（s在前t在后），保存这两个单词，如果s长t短，把它们之间的字符串整体左移长度差个字符，反之整体右移长度差个字符，再把s和t按调整后位置向原数组赋值，O（n^2）。

3.如果字符串全为空、全为空格、首个非空格字符非法，则返回0。若首个合法字符位“-”则记录。int num=0；逐个读取数字部分字符a，若a合法，则num*=10，然后num+=a-‘0’，直到读取结束或者读到非法字符，此时如果记录的首个合法字符为“-”返回num*（-1），否则返回num。不知int型运算过程中结果值溢出，是否自动将值设置为边界值。如果不能就要在每次乘10的时候结合“-”考察一下是否越界。</div>2019-02-09</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/13/17/88/be4fe19e.jpg" width="30px"><span>molybdenum</span> 👍（0） 💬（1）<div>老师新年好，这是我第四天的作业
https:&#47;&#47;blog.csdn.net&#47;github_38313296&#47;article&#47;details&#47;86818634</div>2019-02-09</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/13/03/f7/3a493bec.jpg" width="30px"><span>老杨同志</span> 👍（0） 💬（1）<div>&#47;&#47;字符串转换整数
package com.jxyang.test.geek.day4.Solution;

class Solution2 {
    public int myAtoi(String str) {
        if(str==null){
            return 0;
        }
        char[] arr= str.toCharArray();
        boolean flag = false;
        boolean numBegin = false;
        int result = 0;
        for(int i =0;i&lt;arr.length;i++){
            if(numBegin &amp;&amp; (arr[i]==&#39;-&#39;||arr[i]==&#39;+&#39;||arr[i]==&#39; &#39;)){
                break;
            }else if(arr[i]==&#39; &#39;)  {
                continue;
            }else if(arr[i]==&#39;+&#39;){
                numBegin = true;
                continue;
            }else if(arr[i]==&#39;-&#39;){
                flag = true;
                numBegin = true;
                continue;
            }else if(arr[i]&gt;=&#39;0&#39;&amp;&amp;arr[i]&lt;=&#39;9&#39;){
                numBegin = true;
                if(result==0){
                    result = flag?(&#39;0&#39;-arr[i]):(arr[i]-&#39;0&#39;);
                }else{
                    try{
                        result = Math.multiplyExact(result,10);
                        result = Math.addExact(result,flag?(&#39;0&#39;-arr[i]):(arr[i]-&#39;0&#39;));
                    }catch (Exception e){
                        if(flag){
                            return Integer.MIN_VALUE;
                        }else{
                            return Integer.MAX_VALUE;
                        }
                    }
                }
            }else{
                break;
            }
        }
        return result;
    }
    public static void main(String[] args) {
        Solution2 solution2 = new Solution2();
        System.out.println(solution2.myAtoi(&quot;42&quot;));
        System.out.println(solution2.myAtoi(&quot; +0 123&quot;));&#47;&#47;期望123
        System.out.println(solution2.myAtoi(&quot;   -42&quot;));
        System.out.println(solution2.myAtoi(&quot;4193 with words&quot;));
        System.out.println(solution2.myAtoi(&quot;words and 987&quot;));
        System.out.println(solution2.myAtoi(&quot;-91283472332&quot;));&#47;&#47;期望-2147483648
        System.out.println(solution2.myAtoi(&quot;+1&quot;));&#47;&#47;期望-2147483648
    }
}</div>2019-02-08</li><br/><li><img src="" width="30px"><span>kai</span> 👍（7） 💬（1）<div>实现一个 LRU 缓存淘汰算法:

import java.util.HashMap;
import java.util.Iterator;

public class LRU&lt;K,V&gt; {
    
    private Node head;
    private Node tail;
    private HashMap&lt;K, Node&gt; map;
    private int maxSize;

    private class Node {
        Node pre;
        Node next;
        K k;
        V v;
        public Node(K k, V v) {
            this.k = k;
            this.v = v;
        }
    }

    public LRU(int maxSize) {
        this.maxSize = maxSize;
        this.map = new HashMap&lt;&gt;(maxSize * 4 &#47; 3);
        head = new Node(null, null);
        tail = new Node(null, null);
        head.next = tail;
        tail.pre = head;
    }

    public V get(K key) {
        if (!map.containsKey(key)) {
            return null;
        }
        Node node = map.get(key);
        unlink(node);
        appendToHead(node);
        return node.v;
    }
    public void put(K key, V value) {
        if (map.containsKey(key)) {
            Node node = map.get(key);
            unlink(node);
        }
        Node node = new Node(key, value);
        appendToHead(node);
        map.put(key, node);
        if (map.size() &gt; maxSize) {
            Node toRemove = removeTail();
            map.remove(toRemove.k);
        }
    }
    private Node removeTail() {
        Node node = tail.pre;
        Node pre = node.pre;
        tail.pre = pre;
        pre.next = tail;
        node.next = null;
        node.pre = null;
        return node;
    }
    private void appendToHead(Node node) {
        Node next = head.next;
        node.next = next;
        node.pre = head;
	next.pre = node;
        head.next = node;
    }
    private void unlink(Node node) {
        Node pre = node.pre;
        Node next = node.next;
        pre.next = next;
        next.pre = pre;
        node.pre = null;
        node.next = null;
    }
    
}</div>2019-02-11</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/50/99/44378317.jpg" width="30px"><span>李皮皮皮皮皮</span> 👍（6） 💬（0）<div>散列表的核心是散列函数和冲突解决算法，以及装载因子过大时如何扩容。散列函数的设计较为复杂，一般使用现有的函数，如murmur散列。冲突解决一般有开放寻址法和链表法。查看开源项目的源码实现很有意思，例如lua的table实现，是结合了两个方法的非常优雅的实现。根据装载因子扩容一般保持在2，在占用空间较大时慢慢缩减为1.5，1.25……如golang的实现。为了避免rehash时的延迟，可以使用先分配，后逐步散列的方法，redis就是使用这个方法的。
字符串是编程中一定会出现的问题，变种非常多，反转，反转单词，字串，最长字串，最长子序列等等，有时解决问题需要多种数据结构与算法的结合。</div>2019-02-08</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/1e/b3/25b7984c.jpg" width="30px"><span>hopeful</span> 👍（4） 💬（0）<div>#朴素字符串匹配算法
def nmatching(t, p):
#    t代表主串，p代表模式串
    i = 0
    j = 0
    n = len(t)
    m = len(p)
    while i &lt; n and j &lt; m:
        if t[i] == p[j]:
            i = i+1
            j = j+1
        else:
            i = i-j+1
            j = 0        #i-j+1是关键，遇字符不等时将模式串t右移一个字符
    if j == m:                     
        return i-j       #找到一个匹配，返回索引值
    return -1            #未找到，返回-1</div>2019-03-11</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/13/00/6f/aacb013d.jpg" width="30px"><span>黄丹</span> 👍（4） 💬（0）<div>王争老师，新年的第四天快乐，已经很晚了，祝您好梦！
关于基于链表法解决冲突的散列表，就是使用一个数组，将值散列到数组下标上，但数组的每个值又是一个链表的头结点，当遇到冲突时就遍历该头结点后链表。其实java中hashmap底层的实现原理就是一个基于链表解决冲突的动态扩容的数组。大家有兴趣可以自己实现一下hashmap的底层数据结构，还是很有收获的。
今天leetcode上的三题都是关于字符串的，下面是我的解题思路和代码
1. Reverse String （反转字符串）
解题思路：这一题要求使用O(1)的空间将字符串进行反转，就是原地反转字符串，对字符串s[0…n-1]来说当i&lt;n&#47;2;将i与n-1-i位置的字符进行互换就行.
代码： https:&#47;&#47;github.com&#47;yyxd&#47;leetcode&#47;blob&#47;master&#47;src&#47;leetcode&#47;strings&#47;Problem344_ReverseString.java
2. Reverse Words in a String （翻转字符串里的单词）
解题思路：这一题我用的是java中的StringBuilder处理字符串，先用split函数将字符串按空格分开，但是当有多个连续空格时，一定要注意这种不能当做单词处理，要检查一下。
代码： https:&#47;&#47;github.com&#47;yyxd&#47;leetcode&#47;blob&#47;master&#47;src&#47;leetcode&#47;strings&#47;Problem151_ReverseWordsInString.java
3. String to Integer (atoi) 字符串转换整数 (atoi)）
解题思路：将字符串转化为整数,首先是对数字前面的+&#47;-进行处理，遍历字符串，如果不是数字字符就break，自己不懂得地方在于如何将大于INT.MAX 的值转化为 INT.MAX,将INT.MIN的值化为 INT.MIN，我自己想到的解法是用更高精度的long去保存，然后转化成int类型的值
代码： https:&#47;&#47;github.com&#47;yyxd&#47;leetcode&#47;blob&#47;master&#47;src&#47;leetcode&#47;strings&#47;Problem8_atoi.java
</div>2019-02-08</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/11/38/2b/9db9406b.jpg" width="30px"><span>星夜</span> 👍（2） 💬（0）<div>&#47;&#47; 实现朴素的字符串匹配算法
public int simpleMatch(String main, String pattern) {
    if (null == main || null == pattern || main.length() &lt; pattern.length()) {
        return -1;
    }

    char[] mainChars = main.toCharArray();
    char[] patternChars = pattern.toCharArray();
    int left = 0, right = 0;
    while (left &lt;= mainChars.length - patternChars.length) {
        int index = right - left;
        if (mainChars[right] != patternChars[index]) {
            right = ++left;
            continue;
        }

        if (index == patternChars.length - 1) {
            return left;
        }
        right++;
    }
    return -1;
}</div>2020-11-26</li><br/><li><img src="" width="30px"><span>kai</span> 👍（2） 💬（0）<div>哇塞，老师太牛了，过年都在更新，一直在跟着老师的课程在总结归纳，同时找来题目在练习，这个专栏很牛~</div>2019-02-08</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/f2/aa/32fc0d54.jpg" width="30px"><span>失火的夏天</span> 👍（1） 💬（0）<div>LRU缓存淘汰算法
    private class Node{
        private Node prev;
        private Node next;
        private int key;
        private int value;

        Node(int key,int value){
            this.key = key;
            this.value = value;
        }
    }

    private Node head;&#47;&#47; 最近最少使用，类似列队的头，出队
    private Node tail;&#47;&#47; 最近最多使用，类似队列的尾，入队
    private Map&lt;Integer,Node&gt; cache;
    private int capacity;

    public LRUCache(int capacity) {
        this.cache = new HashMap&lt;&gt;();
        this.capacity = capacity;
    }

    public int get(int key) {
        Node node = cache.get(key);
        if(node == null){
            return -1;
        }else{
            moveNode(node);
            return node.value;
        }
    }

    public void put(int key, int value) {
        Node node = cache.get(key);
        if (node != null){
            node.value = value;
            moveNode(node);
        }else {
            removeHead();
            addNode(new Node(key,value));
        }
        cache.put(key,node);
    }

    private void removeHead(){
        if (cache.size() == capacity){
            Node tempNode = head;
            cache.remove(head.key);
            head = head.next;
            tempNode.next = null;
            if (head != null)
                head.prev = null;
        }
    }

    private void addNode(Node node){
        if (head == null)
            head = tail = node;
        else
            addNodeToTail(node);
    }

    private void addNodeToTail(Node node){
        node.prev = tail;
        tail.next = node;
        tail = node;
    }

    private void moveNode(Node node){
        if(head == node &amp;&amp; node != tail){
            head = node.next;
            head.prev = null;
            node.next = null;
            addNodeToTail(node);
        }else if (tail == node){
        }else {
            node.prev.next = node.next;
            node.next.prev = node.prev;
            node.next = null;
            addNodeToTail(node);
        }
    }
}</div>2019-02-08</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/0f/ec/9d/4d705f03.jpg" width="30px"><span>C_love</span> 👍（1） 💬（0）<div>Reverse Words in a String

public class Solution {
    public String reverseWords(String s) {
        final List&lt;String&gt; words = new ArrayList&lt;&gt;();
        final char[] charArray = s.toCharArray();
        
        int start = 0;
        int end = 0;
        while (end &lt; s.length()) {
            if (&#39; &#39; == charArray[end]) {
                if (start != end) {
                    words.add(getWord(charArray, start, end));
                    start = end;
                }
                start++;
                end++;
            } else {
                end++;
            }
        }
        
        if (start != end) {
            words.add(getWord(charArray, start, end));
        } 
        
        Collections.reverse(words);
        return String.join(&quot; &quot;, words);
    }
    
    private String getWord(final char[] charArray, final int start, final int end) {
        char[] tmp = new char[end - start];
        int pos = 0;
        for(int i = start; i &lt; end; i++) {
            tmp[pos++] = charArray[i];
        }
        return new String(tmp);
    }
}</div>2019-02-08</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/17/35/c2/6f225ccc.jpg" width="30px"><span>杨建斌(young)</span> 👍（0） 💬（0）<div>反转字符串
String[] arr = new String[]{&quot;H&quot;,&quot;a&quot;,&quot;n&quot;,&quot;n&quot;,&quot;a&quot;,&quot;h&quot;};
        int start = 0;
        int end = arr.length - 1;
        while (start &lt; end &amp;&amp; start &gt;= 0 &amp;&amp; end &gt;= 0) {
            String s = arr[start];
            String e = arr[end];
            arr[end] = s;
            arr[start] = e;
            start++;
            end--;
        }
        System.out.println(arr);</div>2023-06-29</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/1e/b3/25b7984c.jpg" width="30px"><span>hopeful</span> 👍（0） 💬（0）<div>#字符串转整数
class Solution:
    def myAtoi(self, str: str) :
        pattern = r&quot;[\s]*[+-]?[\d]+&quot;
        match = re.match(pattern, str)
        if match:
            res = int(match.group(0))
            if res &gt; 2 ** 31 - 1:
                res = 2 ** 31 -1
            if res &lt; - 2 ** 31:
                res = - 2 ** 31
        else:
            res = 0
        return res
</div>2019-03-11</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/1e/b3/25b7984c.jpg" width="30px"><span>hopeful</span> 👍（0） 💬（0）<div>#反转字符串
class Solution:
    def reverseString(self, s):
        low = 0
        high = len(s)-1
        while low &lt;= high:
            s[low] , s[high] = s[high] , s[low]
            low+=1
            high-=1
        return s</div>2019-02-19</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/23/f6/1ef70cab.jpg" width="30px"><span>你看起来很好吃</span> 👍（0） 💬（0）<div>字符串转换整数python实现：
import math

class Solution:
    def myAtoi(self, str: &#39;str&#39;) -&gt; &#39;int&#39;:
        result = 0

        i, N, former = 0, len(str), 1

        while i &lt; N:
            if str[i] != &#39; &#39;:
                break        
            i += 1
            
        if i &lt; N and (str[i] == &#39;-&#39; or str[i] == &#39;+&#39;):
            former = -1 if str[i] == &#39;-&#39; else 1
            i += 1
            
        while i &lt; N:
            if str[i].isdigit():
                result = result * 10 + int(str[i])
                i += 1
            else:
                break

        result = result * former
        if result &gt; (math.pow(2, 31) * -1) and result &lt; (math.pow(2,31) - 1):
            return result
        elif former &gt; 0 :
            return int(math.pow(2,31) - 1)
        else:
            return int(math.pow(2,31) * -1)</div>2019-02-10</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/23/f6/1ef70cab.jpg" width="30px"><span>你看起来很好吃</span> 👍（0） 💬（0）<div>反转字符串python实现：
class Solution:
    def reverseString(self, s: &#39;List[str]&#39;) -&gt; &#39;None&#39;:
        &quot;&quot;&quot;
        Do not return anything, modify s in-place instead.
        &quot;&quot;&quot;
        i, N = 0, len(s)
        while i &lt; N&#47;&#47;2:
            s[i], s[N-1-i] = s[N-1-i], s[i]
            i += 1
            
        print(s)</div>2019-02-09</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/04/9a/f2c0a206.jpg" width="30px"><span>ext4</span> 👍（0） 💬（0）<div>反转字符串
class Solution {
public:
    string reverseString(string s) {
        int length = s.length();
        if (length &lt; 2) {
            return s;
        }
        int i = 0, j = length - 1;
        char temp;
        while (i &lt; j) {
            temp = s[i];
            s[i] = s[j];
            s[j] = temp;
            i++;
            j--;
        }
        return s;
    }
};</div>2019-02-09</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/10/94/47/75875257.jpg" width="30px"><span>虎虎❤️</span> 👍（0） 💬（0）<div>itoa

public class Solution {
	public int myAtoi(String str) {
		if (str.isEmpty())
			return 0;
		str = str.trim();
		int i = 0, ans = 0, sign = 1, len = str.length();
		if (str.charAt(i) == &#39;-&#39; || str.charAt(i) == &#39;+&#39;)
			sign = str.charAt(i++) == &#39;+&#39; ? 1 : -1;
		for (; i &lt; len; ++i) {
			int tmp = str.charAt(i) - &#39;0&#39;;
			if (tmp &lt; 0 || tmp &gt; 9)
				break;
			if (ans &gt; Integer.MAX_VALUE &#47; 10
					|| (ans == Integer.MAX_VALUE &#47; 10 &amp;&amp; Integer.MAX_VALUE % 10 &lt; tmp))
				return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
			else
				ans = ans * 10 + tmp;
		}
		return sign * ans;
	}
}</div>2019-02-08</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/0f/7c/16/4d1e5cc1.jpg" width="30px"><span>mgxian</span> 👍（0） 💬（0）<div>反转字符串 go 语言实现
package main

import &quot;fmt&quot;

func reverseString(s []byte) {
	length := len(s)
	for i := 0; i &lt; length&#47;2; i++ {
		s[i], s[length-i-1] = s[length-i-1], s[i]
	}
}

func main() {
	testString := []byte{&#39;h&#39;, &#39;e&#39;, &#39;l&#39;, &#39;l&#39;, &#39;o&#39;}
	fmt.Println(string(testString[:]))
	reverseString(testString)
	fmt.Println(string(testString[:]))
}
</div>2019-02-08</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/10/1d/13/31ea1b0b.jpg" width="30px"><span>峰</span> 👍（0） 💬（0）<div>反转字符串
class Solution {
    public void reverseString(char[] s) {
        int start = 0;
        int end = s.length - 1;
        while(start &lt; end){
            swap(s,start,end);
            start++;
            end--;
        }
    }
    
    public void swap(char[] array,int a,int b){
        char tmp = array[a];
        array[a] = array[b];
        array[b] = tmp;
    }
    
    
}</div>2019-02-08</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/13/03/f7/3a493bec.jpg" width="30px"><span>老杨同志</span> 👍（0） 💬（0）<div>class Solution {
&#47;&#47;反转字符串
    public void reverseString(char[] s) {
        if(s==null||s.length&lt;2){
            return;
        }
        int l=0;
        int r=s.length-1;
        while (l&lt;r){
            char tmp = s[l];
            s[l] = s[r];
            s[r] = tmp;
            l++;
            r--;
        }
    }
}</div>2019-02-08</li><br/>
</ul>