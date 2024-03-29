---
title: LeetCode的菜鸡实录
date: 2023-03-20 13:00:23
tags: 算法
categories: 算法

---

# LeetCode的菜鸡实录

## Tips：

**双指针的思路：全部从头走的快慢双指针（283），两头向中间走的双指针（本题），快慢指针（慢指针一次动一步，快指针一次动两步，链表，141）**

## 1、两数之和

**重点：HashMap(c++ unordered_map)**

通过查找之前已经加入的数字，判断当前加入的数字是否能和之前已经加入的数字进行组合，得到结果。不必先全部加入再进行逐一查找的操作，减少次数，将两遍哈希表变为一遍即可完成。

**一遍哈希表**

事实证明，我们可以一次完成。在进行迭代并将元素插入到表中的同时，我们还会回过头来检查表中是否已经存在当前元素所对应的目标元素。如果它存在，那我们已经找到了对应解，并立即将其返回。

**复杂度分析：**

- 时间复杂度：O(n)， 我们只遍历了包含有 n 个元素的列表一次。在表中进行的每次查找只花费 O(1) 的时间。
- 空间复杂度：O(n)， 所需的额外空间取决于哈希表中存储的元素数量，该表最多需要存储 n 个元素。

**延伸：迭代器**

**二刷：居然还是没有想到HashMap上去，想的是排序之后左右找，调两端相差最小的往中间不断靠拢，这种时间复杂度至少也是O(nlogn)了。想到HashMap空间换时间之后还是很好做的。这种查找一对结果的问题都可以用HashMap想下有没有空间换时间的可能。题目中说明了可以假设每个输出只对应一个答案，所以相当于默认了只有一对组合，不存在组合中的某个数字出现两次，所以避免了有hashmap被put插入更新之后再选中的情况。**

三刷：还是没想到Hashmap，思路僵硬。另外就是语法问题了，c++的unordered_map以及vector，具体看c++stl那个文件吧。

## 2、两数相加

**重点：链表的使用**

常规思路，主要问题是链表的使用，如何操作链表，另外留意进位以及何时终止的判断。

**复杂度分析**

- 时间复杂度：O(max⁡(m,n))，假设 m 和 n 分别表示 l1 和 l2 的长度，上面的算法最多重复 max⁡(m,n) 次。
- 空间复杂度：O(max⁡(m,n))， 新列表的长度最多为 max⁡(m,n)+1。

**延伸：结构体**

定义结构体，初始化结构体，结构体赋值，结构体函数

**二刷：模拟题，没啥难度，注意各种情况即可，这次Java一开始写的其实不够美观，最早用c++写的那个版本循环和条件判断更为简略清晰，最后用java照着c++改了一下再交了一次。（其实实现的功能到了，写的丑还算可以接受吧....自我安慰中）**

## 3、无重复字符的最长子串

**重点：思路**

实际上就是从非重复的开始往后找，找到第一个在当前的串中重复的字符。因为要找最长，所以比较当前串的长度和现有的最长长度，取其大者。然后直接将位置挪到串中发生重复的字符后的那位，继续向后找即可。

**方法三：优化的滑动窗口**

上述的方法最多需要执行 2n 个步骤。事实上，它可以被进一步优化为仅需要 n 个步骤。我们可以定义字符到索引的映射，而不是使用集合来判断一个字符是否存在。 当我们找到重复的字符时，我们可以立即跳过该窗口。

也就是说，如果 s[j] 在 [i,j) 范围内有与 j′ 重复的字符，我们不需要逐渐增加 i 。 我们可以直接跳过 [i，j′] 范围内的所有元素，并将 i 变为 j′+1。

**复杂度分析**

- 时间复杂度：O(n)，索引 j 将会迭代 n 次。
- 空间复杂度（HashMap）：O(min(m,n))，与之前的方法相同。
- 空间复杂度（Table）：O(m)，m 是字符集的大小。

这里使用unordered_map、字符串还有unordered_set都可以。核心思想是相同的，unordered_set思路更为明显。

**延伸：unordered_set**

**二刷：依旧没想到最优办法，用的是队列/链表(LinkedList)实现的，优化方法还一下子没看懂。。。下面这个是速度最快的方法，也算是一种空间换时间吧。速度之所以快是因为没有不断复杂的插入删除，只是下标做差在算长度，内存消耗就是建的表的大小。内存消耗实际上和链表方法相比基本一样，并没有多。这个应该也算是Hash的思想了。二刷最初的时候看着题目分类提示想过用hashmap但是想着存的并不是原始下标，而是存了新串的下标，就导致了还要更新，速度还不如链表，然后就放弃了。**

```java
// 方法三等价思想的java实现
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int len=s.length();
        int [] map=new int[128]; // 对应ASCII表的值，保证能够塞满
        for(int i=0;i<128;i++){
            map[i]=-1;
        }
        int L=0,R=0;
        int max_len=0;
        for(;R<len;R++ ){
            if( map[s.charAt(R)]!=-1){  // 这个字母在之前出现过
                L=Math.max(L, map[s.charAt(R)]+1);  // 这个字符最近一次出现的位置是不是在当前计算的字符串起始位置右侧，是否要修改字符串的头的位置。整个程序的关键点就在这，就是要想通出现多次的相同字符，每次计算的过程中。只有最右侧也就是最新的那个字符才可能会对字符串的长度计算产生影响。
            }
            map[s.charAt(R)]=R;  // 始终保持出现的字符串表示的是最近一次出现的位置

            max_len=Math.max(max_len, R-L+1);
        }
        return max_len;
    }
}
// 方法很巧妙，最重要的是要有这种空间换时间的思想，做题一直死脑筋不会选择性放弃空间复杂度也是问题，毕竟内购不够可以加钱解决，而时间不够买不了啊！/滑稽
```

## 4、寻找两个有序数组的中位数

**重点：思路+边界分析**

第一道困难就被锤到自闭…

其实大概的思路是想到了，可做，但是还是脑筋太死，其实变通一下可能完整思路就有了。当然了，对着例程都错了半天，真的是把以前的东西都还光了。

题目和之前算法课上面的等长串求中位数有些类似，因为是有序的，所以肯定是在某点进行分割，只是不等长的情况下需要稍微进行一些修改。

[题解超长](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/)

用自己的语言来解释下：

中位数肯定是能够使两个数组总的个数长度左右平分的，所以相当于是在两个数组之间有分隔线把左右的内容隔开。这里为了保证之后进行判断的时候可以不用讨论多种情况，所以上来第一步就是把两个数组的个数多少确定了下来，个数少的为nums1，多的为nums2。这里特别注意，**交换完之后把数组的长度也要换**，智障如我对着样例改的时候死活不对，就是忘了交换两个数组的长度。由于在两个数组中的分割线实际上是有关联的。为了保证大于和小于的个数相同，nums1中的分割线发生了移动，相应的nums2中的分割线也要发生变动。这里使选择较小的那个作为自变量，多的数组作为因变量。（关于在较小的数组端点也是有可能的特殊情况）在分割线的左右之间是具有关联的，这里的关联是指不同的数组之间有着大小关系（同数组之间有序肯定是有关系的），通过这样的限制条件来进行分割线的移动。

这道题目等过一段时间可以重做一遍，到时候应该会重新理解一下，然后再解释一遍，现在只是理解了大概的思想，但是在具体的问题中还是存在很多问题，比如对于边界问题限制的理解。

**延伸：中位数、==双有序数组找第k小值==**

```java
// 双有序数组找第k小值
public int getKthElement(int[] nums1, int[] nums2, int k) {
    /* 主要思路：要找到第 k (k>1) 小的元素，那么就取 pivot1 = nums1[k/2-1] 和 pivot2 = nums2[k/2-1] 进行比较
     * 这里的 "/" 表示整除
     * nums1 中小于等于 pivot1 的元素有 nums1[0 .. k/2-2] 共计 k/2-1 个
     * nums2 中小于等于 pivot2 的元素有 nums2[0 .. k/2-2] 共计 k/2-1 个
     * 取 pivot = min(pivot1, pivot2)，两个数组中小于等于 pivot 的元素共计不会超过 (k/2-1) + (k/2-1) <= k-2 个
     * 这样 pivot 本身最大也只能是第 k-1 小的元素
     * 如果 pivot = pivot1，那么 nums1[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums1 数组
     * 如果 pivot = pivot2，那么 nums2[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums2 数组
     * 由于我们 "删除" 了一些元素（这些元素都比第 k 小的元素要小），因此需要修改 k 的值，减去删除的数的个数
     */

     int length1 = nums1.length, length2 = nums2.length;
     int index1 = 0, index2 = 0;
     int kthElement = 0;

     while (true) {
         // 边界情况
         if (index1 == length1) {
             return nums2[index2 + k - 1];
         }
         if (index2 == length2) {
             return nums1[index1 + k - 1];
         }
         if (k == 1) {
             return Math.min(nums1[index1], nums2[index2]);
         }

         // 正常情况
         int half = k / 2;
         int newIndex1 = Math.min(index1 + half, length1) - 1;
         int newIndex2 = Math.min(index2 + half, length2) - 1;
         int pivot1 = nums1[newIndex1], pivot2 = nums2[newIndex2];
         if (pivot1 <= pivot2) {
             k -= (newIndex1 - index1 + 1);
             index1 = newIndex1 + 1;
         } else {
             k -= (newIndex2 - index2 + 1);
             index2 = newIndex2 + 1;
         }
     }
}
```

**二刷：还是没做出来，然后看了方法二也就是复杂但更快的那个方法理解依旧不到位，感觉方法一的刚满足时间复杂度的方法相对来说更好理解。这题先不再交了，留着过两天再看，至少要能用方法一那个完成实现吧。方法一的关键应该是先转化为找第n小问题，这样一次性就解决了之后递归里面的奇偶判断，之后找第n小的思路当然就是二分。注意这里实际上没用递归而是用的等价形式，节省栈空间，而且while内也只需要保存下标就可以了。**

## 5、最长回文子串

**重点：回文**

其实在想到镜像做法之后，一度认为是否还能够继续节省时间复杂度，最后忍不住先瞄了一眼题解，发现复杂度没问题之后就写了。其实最后过的代码虽然复杂度相同，但是次数仍旧可以从两次的遍历降低为一次，就同第一题hashmap的优化一样。

**方法四：中心扩展算法**

事实上，只需使用恒定的空间，我们就可以在 O(n2) 的时间内解决这个问题。

我们观察到回文中心的两侧互为镜像。因此，回文可以从它的中心展开，并且只有 2n−1 个这样的中心。

你可能会问，为什么会是 2n−1 个，而不是 n 个中心？原因在于所含字母数为偶数的回文的中心可以处于两字母之间（例如 “abba” 的中心在两个 ‘b’ 之间）。

**复杂度分析**

- 时间复杂度：O(n2)， 由于围绕中心来扩展回文会耗去 O(n) 的时间，所以总的复杂度为 O(n2)。
- 空间复杂度：O(1)。

主要讲一下方法中将两次遍历减少为一次的思路吧，其实就是在一次的过程中对2位对称和3位对称均进行判断，不过需要特别注意如何控制左右边界的变化，具体可参考时间轴第二根的样例代码。

关于两次遍历降低为一次的实践留到之后复习的时候再进行尝试。

**延伸：动态规划、Manacher算法**

Manacher是O(n)的…这里贴两个讲解，待有空的时候可以理解一下。

[讲解1](https://segmentfault.com/a/1190000008484167?utm_source=tag-newest) [讲解2](https://www.jianshu.com/p/116aa58b7d81)

动态规划是肯定需要掌握的。在思考的时候其实没太往这方面去想，不过这个思想肯定还是要有的。[题解](https://leetcode-cn.com/problems/longest-palindromic-substring/solution/)中的方法三

看题解的时候，动态方程基本能理解，不过由于不是自己列的，所以还是需要等以后做到用动态规划的题目时再判断是否掌握。

## 6、Z字型变换

这题…做出来了，但是问题是这复杂度做炸了，其实看了题解之后发现思路没问题啊，就是手法用的不对，真是要死…

**讲道理，我写的代码真是又臭又长…除了能苟过样例还有啥用**

**方法二：按行访问**

**思路**

按照与逐行读取 Z 字形图案相同的顺序访问字符串。

**算法**

首先访问 `行 0` 中的所有字符，接着访问 `行 1`，然后 `行 2`，依此类推…

对于所有整数 k，

- 行 0 中的字符位于索引 k  (2⋅numRows−2)处;
- 行 numRows−1 中的字符位于索引 k  (2⋅numRows−2)+numRows−1 处;
- 内部的 行 i 中的字符位于索引 k  (2⋅numRows−2)+i 以及 (k+1)(2⋅numRows−2)−i 处;

**复杂度分析**

- 时间复杂度：O(n)，其中 n==len(s)。每个索引被访问一次。
- 空间复杂度：O(n)。对于 cpp 实现，如果返回字符串不被视为额外空间，则复杂度为 O(1)。

**延伸：边界值分析**

其实就是字符串长度为1的情况要单独讨论下

## 7、整数反转

**难度：简单** 是的，没错，就是简单，心态炸了。

思路无难度，代码无难度，然后失去理智疯狂交疯狂炸…

反正就是取模然后*10，唯一注意的就是边界值分析。

INT_MAX和INT_MIN，就是在下一次运算前先用这两个判断下会不会炸，会超过就直接返回0。另外因为**只能存储得下 32 位的有符号整数**，所以想用更大的类型偷鸡是不存在的…

[题解](https://leetcode-cn.com/problems/reverse-integer/solution/)

**延伸：INT_MAX和INT_MIN**

用这个可比pow什么方便多了

## 8、字符串转换整数

题解都没…

就是逐个识别字符，要一个符号标志和一个首位判断。不是首位且不在0-9之间返回，是首位为空直接找下一位，是首位非空，判断是否是符号，如果为-，，符号标志置1，消除首位标识，如果为+，仅消除首位标识即可，如果直接是数字就直接开始记录并消除首位标识。如果不是首位且读到字母，运算方式很简单，这里和之前的题目一样，要考虑超出范围直接返回上或下临界点。小于0用-，大于等于0用加。特别注意”-00001”这类情况，读到符号后的第一个非0数字才开始运算，并消除符号位，不然用0运算，符号位就会消失，正负发生错误。

总的来说细心+注意细节就行了，碰到的一些临界点的情况错过一次之后希望以后就能记住，然后日后写代码的时候多注意。

**延伸：NULL**

## 9、回文数

其实还可以用比过数据的代码更加节约的方法，也就是并不需要将整个数完全翻转完再判断，只需要翻转一般的数字比较即可。

其他需要注意的就是不要超过INT_MAX爆炸即可。

**方法：反转一半数字**

**思路**

映入脑海的第一个想法是将数字转换为字符串，并检查字符串是否为回文。但是，这需要额外的非常量空间来创建问题描述中所不允许的字符串。

第二个想法是将数字本身反转，然后将反转后的数字与原始数字进行比较，如果它们是相同的，那么这个数字就是回文。 但是，如果反转后的数字大于 int.MAX，我们将遇到整数溢出问题。

按照第二个想法，为了避免数字反转可能导致的溢出问题，为什么不考虑只反转 int\text{int}int 数字的一半？毕竟，如果该数字是回文，其后半部分反转后应该与原始数字的前半部分相同。

例如，输入 `1221`，我们可以将数字“12**21**”的后半部分从“**21**”反转为“**12**”，并将其与前半部分“12”进行比较，因为二者相同，我们得知数字 `1221` 是回文。

让我们看看如何将这个想法转化为一个算法。

**算法**

首先，我们应该处理一些临界情况。所有负数都不可能是回文，例如：-123 不是回文，因为 `-` 不等于 `3`。所以我们可以对所有负数返回 false。

现在，让我们来考虑如何反转后半部分的数字。 对于数字 `1221`，如果执行 `1221 % 10`，我们将得到最后一位数字 `1`，要得到倒数第二位数字，我们可以先通过除以 10 把最后一位数字从 `1221` 中移除，`1221 / 10 = 122`，再求出上一步结果除以10的余数，`122 % 10 = 2`，就可以得到倒数第二位数字。如果我们把最后一位数字乘以10，再加上倒数第二位数字，`1 * 10 + 2 = 12`，就得到了我们想要的反转后的数字。 如果继续这个过程，我们将得到更多位数的反转数字。

现在的问题是，我们如何知道反转数字的位数已经达到原始数字位数的一半？

我们将原始数字除以 10，然后给反转后的数字乘上 10，所以，当原始数字小于反转后的数字时，就意味着我们已经处理了一半位数的数字。

```
public class Solution {
    public bool IsPalindrome(int x) {
        // 特殊情况：
        // 如上所述，当 x < 0 时，x 不是回文数。
        // 同样地，如果数字的最后一位是 0，为了使该数字为回文，
        // 则其第一位数字也应该是 0
        // 只有 0 满足这一属性
        if(x < 0 || (x % 10 == 0 && x != 0)) {
            return false;
        }

        int revertedNumber = 0;
        while(x > revertedNumber) {
            revertedNumber = revertedNumber * 10 + x % 10;
            x /= 10;
        }

        // 当数字长度为奇数时，我们可以通过 revertedNumber/10 去除处于中位的数字。
        // 例如，当输入为 12321 时，在 while 循环的末尾我们可以得到 x = 12，revertedNumber = 123，
        // 由于处于中位的数字不影响回文（它总是与自己相等），所以我们可以简单地将其去除。
        return x == revertedNumber || x == revertedNumber/10;
    }
}
```

**复杂度分析**

- 时间复杂度：O(log⁡10(n))， 对于每次迭代，我们会将输入除以10，因此时间复杂度为 O(log⁡10(n))。
- 空间复杂度：O(1)。

直接返回false的if条件中能扩得越大越好，小于0和末位为0但原数不为0都可以直接清洗掉。

返回时候的或是用来规避奇数个位数和偶数个位数的情况。

**延伸：边界 INT_MAX判断**

## 10、正则表达式匹配(待解)

没写出来，最后肯定是涉及递归了，但是按照我的写法逻辑上应该有问题。

```
bool isMatch(string s, string p) {
    int temp1 = 0,temp2 = 0;
    while(temp2 < p.length()){
        if(p[temp2] >= 'a' && p[temp2] <= 'z'){
            if(temp2 + 1 < p.length() && p[temp2 + 1] == '*'){
                while(p[temp1++] == p[temp2])
                temp2 += 2;
            } else if(temp2 + 1 == p.length()){
                if(p[temp1] == p[temp2] && temp1 + 1 == s.length())
                    return true;
                else
                    return false;
            }
        } else if(p[temp2] == '.'){
            if(temp2 + 1 < p.length() && p[temp2 + 1] == '*'){
                if(temp2 + 2 == p.length())
                    return true;
                else{
                    while(!(p[temp2] >= 'a' && p[temp2] <= 'z')){
                        temp2++;
                    }
                    //  此处应该是一个进入的递归，但是如何判断是在第几个字母分割...
                }
            } else if(temp2 + 1 == p.length()){
                if(temp1 + 1 == s.length())
                    return true;
                else
                    return false;
            }
        }
    }
}
```

网页上也没有题解，下面是过得了之后别人的范例

```
class Solution {
public:
    vector<vector<int>> f;
    int m,n;
    bool isMatch(string s, string p) {
        n=s.size();
        m=p.size();
        f=vector<vector<int>>(n+1,vector<int>(m+1,-1));
        return dp(0,0,s,p);
    }

    int dp(int x,int y,string &s,string &p)
    {
        if(f[x][y]!=-1) 
            return f[x][y];
        if(y==m)
            return f[x][y]=x==n;
        bool first_match=x<n&&(s[x]==p[y]||p[y]=='.');
        bool ans;
        if(y+1<m&&p[y+1]=='*')
        {
            ans=dp(x,y+2,s,p)||first_match&&dp(x+1,y,s,p);
        }
        else
                ans=first_match&&dp(x+1,y+1,s,p);
        return f[x][y]=ans;
    }
};
```

应该使用的是动态规划，这个大概理解下思路吧…

## 11、盛最多水的容器

思考的时候其实闪过一丝双指针向中间靠的方法，但是没想下去。其实可以把这个问题理解为木桶问题（短板原理）。就是为了要能盛更多的容器，一定是在向中间靠拢的情况下，需要提升两侧板中较低的一侧板的高度，所以就能够知道是移动哪一侧的指针了。

**方法二：双指针法**

**算法**

这种方法背后的思路在于，两线段之间形成的区域总是会受到其中较短那条长度的限制。此外，两线段距离越远，得到的面积就越大。

我们在由线段长度构成的数组中使用两个指针，一个放在开始，一个置于末尾。 此外，我们会使用变量 maxarea 来持续存储到目前为止所获得的最大面积。 在每一步中，我们会找出指针所指向的两条线段形成的区域，更新 maxarea，并将指向较短线段的指针向较长线段那端移动一步。

**复杂度分析**

- 时间复杂度：O(n)，一次扫描。
- 空间复杂度：O(1)，使用恒定的空间。

**延伸：迭代器**

本来想用迭代器熟练下，结果没想到还wa了一发。迭代器中的begin是首个，但是end并不是最后一个元素，而是指向最后一个元素之后。另外在进行数学比较的时候，需要解引用（即*iter）。如果直接使用是没有办法成功比较内容的，需要用如下形式：

```c++
if (*begin == min(*begin, *end)) {
	begin++;
} else {
  end--;
}
```

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int l = 0, r = height.size() - 1;
        int ans = 0;
        while (l < r) {
            int area = min(height[l], height[r]) * (r - l);
            ans = max(ans, area);
            if (height[l] <= height[r]) {
                ++l;
            }
            else {
                --r;
            }
        }
        return ans;
    }
};
```

```c++
class Solution {
public:
    int maxArea(vector<int> &height) {
        auto begin = height.begin();
        auto end = height.end();
        end--;
        int maxArea = 0;
        int length = height.size() - 1;
        while (begin != end) {
            if (maxArea < min(*begin, *end) * length)
                maxArea = min(*begin, *end) * length;
            if (*begin == min(*begin, *end)) {
                begin++;
            } else {
                end--;
            }
            length--;
        }
        return maxArea;
    }
};
```

```java
class Solution {
	public int maxArea(int[] a) {
		int max = 0;
		for (int i = 0, j = a.length - 1; i < j; ) {
			int minHeight = a[i] < a[j] ? a[i ++] : a[j --];
			int area = (j - i + 1) * minHeight;
			max = Math.max(max, area);
		}
		return max;
	}
}
```

当两侧指针处的柱子长度一样的时候，其实挪左和挪右是无所谓的。如果里面的柱子比原本的矮，那一定面积比原本的小，先挪啥都无所谓，如果里面的柱子比原本长，那么当前的柱子高度就锁死了最小高度，宽度还变小，必然导致面积比原本小。（**并不会因为里面的柱子变得比原本高，就使得最小高度发生变化**）

**双指针的思路：全部从头走的快慢双指针（283），两头向中间走的双指针（本题），快慢指针（慢指针一次动一步，快指针一次动两步，链表，141）**

二刷or三刷？：知道双指针的思路之后实现还是很简单的。这题主要就是要了解并且记住双指针方法的一种形式吧，当然了双指针的思路有很多，这边上面有写，另外在文档最顶上的Tips处也写了，之后如果还有其它相关于双指针的思路都会补充到这个文章最顶上面的tips处。这里左右双指针向中间走的思想虽然可以是很简单的，但是演变出来的变化情况其实可以有很多种，比如说三指针固定一个，另外两个指针从两端向中间走（15题），另外是不是还有可能双指针往中间走，但是走的速度不一样啥的，情况反正可以有很多，目前总结的双指针的三个主要思想就是上面核心的这三条，想办法融会贯通。

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int length = height.size(), head = 0, back = length - 1;
        int ans = 0;
        while (head < back) {
            ans = max(ans, min(height[head], height[back]) * (back - head));
            if (height[head] < height[back]) {
                ++ head;
            } else {
                -- back;
            }
        }
        return ans;
    }
};
```

## 12、整数转罗马数字

这题压根没包含任何算法吧…

方法就是通过不断的循环把原本的数依次剪去满足的最大数值，直到传入的数值减为0。关于部分特殊情况

- `I` 可以放在 `V` (5) 和 `X` (10) 的左边，来表示 4 和 9。
- `X` 可以放在 `L` (50) 和 `C` (100) 的左边，来表示 40 和 90。
- `C` 可以放在 `D` (500) 和 `M` (1000) 的左边，来表示 400 和 900。

只需要在判断的时候先以较低的边界设为界，再在while中间用if-else去判断即可。

具体的没什么可解释的，直接贴代码如下：

```
class Solution {
public:
    string intToRoman(int num) {
        string ans;
        while (num >= 900) {
            if (num < 1000) {
                ans += "CM";
                num -= 900;
            } else {
                ans += "M";
                num -= 1000;
            }
        }
        while (num >= 400) {
            if (num < 500) {
                ans += "CD";
                num -= 400;
            } else {
                ans += "D";
                num -= 500;
            }
        }
        while (num >= 90) {
            if (num < 100) {
                ans += "XC";
                num -= 90;
            } else {
                ans += "C";
                num -= 100;
            }
        }
        while (num >= 40) {
            if (num < 50) {
                ans += "XL";
                num -= 40;
            } else {
                ans += "L";
                num -= 50;
            }
        }
        while (num >= 9) {
            if (num < 10) {
                ans += "IX";
                num -= 9;
            } else {
                ans += "X";
                num -= 10;
            }
        }
        while (num >= 4) {
            if (num < 5) {
                ans += "IV";
                num -= 4;
            } else {
                ans += "V";
                num -= 5;
            }
        }
        while (num > 0) {
            ans += "I";
            num--;
        }
        return ans;
    }
};
```

写的有点长，其实可以通过一些方式把代码写得更精简，不过从执行上来说不会有差别。

## 13、罗马数字转整数

和上一题一样超级水题，刷完完事。

规则和12中所描述到的题目相同。

```
class Solution {
public:
    int romanToInt(string s) {
        int i = 0, len = s.length(), ans = 0;
        while (i < len) {
            if (i + 1 == len) {
                if (s[i] == 'I')
                    ans += 1;
                else if (s[i] == 'V')
                    ans += 5;
                else if (s[i] == 'X')
                    ans += 10;
                else if (s[i] == 'L')
                    ans += 50;
                else if (s[i] == 'C')
                    ans += 100;
                else if (s[i] == 'D')
                    ans += 500;
                else if (s[i] == 'M')
                    ans += 1000;
                i++;
            } else {
                if (s[i] == 'I' && s[i + 1] == 'V') {
                    ans += 4;
                    i += 2;
                } else if (s[i] == 'I' && s[i + 1] == 'X') {
                    ans += 9;
                    i += 2;
                } else if (s[i] == 'X' && s[i + 1] == 'L') {
                    ans += 40;
                    i += 2;
                } else if (s[i] == 'X' && s[i + 1] == 'C') {
                    ans += 90;
                    i += 2;
                } else if (s[i] == 'C' && s[i + 1] == 'D') {
                    ans += 400;
                    i += 2;
                } else if (s[i] == 'C' && s[i + 1] == 'M') {
                    ans += 900;
                    i += 2;
                } else if (s[i] == 'I') {
                    ans += 1;
                    i++;
                } else if (s[i] == 'V') {
                    ans += 5;
                    i++;
                } else if (s[i] == 'X') {
                    ans += 10;
                    i++;
                } else if (s[i] == 'L') {
                    ans += 50;
                    i++;
                } else if (s[i] == 'C') {
                    ans += 100;
                    i++;
                } else if (s[i] == 'D') {
                    ans += 500;
                    i++;
                } else if (s[i] == 'M') {
                    ans += 1000;
                    i++;
                }
            }

        }
        return ans;
    }
};
```

想看代码长度更短的可以去通过的样例里面参照，因为买啥难度就不折腾了。

## 14、最长公共前缀（树方法待解）

本来看是道简单，以为是暴力，但是感觉不大对，于是没忍住，先看了一眼答案。

首先是类似于二分搜索的方法，这个相对方便理解，易于实现。最长的公共前缀一定是小于等于字符串数组中的最短的一条字符串的。所以不妨在0到最短的字符串长度之间进行查找，使用二分的方法来进行实现。判断是不是最长公共前缀的方法就是将使用substr切分下来的字符串和字符串数组中第一个字符串切下来的部分（作为基准来与所有的字符串比）进行比较，只要其中有一条不同就是当前的最长公共前缀太长，high = middle - 1即可，如果都相同则low = middle + 1。

另外一种实现方式是通过树的形式来完成的。大概的意思就是找到第一个分叉之前的深度就是最长公共前缀，对于这个没做实现。可以等到以后复习数据结构的时候再回过来写这道题。

[题解较长且带图，只挂链接。](https://leetcode-cn.com/problems/longest-common-prefix/solution/)

**延伸：树的实现，这个等以后补**

其实这道题目因为可以直接在字符串之间切出子串进行比较，所以实际上和简单的搜索没什么区别，也因此可以借用二分的思想来完成。

## 15、三数之和（快排）

这题真是写到想骂人…反正一直到最后还是wa穿，一组超长的输入超了时间，本地是能跑出来的，应该就是因为我之前为了能先过多次偷懒把复杂度弄炸了。

主要借鉴评论中的一位大哥的想法，留一个思路吧。

**思路：**

1. 将数组排序
2. 定义三个指针，i，j，k。遍历i，那么这个问题就可以转化为在i之后的数组中寻找nums[j]+nums[k]=-nums[i]这个问题，也就将三数之和问题转变为二数之和—（可以使用双指针）

复杂度如果这样执行的话应该是O(n^2)

数组排序使用快排还是冒泡实际上在这里影响并不大，因为量级不会因为排序增加。这里贴个快排代码，快排当时学的时候就有点乱，这次写的时候果然就出问题了。

```
void quickSort(vector<int>& nums, int low, int high){
        if(high <= low)
            return;
        int i = low, j =high, key = nums[low];
        while(true){
            while(key > nums[++i]){
                if(i == high)
                    break;
            }
            while(key < nums[--j]){
                if(j == low)
                    break;
            }
            if(i >= j)
                break;
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
        int temp = nums[low];
        nums[low] = nums[j];
        nums[j] = temp;
        quickSort(nums, low, i - 1);
        quickSort(nums, j + 1, high);
    }
```

以上是快排代码，一定要理解，之后写题时有排序必用快排，争取在复习数据结构之前先把代码记熟、理解透彻。

这里的排序价值是在第二步中后两个数求和时体现的，因为如果是有序数列就能够做线性的查找，加上第一位数，两个整体就是O(n^2)的复杂度，从无脑的三次方可以减少到两次方。其中后两个数的查找和第一题相同。我本来是想直接用第一题代码的，但是后来发现，这里要求的全部的可能，对于第一题中代码要有所改变，本着想先把题过了的念头，就变成随手一个三层嵌套…实现线性的查找使用hash_map，这次查找需要进行到结束而不是找到一个即可。

总的来说思路就是以上这样，这题可能会在数据结构复习到排序算法部分的时候拿出来用来练习快排的代码书写。

**延伸：快排，hash_map实现两个数在数组中查找和的线性搜索**

### 补充：

被蔡大佬抬了一手，算是知道怎么过了，应该就是因为之前写的时候没有优化导致$O(n^2)$复杂度炸了。先贴代码再解释：

```
vector<vector<int>> threeSum(vector<int> &nums)
    {
        std::sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        int n = nums.size();
        for (int i = 0; i < n; i++)
        {
            int a = nums[i];
            int l = i + 1, r = n - 1;
            while (l < r)
            {
                int b = nums[l], c = nums[r];
                if (a + b + c == 0)
                {
                    ans.push_back({a, b, c});
                    while (l < r && nums[l] == b)
                        l++;
                    while (l < r && nums[r] == c)
                        r--;
                }
                else if (a + b + c > 0)
                {
                    r--;
                }
                else
                {
                    l++;
                }
            }
        }
        sort(ans.begin(), ans.end());
        ans.erase(unique(ans.begin(), ans.end()), ans.end());
        return ans;
    }
```

思路其实和之前所记录的类似。首先先进行排序，随后第一个数是使用枚举的方式从前向后遍历的，这花费了$O(n)$的时间复杂度，随后后面的字符串中就变成了在一个有序的数组中寻找两个数的和为一给定值的复杂度$O(n)$。这里代码中使用的是从两头向中间夹逼的方式。如果已经得到了三个数的和为零，那么实际上就是两端继续向中间进行搜寻，如果相同则继续向中间找，直到找到第一个不相同的元素（左指针与右指针都遵循此规则）。这里实际上就是卡掉了可能重复的组合（**实际上只是卡掉了当第一个数为这个数的情况下的重复组合**），因为在之前已经进行了排序，所以肯定可以保证向中间找有不同的元素的时候一定是不重复的一组新的元素对。如果三个数的和大于0，就减少最大值（左指针只能向中间走，所以必定会导致三数之和增加），如果三个数的和小于0，就增大最小值（右指针只能向中间走，所以必定会导致三数之和减少）。因为已经排序+两个指针只能向固定的方向移动，所以成功地将复杂度降为$O(n)$和之前的结合在一起就是$O(n^2)$。这里的$O(n)$实际上是**建立在了排完序的基础上**的，所以之前的理解并不准确，不过用hash_map应该还是能够将它加速到$O(n)$。

提一下关于最后sort和erase的价值，放一个样例。

```
[-1,0,1,2,-1,-4]
```

有两个重复的-1，当-1在做第一位数时，我们只能保证在这个情况下是没有重复的组合的，但是当我们取第二个-1时，还是会产生一个同样的组合，这是没有办法卡掉的，所以一种方法是在最后用以上程序的方式把内容卡掉，另一种方式就是想办法卡第一个数相同的情况。放一段改完的程序：

```
vector<vector<int>> threeSum(vector<int> &nums)
    {
        std::sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        int n = nums.size();
        for (int i = 0; i < n; i++)
        {
            int a = nums[i];
            int l = i + 1, r = n - 1;
            while (l < r)
            {
                int b = nums[l], c = nums[r];
                if (a + b + c == 0)
                {
                    ans.push_back({a, b, c});
                    while (l < r && nums[l] == b)
                        l++;
                    while (l < r && nums[r] == c)
                        r--;
                }
                else if (a + b + c > 0)
                {
                    r--;
                }
                else
                {
                    l++;
                }
            }
            while(i + 1 < nums.size() && nums[i] == nums[i + 1])
                i++;
        }
        return ans;
    }
```

一看跑出来的比例。哇，我又膨胀了！

反正就是第一位数也用相同即++的模式去过，这样可以减少掉其中无用的计算，同时不用sort。解释一下原因，因为如果前一个数和后一个数相同，后一个数之后的数组一定是之前一个数之后的数组的子数组，所以可能产生的所有数组的可能一定都已经在之前一次的运算结果中全部取到了，直接跳过这一种情况就可以了。

其实头尾指针的这个想法当时做的时候是有的，只是固定的第三个指针当时并没有把他放到第一位作为固定，这样就没有办法减低问题的维数，总之这个思路很有用，一定要记住。

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ans;
        sort(nums.begin(), nums.end());
        int length = nums.size();
        for (int i = 0; i < length - 2; ++i) {
            if (nums[i] > 0) {
                break;
            }
            if (i > 0 && nums[i] == nums[i-1]){
                continue;
            }
            int left = i + 1, right = nums.size() - 1;
            while (left < right){
                int sum = nums[i] + nums[left] + nums[right];
                if (sum == 0) {
                    vector<int> temp_ans = {nums[i], nums[left], nums[right]};
                    ans.push_back(temp_ans);
                    temp_ans.clear();
                    left ++;
                    while (left < right && nums[left] == nums[left - 1]) {
                        left ++;
                    }
                    right --;
                    while (left < right && nums[right] == nums[right + 1]) {
                        right --;
                    }
                } else if (sum < 0) {
                    left ++;
                    while (left < right && nums[left] == nums[left - 1]) {
                        left ++;
                    }
                } else if (sum > 0) {
                    right --;
                    while (left < right && nums[right] == nums[right + 1]) {
                        right --;
                    }
                }
            }
        }
        return ans;
    }
};
```

![image-20220521221910962](../images/LeetCode的菜鸡实录.assets/image-20220521221910962.png)

## 20、有效的括号

实际上就是一个栈的简单使用操作。本来用自己的方式写完之后觉得复杂度好像有点高？后来仔细看了比较快的例程，发现其实就是用map加速了一下，逻辑应该是一样的。

当栈为空时，如果是左括号直接入栈，如果为右则直接返回失败（也可以不管左右都入栈，因为在后面第二段匹配时一定是三组一一对应，可能直接入栈还会运算更少）。当栈不为空时进行匹配，总共三组可能，如果成功则出栈括号的左半部分（右半部分括号在能够成功匹配的情况下是不可能进入栈中的）。当栈不为空，然后面临左半部分括号时则继续入栈。最后在遍历完字符串之后返回之前需要判断一下栈是否为空，只要栈中还有内容，那么就是匹配失败。

**复杂度分析**

- 时间复杂度：O(n)，因为我们一次只遍历给定的字符串中的一个字符并在栈上进行 O(1) 的推入和弹出操作。
- 空间复杂度：O(n)，当我们将所有的开括号都推到栈上时以及在最糟糕的情况下，我们最终要把所有括号推到栈上。例如 `((((((((((`。

**延伸：栈的使用**

## 21、合并两个有序链表

直接记评论中第一条回复的例子，代码 如下：

```
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        // 类似归并排序中的合并过程
        ListNode dummyHead = new ListNode(0);
        ListNode cur = dummyHead;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                cur.next = l1;
                cur = cur.next;
                l1 = l1.next;
            } else {
                cur.next = l2;
                cur = cur.next;
                l2 = l2.next;
            }
        }
        // 任一为空，直接连接另一条链表
        if (l1 == null) {
            cur.next = l2;
        } else {
            cur.next = l1;
        }
        return dummyHead.next;
    }
```

这里实际上是为了规避第一个头结点如何选择的问题，采用了浪费一个结点的方式来处理。其实我自己代码里面也是用了$O(1)$的空间来存是哪个头，很明显，这种使用$O(1)$的记录方式更加简便易于理解。在返回的时候直接返回无意义的头的下一个即可返回需要的有效的序列。在两个指针都不为空时，对两个结点进行比较，确定将哪个结点挂到答案结点的后面。只要有一个为空了，如果另一个不为空，那么就只需要把另一个结点之后的所有的结点全部挂到当前答案结点最后一个结点的后面就可以得到结果了。

这里最巧妙的地方实际上就是取头，对一些要判断的情况如果有这种思路可以通过空间换区复杂判断的代码逻辑和时间。

**延伸：dummyHead头结点**

## 26、删除排序数组中的重复项

主要就是快慢指针的概念，不过这道题里面为啥快我倒是没搞懂，以及用的空间多少我也有点晕…反正肯定是O(1)级别上的，可能稍微多点少点，然后复杂度一定是O(n)的。因此这道题目就主要记住思想就行了。

```
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if(nums.empty())
            return 0;
        int i = 0;
        for(int j = 1; j < nums.size(); j++){
            if(nums[j] != nums[i]){
                i++;
                nums[i] = nums[j];
            }
        }
        return i + 1;
    }
};
```

以上是将题解的代码总java翻译成c++所得的。只使用下标来访问，一个在前面走，遍历完整个数组，一个在后面处理要更新的位置。因为是下标所以返回的数值时要+1。**这里是有序数组**

，如果不是有序数组的话应该要换用别的方式。

自己在写题的时候用的是迭代器，好像速度会更快一点？但是占用了更多的内存…搞不懂是什么机制，以及我后来一次用两个迭代器去解题反而还比之前只用一个迭代器的时候速度要慢，exm???反正有点迷，记住思想完事了。

**延伸：双指针法**

**前一段时间一直因为各种各样原因偷懒鸽了，现在发现药可以不吃，题不能不刷，每天就算是简单题也要水一道！我绝对不会说我是为了小绿点的**

## 27、移除元素

还是一个双指针的题，这次的判断是是否等于某一个值，所以只需要将里面的判断作出改变即可。

```
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        if(nums.empty())
            return 0;
        int s = 0;
        for(int i = 0; i < nums.size(); i++){
            if(nums[i] != val)
                nums[s++] = nums[i];
        }
        return s;
    }
};
```

**延伸：迭代器与下标访问**

这两者应该是不存在量级上的快慢的，除了set必须用迭代器之外，其他情况都是下标和迭代器均可的形式。所以在非set的情况下，都使用下标就完事了，不搞幺蛾子。

**二刷：**

这次写的时候实现的思路和上一次的不太一样，直接变成从尾部插了，这个主要是没限制顺序，如果要保留原顺序的话还得是上面的那种做法。

不保证顺序：

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        count = len(nums)
        index = 0
        while index < count:
            if nums[index] == val:
                nums[count-1], nums[index] = nums[index], nums[count-1]
                count -= 1
            else:
                index += 1
        return count
```

[保证顺序（不改变相对位置）](https://github.com/youngyangyang04/leetcode-master/blob/master/problems/0027.移除元素.md)：

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        index = -1
        for i in range(len(nums)):
            if nums[i] != val:
                index += 1
                nums[index] = nums[i]
        return index + 1
```

**==核心思想-双指针法（快慢指针法）==**：通过一个快指针和慢指针在一个for循环下完成两个for循环的工作

定义快慢指针

- 快指针：寻找新数组的元素 ，新数组就是不含有目标元素的数组
- 慢指针：指向更新 新数组下标的位置

## [65、有效数字](https://leetcode-cn.com/problems/valid-number/)

其实用暴力的办法写到最后是想到了要用图灵机去做，但是不会，就很真实。

这里记两种方法，一种是图灵机的状态转移方法，另一种是字符串从前向后扫描进行判断。图灵机的方法比较稳定，熟悉套路之后应该都能写这类题，扫描的方法需要根据条件的限制增加或者改变判断条件，但是由于是从前向后扫描，所以一旦发现不符合规则可以立刻中断，相较于图灵机，可能可以跳出更早？（反正从运行速度上来看确实是更快的）。

**扫描方法：**

```java
class Solution {
        private String s;
    private boolean hasDian = false;
    public boolean isNumber(String s) {
        this.s = s;

        for(int i=0;i<s.length();++i){
            char c = s.charAt(i);
            if(c == '.'){
                if(!dian(i)) return false;
            }else if(c == '+' || c == '-'){
                if(i!=0) return false;
            }else if(c == 'e' || c == 'E'){
                return eOrE(i);
            }else if(!isNum(i)){
                return false;
            }
        }

        return true;
    }

    //判断这个点是否合法
    private boolean dian(int index){
        if(hasDian) return false;

        boolean result = false;

        if(index-1>=0 && isNum(index-1)){
            result = true;
        }

        if(index+1<s.length() && isNum(index+1)){
            result = true;
        }

        hasDian = true;
        return result;
    }

    //判断e E是否合法
    private boolean eOrE(int index){
        if(index==0||index==s.length()-1 || s.charAt(index-1)=='+' || s.charAt(index-1)=='-') return false;

        if(s.charAt(index+1) == '+' || s.charAt(index+1) == '-'){
            index++;
        }

        if(++index == s.length()) return false;

        for(;index<s.length();++index){
            if(!isNum(index)) return false;
        }
        return true;
    }

    //判断是否是数字
    private boolean isNum(int index){
        int result = s.charAt(index) - '0';

        if(result>=0 && result<=9){
            return true;
        }else {
            return false;
        }
    }
}
```

核心的思想是根据三类符号判断，数字的情况就直接判断是否在0~9中间就行了；点的判断是判断前方和后方是否至少有一个方向存在数字；指数符号在首尾都违法，前面是正负号也是违法的，并且后面一定要跟东西，跟的还得是整数。其实对于所有的字符类别来说，这里没有把正负符号单独拉出来判断，而是否使用遇到跳过判断后面情况即可。大致的思想就是这么做的，不过感觉这种方法容易出错，可能还是用图灵机稳定点？

==**图灵机方法：**==

状态转移图

![fig1](https://assets.leetcode-cn.com/solution-static/65/1.png)

讲道理，这图出来了之后无法就是会不会用Map和书写手法的问题。这个看图理解吧，最难的是怎么理解成图中的每个状态转移，四种符号的转移情况是很好得出的，主要就是图中的状态和接受状态的确定。

代码如下：

```java
class Solution {
    public boolean isNumber(String s) {
        Map<State, Map<CharType, State>> transfer = new HashMap<State, Map<CharType, State>>();
        Map<CharType, State> initialMap = new HashMap<CharType, State>() {{
            put(CharType.CHAR_NUMBER, State.STATE_INTEGER);
            put(CharType.CHAR_POINT, State.STATE_POINT_WITHOUT_INT);
            put(CharType.CHAR_SIGN, State.STATE_INT_SIGN);
        }};
        transfer.put(State.STATE_INITIAL, initialMap);
        Map<CharType, State> intSignMap = new HashMap<CharType, State>() {{
            put(CharType.CHAR_NUMBER, State.STATE_INTEGER);
            put(CharType.CHAR_POINT, State.STATE_POINT_WITHOUT_INT);
        }};
        transfer.put(State.STATE_INT_SIGN, intSignMap);
        Map<CharType, State> integerMap = new HashMap<CharType, State>() {{
            put(CharType.CHAR_NUMBER, State.STATE_INTEGER);
            put(CharType.CHAR_EXP, State.STATE_EXP);
            put(CharType.CHAR_POINT, State.STATE_POINT);
        }};
        transfer.put(State.STATE_INTEGER, integerMap);
        Map<CharType, State> pointMap = new HashMap<CharType, State>() {{
            put(CharType.CHAR_NUMBER, State.STATE_FRACTION);
            put(CharType.CHAR_EXP, State.STATE_EXP);
        }};
        transfer.put(State.STATE_POINT, pointMap);
        Map<CharType, State> pointWithoutIntMap = new HashMap<CharType, State>() {{
            put(CharType.CHAR_NUMBER, State.STATE_FRACTION);
        }};
        transfer.put(State.STATE_POINT_WITHOUT_INT, pointWithoutIntMap);
        Map<CharType, State> fractionMap = new HashMap<CharType, State>() {{
            put(CharType.CHAR_NUMBER, State.STATE_FRACTION);
            put(CharType.CHAR_EXP, State.STATE_EXP);
        }};
        transfer.put(State.STATE_FRACTION, fractionMap);
        Map<CharType, State> expMap = new HashMap<CharType, State>() {{
            put(CharType.CHAR_NUMBER, State.STATE_EXP_NUMBER);
            put(CharType.CHAR_SIGN, State.STATE_EXP_SIGN);
        }};
        transfer.put(State.STATE_EXP, expMap);
        Map<CharType, State> expSignMap = new HashMap<CharType, State>() {{
            put(CharType.CHAR_NUMBER, State.STATE_EXP_NUMBER);
        }};
        transfer.put(State.STATE_EXP_SIGN, expSignMap);
        Map<CharType, State> expNumberMap = new HashMap<CharType, State>() {{
            put(CharType.CHAR_NUMBER, State.STATE_EXP_NUMBER);
        }};
        transfer.put(State.STATE_EXP_NUMBER, expNumberMap);

        int length = s.length();
        State state = State.STATE_INITIAL;

        for (int i = 0; i < length; i++) {
            CharType type = toCharType(s.charAt(i));
            if (!transfer.get(state).containsKey(type)) {
                return false;
            } else {
                state = transfer.get(state).get(type);
            }
        }
        return state == State.STATE_INTEGER || state == State.STATE_POINT || state == State.STATE_FRACTION || state == State.STATE_EXP_NUMBER || state == State.STATE_END;
    }

    public CharType toCharType(char ch) {
        if (ch >= '0' && ch <= '9') {
            return CharType.CHAR_NUMBER;
        } else if (ch == 'e' || ch == 'E') {
            return CharType.CHAR_EXP;
        } else if (ch == '.') {
            return CharType.CHAR_POINT;
        } else if (ch == '+' || ch == '-') {
            return CharType.CHAR_SIGN;
        } else {
            return CharType.CHAR_ILLEGAL;
        }
    }

    enum State {
        STATE_INITIAL,
        STATE_INT_SIGN,
        STATE_INTEGER,
        STATE_POINT,
        STATE_POINT_WITHOUT_INT,
        STATE_FRACTION,
        STATE_EXP,
        STATE_EXP_SIGN,
        STATE_EXP_NUMBER,
        STATE_END
    }

    enum CharType {
        CHAR_NUMBER,
        CHAR_EXP,
        CHAR_POINT,
        CHAR_SIGN,
        CHAR_ILLEGAL
    }
}
```

这里用到了枚举类enum，还有Map。大概解释一下最顶层transfer这个Map的含义吧。

Map<State, Map<CharType, State>>：前一个State表示的是当前的状态，即有向箭头的起点状态（圆圈）；内部Map的CharType对应的是箭头的字符类型，也就是转移条件；内部Map的State表示的是有向箭头的终点状态（圆圈）。

在进行状态转移判断的时候，先把当前状态初始化为开始状态，随后先判断目前状态是否有当前字符的转移条件（transfer.get(state).containsKey(type)），如果不存在则肯定不符合要求，如果符合要求则继续转移。在转移终止之后，判断到达的状态是否为最终的接收状态。

## [70、爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

核心：找（最近）重复子问题；泛化的能力

见过的题，所以知道就是个费波那契数列。

思想上来说，要上到最新一级的楼梯要么前一步走两阶，要么走一阶，所以就是走到下两阶的可能加上走到下一阶的可能数之和。

```c++
class Solution {
public:
    int climbStairs(int n) {
        int a = 0, b = 0, c = 1;
        for (int i = 1; i <= n; i++) {
            a = b;
            b = c;
            c = a + b;
        }
        return c;
    }
};
```

```python
class Solution(object):
	def climbStairs(self, n):
		if (n <= 2):
			return n
		for i in range(3, n+1):
			f3 = f1 + f2
			f1 = f2
			f2 = f3
		return f3
```

这里面唯一值得记忆的就是费波那契数列的起始方式，a,b,c分别为0,0,1然后从i=1开始。

另外费波那契数列还可以通过矩阵快速幂来解：

使用矩阵快速幂建立在可以构建合适递推关系的前提下。

**递推关系的建立：有点类似于凑同项公式那种，然后目前理解的是等号右边的项会作为递推关系的矩阵乘法右乘部分（就是这些项都竖过来排列成一个列向量，然后用矩阵做成描述递推关系）**

具体矩阵快速幂的计算和快速幂差不多，也是拆分 2^n ->2^(n/2) 这种，最终实现$O(logn)$的时间复杂度完成计算。矩阵快速幂与快速幂的区别就在于计算的时候要自己定义一下矩阵的乘法计算函数。（还有就是c++里面二维数组传参）

代码：

```c++
public class Solution {
    public int climbStairs(int n) {
        int[][] q = {{1, 1}, {1, 0}};
        int[][] res = pow(q, n);  // 相比于费波那契数列，这个爬楼梯大一位，所以是n而不是n-1
        return res[0][0];
    }

    public int[][] pow(int[][] a, int n) {
        int[][] ret = {{1, 0}, {0, 1}};
        while (n > 0) {
            if ((n & 1) == 1) {
                ret = multiply(ret, a);
            }
            n >>= 1;
            a = multiply(a, a);
        }
        return ret;
    }

    public int[][] multiply(int[][] a, int[][] b) {
        int[][] c = new int[2][2];
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                c[i][j] = a[i][0] * b[0][j] + a[i][1] * b[1][j];
            }
        }
        return c;
    }
}
```

总结

这里形成的数列正好是斐波那契数列，答案要求的 f(n) 即是斐波那契数列的第 n 项（下标从 0 开始）。我们来总结一下斐波那契数列第 n 项的求解方法：

- n 比较小的时候，可以直接使用过递归法求解，不做任何记忆化操作，时间复杂度是 $O(2^n)$，存在很多冗余计算。
- 一般情况下，我们使用「记忆化搜索」或者「迭代」的方法，实现这个转移方程，时间复杂度和空间复杂度都可以做到 $O(n)$。
- 优化空间复杂度，我们可以不用保存 f(x−2) 之前的项，我们只用三个变量来维护 f(x)、f(x−1) 和 f(x−2)，你可以理解成是把「滚动数组思想」应用在了动态规划中，也可以理解成是一种递推，这样把空间复杂度优化到了 $O(1)$。
- 随着 n 的不断增大 $O(n)$ 可能已经不能满足我们的需要了，我们可以用「矩阵快速幂」的方法把算法加速到 $O(logn)$。
- 我们也可以把 n 代入斐波那契数列的通项公式计算结果，但是如果我们用浮点数计算来实现，可能会产生精度误差。（通项公式到特征方程这块可能得回头补下数学了，线性代数的内容应该是，[参考网址](https://zhidao.baidu.com/question/565148974711671324.html)）

非快速幂的方法实现没啥问题，唯一要注意下的就是三个数值的交换了。原本的a、b、c在右移进行更新的时候，原本的a中的数值是没有作用的，所以不需要依赖额外的变量进行存储，直接先把原本的b赋给a，然后再把原本的c赋给b，最后用已经赋值之后的a和b相加计算出c就可以了。如果要从头开始直接用一个循环不对初始情况进行特殊判断得到结果的话，最初始的a、b、c的值分别是0、0、1，只要记住这三个初始值就行了。

```c++
class Solution {
public:
    int climbStairs(int n) {
        if (n == 0) {
            return 0;
        } else if (n == 1) {
            return 1;
        } else if (n == 2) {
            return 2;
        }
        int a = 1, b = 2, c = 3;
        for (int i = 3; i < n; ++i) {
            a = b;
            b = c;
            c = a + b;
        }
        return c;
    }
};
```

```c++
class Solution {
public:
    int climbStairs(int n) {
        int a = 0, b = 0, c = 1;
        for (int i = 0; i < n; ++i) {
            a = b;
            b = c;
            c = a + b;
        }
        return c;
    }
};
```

矩阵快速幂：

写得无比痛苦，从数据结构到实现细节到对题解的理解都有问题。

首先对于二维数组，java的话可以直接int[][]这种，但是对于c++来说，二维数组的传参比较麻烦，所以借助于vector实现传值是更理想的实现方式。这里java的话用int型似乎就可以了，但是在c++里面int貌似会越界，所以反正就用long long保证下范围不会超就行。

快速幂的思想，本质上就是把指数拆分成二进制然后进行计算。例如$7=(111)_2$，如果直接去乘那就要花7次，但是如果使用快速幂那么就只需要$2^2*2^1*2^0$，时间复杂度可以从$O(n)$进一步缩短到$O(logn)$

**快速幂的方式就是每次检查最低位，如果二进制位上是1，那么就乘当前的指数值，如果二进制位上是0那就跳过不用管。然后指数值伴随着n在不断右移的过程中不停的通过自己乘自己进行更新就可以了。**

其它的就是参照正常的矩阵运算规则，定义一个矩阵运算的函数就可以了。

然后就是对题解的理解：

题解里面对于最终值的计算是直接把那个关系矩阵计算了n次幂之后，取左上角的值作为最后的结果。但是从递推公式上进行理解我并没有能看到这个关系，关系矩阵的n-2次幂和f(2)与f(1)组成的列向量计算得到的结果能确定f(n)这个是没问题的，但是需要注意，如果是使用n-2次幂，则需要对n<2的结果进行特判。

题解里面的

![image-20220522173034852](../images/LeetCode的菜鸡实录.assets/image-20220522173034852.png)

最终计算完之后是取了n次幂矩阵的左上角元素的值作为结果，从结果的数值上来说这个是没错的，但是中间的形式是如何转换的并没有提。我的理解是，对于f(0)这种不明确具体含义的内容最理想的情况应该是不用作初始的值用于计算，与其使用一个不确定是否符合规则的初始化值，不如从更为确定的初始化值开始计算同时添加对于其它情况的特判。[对于这个问题的思考可以参考这个题解，里面的思路是和我一样的，即仅可保证下面的这个式子是确定的](https://blog.csdn.net/weixin_44458659/article/details/110086455)

![image-20220522173813218](../images/LeetCode的菜鸡实录.assets/image-20220522173813218.png)

题解代码：

```c++
class Solution {
public:
    vector<vector<long long>> multiply(vector<vector<long long>> &a, vector<vector<long long>> &b) {
        vector<vector<long long>> c(2, vector<long long>(2));
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                c[i][j] = a[i][0] * b[0][j] + a[i][1] * b[1][j];
            }
        }
        return c;
    }

    vector<vector<long long>> matrixPow(vector<vector<long long>> a, int n) {
        vector<vector<long long>> ret = {{1, 0}, {0, 1}};
        while (n > 0) {
            if ((n & 1) == 1) {
                ret = multiply(ret, a);
            }
            n >>= 1;
            a = multiply(a, a);
        }
        return ret;
    }

    int climbStairs(int n) {
        vector<vector<long long>> ret = {{1, 1}, {1, 0}};
        vector<vector<long long>> res = matrixPow(ret, n);
        return res[0][0];
    }
};
```

自己写得：

```c++
class Solution {
public:
    vector<vector<long long>> matrixmul(vector<vector<long long>> &matrix1, vector<vector<long long>> &matrix2) {
        vector<vector<long long>> result = {{0,0},{0,0}};
        // vector<vector<long long>> result(2, vector<long long>(2));
        result[0][0] = matrix1[0][0] * matrix2[0][0] + matrix1[0][1] * matrix2[1][0];
        result[0][1] = matrix1[0][0] * matrix2[0][1] + matrix1[0][1] * matrix2[1][1];
        result[1][0] = matrix1[1][0] * matrix2[0][0] + matrix1[1][1] * matrix2[1][0];
        result[1][1] = matrix1[1][0] * matrix2[0][1] + matrix1[1][1] * matrix2[1][1];
        // for (int i = 0; i < 2; ++i) {
        //     for (int j = 0; j < 2; ++j) {
        //         result[i][j] = matrix1[i][0] * matrix2[0][j] + matrix1[i][1] * matrix2[1][j];
        //     }
        // }
        return result;
    }

    vector<vector<long long>> matrixpow(vector<vector<long long>> matrix, int pow) {
        vector<vector<long long>> result = {{1, 0}, {0, 1}};
        while (pow) {
            if (pow % 2 != 0) {
                result = matrixmul(result, matrix);
            }
            matrix = matrixmul(matrix, matrix);
            pow >>= 1;
        }
        return result;
    }
    
    int climbStairs(int n) {
        vector<vector<long long>> matrix = {{1,1},{1,0}};
        if (n == 0) {
            return 0;
        } else if (n == 1) {
            return 1;
        } else {
            matrix = matrixpow(matrix, n - 2);
            return matrix[0][0] * 2 + matrix[0][1];
        }
        // vector<vector<long long>> res = matrixpow(matrix, n);
        // return res[0][0];
    }
};
```

另外这道题目其实也是动态规划范畴里面的一道题，可以通过动态规划的思路来理解，具体的内容等后面复习到动态规划之后再补充。

## [82、删除排序链表中的重复元素 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/)

难受啊

因为原先见过删除重复元素只保留一个，所以中间想到的思路是三指针扫一遍之后，再对着dict重扫一遍，然后也没写出来。

[题解](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/solutions/678681/fu-xue-ming-zhu-di-gui-die-dai-yi-pian-t-wy0h)：三种思路

1. 递归
2. 迭代——一次遍历（使用有序这一性质）
3. 迭代——利用计数，两次遍历（不使用有序这一性质），这个其实就跟自己想到的思路有一丢丢像

```python
class Solution(object):
    def deleteDuplicates(self, head):
        if not head or not head.next:
            return head
        dummy = ListNode(0)
        dummy.next = head
        pre = dummy
        cur = head
        while cur:
            # 跳过当前的重复节点，使得cur指向当前重复元素的最后一个位置
            while cur.next and cur.val == cur.next.val:
                cur = cur.next
            if pre.next == cur:
                 # pre和cur之间没有重复节点，pre后移
                pre = pre.next
            else:
                # pre->next指向cur的下一个位置（相当于跳过了当前的重复元素）
                # 但是pre不移动，仍然指向已经遍历的链表结尾
                pre.next = cur.next
            cur = cur.next
        return dummy.next

链接：https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/solutions/678681/fu-xue-ming-zhu-di-gui-die-dai-yi-pian-t-wy0h/
```

### 哑节点

代码中用到了一个常用的技巧：dummy 节点，也叫做 **==哑节点==**。它在链表的迭代写法中非常常见，因为对于本题而言，我们可能会删除头结点 head，为了维护一个不变的头节点，所以我们添加了 dummy，让dummy.next = head，这样即使 head 被删了，那么会操作 dummy.next 指向新的链表头部，所以最终返回的也是 dummy.next

## [125、验证回文串](https://leetcode.cn/problems/valid-palindrome/)

思路很简单的一道题，主要是预处理和判断条件。

首先是两个函数：

```c++
isalnum()  // 判断一个字符是否为字母(不论大小写)或者数字，属于C 标准库 - <ctype.h>
tolower()  // 将一个字符从大写转成小写，如果不是字母则不处理，同样属于ctype.h
// 如果记得库并且知道这个函数就可以不用去管ASCII码具体数值了
```

大致可以分为三个思路：

- 栈：通过栈对符合条件的字符全部入栈之后出栈，再逐个比对即可（浪费空间，pass）
- reverse：清理不符合条件的字符后reverse判断是否等于自身（也会浪费空间，pass）
- 双指针：通过前后指针移动，就地判断前后是否对应

前两种方法：

- 时间复杂度：O(|s|)，其中 |s| 是字符串 s 的长度。
- 空间复杂度：O(|s|)

双指针复杂度：

- 时间复杂度：O(|s|)，其中 |s| 是字符串 s 的长度。
- 空间复杂度：O(1)

其实都是些实现细节，直接贴代码吧：（思路非常简单，但是实现的时候有些细节，比如这个上面while一次性移完所有非法字符肯定比写个if之后在那continue要强，关键就是怎么保证while不飞出去）

```c++
class Solution {
public:
    bool isPalindrome(string s) {
        int n = s.size();
        int left = 0, right = n - 1;
        while (left < right) {
            while (left < right && !isalnum(s[left])) {  // 这里的left<right的作用是为了防止直接指针飞到外面去了，比如全是标点
                ++left;
            }
            while (left < right && !isalnum(s[right])) {
                --right;
            }
            if (left < right) {
                if (tolower(s[left]) != tolower(s[right])) {  // 用的时候再处理，不新建数组用来存处理完的可以不多占空间
                    return false;
                }
                ++left;
                --right;
            }
        }
        return true;
    }
};
```

二刷：在知道 isalnum() 和 tolower() 这两个函数的前提下，这道题目还是非常简单的。这次的代码基本上和题解代码一样了，无非就是一些判断选项出和顺序的写法上有一点点不同，这个感觉看个人习惯了，或许不用else能更快？

```c++
class Solution {
public:
    bool isPalindrome(string s) {
        int length = s.size(), head = 0, back = length - 1;
        while (head < back) {
            while (!isalnum(s[head]) && head < back) {
                ++ head;
            }
            while (!isalnum(s[back]) && head < back) {
                -- back;
            }
            if (tolower(s[head]) == tolower(s[back])){
                ++ head;
                -- back;
            } else {
                return false;
            }
        }
        return true;
    }
};
```

c++的话，总的来说还是了解知道两个基本函数和他们的作用就行了。如果要考虑别的语言里面没有相关的实现，最好在之后面试前再记下ASCII码的数字、大写字母以及小写字母的起始和终止值就行了。（另外补充一点，ASCII码有128个，容量占低7位。ASCII码在计算机中存储是以一个字节，也就是8位进行存储的，其中最高位是低7位的奇偶校验位。）

## [138、复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

用暴力写的虽然过了但是肯定是不行的，然后瞄了下题解感觉都描述得贼麻烦...一看评论了一个大佬的代码豁然开朗（虽然空间还是用的多的），代码如下：

```java
class Solution {
    public Node copyRandomList(Node head) {
        if(head == null){
            return null;
        }
        Node cur = head;
        HashMap<Node,Node> map = new HashMap<>();
        while(cur!=null){
            map.put(cur,new Node(cur.val));
            cur = cur.next;
        }
        cur=head;
        while(cur!=null){
            map.get(cur).next=map.get(cur.next);
            map.get(cur).random=map.get(cur.random);
            cur=cur.next;
        }
        return map.get(head);
    }
}
```

这个思路就是说，因为有随机指针可能会知道后面的结点上，我一个个按顺序去整结点的next和random肯定一堆麻烦，所以为啥不干脆先创建完之后再给指针赋指向呢。（讲道理，我的暴力做法也是这个思想，但是问题在于我写的很蠢，实际上靠一个HashMap解决的问题我用了两个ArrayList加上下标指向去实现...）在所有结点对应创建的同时，构建一个原本结点与新建结点的哈希表，并在第二次循环的时候通过去原先结点作为键就能够给新建结点赋予两个指针。

那上面这种解法思路其实蛮简单的，说到底还是对于java的数据结构不太熟，但是这道题还没结束，新开的这个哈希表是必要的吗？其实还可以接着省空间。

![image-20210722160637564](../images/LeetCode的菜鸡实录.assets/image-20210722160637564.png)

代码如下：

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) {
            return null;
        }
        for (Node node = head; node != null; node = node.next.next) {
            Node nodeNew = new Node(node.val);
            nodeNew.next = node.next;
            node.next = nodeNew;
        }  // 新建A',B',C'并插入
        for (Node node = head; node != null; node = node.next.next) {
            Node nodeNew = node.next;
            nodeNew.random = (node.random != null) ? node.random.next : null;
        }
        Node headNew = head.next;
        for (Node node = head; node != null; node = node.next) {
            Node nodeNew = node.next;
            node.next = node.next.next;
            nodeNew.next = (nodeNew.next != null) ? nodeNew.next.next : null;
        }  // 最终的结点拆分
        return headNew;
    }
}
```

## [168、Excel表列名称](https://leetcode-cn.com/problems/excel-sheet-column-title/)

简单题，但是依然被制裁了一发。

思路上很简单，1\~26相当于是一个进制嘛，然后数字变成字母就行，又有StringBuffer直接reverse，所以实现上是没有任何困难的。那么为啥会被制裁一发呢，主要就是这个进制是1\~26而不是从0开始的。所以一开始直接%26的话，像52这种情况就没辙了，因为就会变成倒数第一位上是0，倒数第二位上是2。其实一开始发现这个问题之后懵逼了一会儿，不过后来反应过来了，1\~26跟0\~25没区别，稍加改动就行了。既然你要的是1\~26我现在模出来只能是0\~25，那我就直接把模的结果弄到这个范围不就行了嘛。所以就再处理大于26的时候直接先-1再取模，这样保证了在生成字符的过程中一定是一个非负整数去与A相加，就不会弄出来个"@"了。当然这么处理在每步相减的时候要把这个补回来，因为实际上还是1~26的数值。在最终小于等于26的时候就不用担心变出0这个问题了，因为要经过上面步骤的一定是大于26的，减完余数（模）那部分，再去除一定大于等于1，所以最后的结果-1也不会出现到"@"的情况。

稍微算是转个弯，不过还是常规题。

```java
class Solution {
    public String convertToTitle(int columnNumber) {
        StringBuffer ans = new StringBuffer();
        while(columnNumber > 26){
            int temp = (columnNumber - 1) % 26;
            ans.append((char)(temp + 'A'));
            columnNumber -= (temp + 1);
            columnNumber /= 26;
        }
        ans.append((char)(columnNumber - 1 + 'A'));
        ans.reverse();
        return ans.toString();
    }
}
```

![image-20210629194912986](../images/LeetCode的菜鸡实录.assets/image-20210629194912986.png)

## [209、长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

知道是滑动窗口的问题后其实就明白需要把时间复杂度降低到$O(n)$。反正就是拿两个指针作为窗口的两端去扫，这样就保证是线性的时间复杂度了。

然后自己写的这个实现就属于能跑，但逻辑很乱：

```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        left = -1
        right = -1
        val = 0
        res = 10**5+1
        while right < len(nums) - 1:
            if left == right:
                right += 1
                val = nums[right]
            elif val < target:
                right += 1
                val += nums[right]
            else:
                res = min(res, right-left)
                left += 1
                val -= nums[left]
        while left < len(nums) - 1:
            if val < target:
                break
            else:
                res = min(res, right-left)
                left += 1
                val -= nums[left]
        return res if res <= 10**5 else 0
```

[题解](https://github.com/youngyangyang04/leetcode-master/blob/master/problems/0209.长度最小的子数组.md)：

```python
class Solution:
    def minSubArrayLen(self, s: int, nums: List[int]) -> int:
        l = len(nums)
        left = 0
        right = 0
        min_len = float('inf')
        cur_sum = 0 #当前的累加值
        
        while right < l:
            cur_sum += nums[right]
            
            while cur_sum >= s: # 当前累加值大于目标值
                min_len = min(min_len, right - left + 1)
                cur_sum -= nums[left]
                left += 1
            
            right += 1
        
        return min_len if min_len != float('inf') else 0
```

其实上面自己写的那种一开始还忘了最后面的一个while，就是右指针到末尾，但左指针还没到末尾，并且和依然满足大于target的处理。

理思路的时候还是需要当两重循环来思考，只是有了边界范围，并且保证了不会存在回退。

[leetcode题解](https://leetcode.cn/problems/minimum-size-subarray-sum/solutions/305704/chang-du-zui-xiao-de-zi-shu-zu-by-leetcode-solutio/)

## 239、滑动窗口最大值

**双端队列**

首先得知道双端队列这个东西或者要有类似的实现思路（deque）。

复杂度为O(nlogn)的方法是肯定可以做的，不过也不是最初想的直接排序，是用**优先队列**。

但是肯定不可能满足于O(nlogn)的复杂度，那么是否存在O(n)复杂度的方法呢？其实在初次考虑的时候相关类似于用两个指针标注然后单向移动去解决这个问题，不过这样肯定是不够的，大致思想方向没错但是空间上需要更多才能达到目标复杂度。事实上需要O(k)的复杂度来解决这一问题。

目前理解的关键思想是，一旦出现一个更大的数值在范围内，那么之前出现的最大值都不会在之后的移动中再次出现可以直接进行丢弃；但是在这个值之后出现的值可能会因为该值移出窗口而成为最大值，所以要保留可能成为最大值的数。

1. 如果新进来的值比之前的所有候选值都打，直接把之前的候选值全清楚即可，因为他们不再可能成为最大值；
2. 如果新进来的值比除最大值之外的值都大，也可以清空除最大值之外的候选值，因为如果窗口已经移除了，那最大值就不会能候选，新的最大值进来表示，之前保留的候选最大值没机会成为最大值了，因此也能进行更新；
3. 如果新进来的还没之前保留的候选最大值大，那肯定没有成为任何一个窗口中最大值的可能，所以直接pass就行；
4. 如果最大值移出，那就去除然后取剩下的里面的最大值即可

明白想法之后应该还算是简单，主要还是要知道能有**双端队列**这样一种东西，然后要了解这个东西的规则和思想。

[解析（非leetcode)](https://www.cnblogs.com/gzshan/p/10904273.html)

算法课（c++实现）

```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> queue1;
        vector<int> ans;
        if (nums.size() == 0 || k == 0){
            return ans;
        }
        for (int i = 0; i < k; i++){
            while(!queue1.empty() && nums[i] > nums[queue1.back()]){
                queue1.pop_back();
            }
            queue1.push_back(i);
        }
        ans.push_back(nums[queue1.front()]);
        for (int i = k; i < nums.size(); i++){
            while(!queue1.empty() && nums[i] > nums[queue1.back()]){
                queue1.pop_back();
            }
            queue1.push_back(i);
            while(!queue1.empty() && queue1.front() < i - k + 1){
                queue1.pop_front();
            }
            ans.push_back(nums[queue1.front()]);
        }
        return ans;
    }
};
```

java实现（二刷时补充）

## [278、第一个错误的版本](https://leetcode-cn.com/problems/first-bad-version/)

傻逼二分，没啥说的就是注意范围。

$min+max$可能越界；一般有两种处理方法，第一个是直接int变long或者再拉大一级，反正塞得下就行，第二个是**写成**$min+(max-min)/2$**形式**，这样可以保证数组不越界，写题的是否求平均啥的记得用这种。代码如下：

```java
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int min = 1, max = n, mid;
        while(min <= max) {
            mid = min + (max - min) / 2;
            if (isBadVersion(mid)) {
                max = mid - 1;
            } else {
                min = mid + 1;
            }
        }
        return max + 1;
    }
}
```

## [279、完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

只想到了dp，方案跟518一样，只不过一个用来表示方案数，一个用来表示最少的组合平方数的个数（自然是用Math.min）。唯一稍微值得提一嘴的就是初始只有dp[0] = 0，别的其实相当于是无穷，这里就赋了101（反正达不到）。

```java
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            dp[i] = 101;
        }
        for (int i = 1; Math.pow(i,2) <= n; i++) {
            for (int j = (int)Math.pow(i,2); j <= n; j++) {
                dp[j] = Math.min(dp[j], dp[j - (int)Math.pow(i,2)] + 1);
            }
        }
        return dp[n];
    }
}
```

但是，这tm交完看下速度当场裂开。

数学方法就放弃了，这里提一下贪心吧。

其实就是从一个完全平方数可以组成开始不断递增搜索，这样的话对于各个数只用搜索最少的次数就可以得到结果了，相比于BFS（题解评论里的那个没看懂），搜索次数肯定是更少的。

```python
ps = set([i * i for i in range(1, int(n**0.5)+1)])
def divisible(n, count):
    if count == 1: return n in ps
    for p in ps:
        if divisible(n-p, count-1):
            return True
    return False

for count in range(1, n+1):
    if divisible(n, count):
        return count
```

divisible函数表述数n能否用count个完全平方数进行表示。

## [283、移动零](https://leetcode.cn/problems/move-zeroes/)

##### ==数组的坐标变换！==

要用双指针以及需要通过swap操作来在原数组上尽可能少次数的交换，这两个是想到了，主要是具体怎么应用。对于双指针来说肯定一个是只想前端的（已经处理完的序列尾），另外一个肯定是指向后端（正常单次循环向后走的指针），然后通过把后面的非零元素交换到前面该保存的位置就可以了。（这里因为后面是全0已知，也可以说最后是把剩下的位置全填零，这样可以不需要在前面进行交换）

看完题解之后把一开始最原始想法的代码修改了，原本思想的写法应该是下面这种：

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int length = nums.size(), pt1 = 0, pt2 = 0;
        while (pt1 < length) {
            if (nums[pt1] != 0) {
                int temp = nums[pt1];
                nums[pt1] = nums[pt2];
                nums[pt2] = temp;
                pt2 ++;
            }
            pt1 ++;
        }
    }
};
```

这里其实while完全可以用一个正常的for循环去代替，然后就和题解给的差不多了。这里原本思想没弄清楚的关键在于，一定是先判断走在前面的指针（pt1）是否踩到0上面。因为pt2相当于是pt1走过的位置，已经判断了是否为0了，要想在一遍里面完成当然是判断走在前面的那个指针的状态。

题解代码：

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int n = nums.size(), left = 0, right = 0;
        while (right < n) {
            if (nums[right]) {
                swap(nums[left], nums[right]);
                left++;
            }
            right++;
        }
    }
};
```

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int length = nums.size();
        int j = 0;
        for (int i = 0; i < length; i++) {
            if (nums[i] != 0) {
                nums[j] = nums[i];
                j ++;
            }
        }
        for (;j < length; j++) {
            nums[j] = 0;
        }
    }
};
```

看了下几个速度更快的代码样例，思想基本上没有太大差别，最多只是细节上面有一些小的优化，正常来说能把上面的思路盘清楚也就ok了。

二刷：没啥大问题，知道指针前后走交换位置后，这道题还是很简单的，只要确定了快指针判断新元素，然后交换到同向移动的慢指针所在位置就行，剩下的就是按照条件右移指针就ok。

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int length = nums.size(), fast = 0, slow = 0;
        while (fast < length) {
            if (nums[fast] != 0) {
                nums[slow] = nums[fast];
                slow ++;
            }
            fast ++;
        }
        while (slow < length) {
            nums[slow] = 0;
            slow ++;
        }
    }
};
```

## [374、猜数字大小](https://leetcode-cn.com/problems/guess-number-higher-or-lower/)

折腾这种恶心人真的有意思吗？？？傻逼二分，注意数据范围，防爆int情况，并且由于调用的接口传入int型，不能开long；

其实本来是想偷懒直接用判断是否等于作为条件的，然后发现直接把自己给整进去了，因为第一次初始化的时候也要用$min+(max-min)/2$的形式才能保证不越界。当然了，如果用的是min和max的大小关系判断就没这个问题了（写法就跟278完全一样）

其实对于最通用形式的二分来说是不能直接用是否等于作为条件中止的，因为可能存在数组中不包含的情况，但是对于连续的数组就无所谓了，一定会存在，所以可以直接用是否等于作为while循环的终止判断条件。（朴素条件判断：**while(min <= max)**）需要当min大于max才能判断数组中是否存在。

代码如下：

```java
public class Solution extends GuessGame {
    public int guessNumber(int n) {
        int min = 1, max = n, mid = 1 + (n - 1) / 2, status = guess(mid);
        while(status != 0){
            if(status == 1){
                min = mid + 1;
            } else if(status == -1){
                max = mid - 1;
            }
            mid = min + (max - min) / 2;
            status = guess(mid);
        }
        return mid;
    }
}
```

## [401、二进制手表](https://leetcode-cn.com/problems/binary-watch/)

一开始想到的思路其实是通过将turnedOn拆分成小时和分钟两部分然后去枚举所有的可能，这样可以通过空间的开销去使得时间开销上到达O(1)。

实际上并不需要这么干，因为时间数值是有有效范围的，所以直接通过枚举时间数值去计算二进制数上面的位数也可以得到这个结果，但是这样似乎比上面想法的开销更大？代码实现起来非常容易

```java
class Solution {
    public List<String> readBinaryWatch(int turnedOn) {
        List<String> ans = new ArrayList<>();
        for (int i = 0; i < 12; i++) {
            for (int j = 0; j < 60; j++) {
                if(Integer.bitCount(i) + Integer.bitCount(j) == turnedOn) {
                    ans.add(i + ":" + (j < 10 ? "0" : "") + j);
                }
            }
        }
        return ans;
    }
}
```

## [410、分割数组的最大值](https://leetcode.cn/problems/split-array-largest-sum/)

这题看到的时候想到了是动态规划，但是转移方程还是没写出来。另外二分的想法是部分想到了的，就是拿总和的值去试嘛，但也没细想，就直接看题解了。

首先是第二种二分的思路，这个还是比较好理解的，就相当于是枚举嘛，枚举的目标就是数组的最大和，然后用枚举的值去校验就行了。这个校验的时候，因为要求是连续的，所以每超过一次就增加下计数，最后比较就能得到是否满足条件了。（这个么其实按理应该是要能写出来的）

```python
class Solution:
    def splitArray(self, nums: List[int], m: int) -> int:
        def check(x: int) -> bool:
            total, cnt = 0, 1
            for num in nums:
                if total + num > x:
                    cnt += 1
                    total = num
                else:
                    total += num
            return cnt <= m


        left = max(nums)
        right = sum(nums)
        while left < right:
            mid = (left + right) // 2
            if check(mid):
                right = mid
            else:
                left = mid + 1

        return left

链接：https://leetcode.cn/problems/split-array-largest-sum/solutions/345417/fen-ge-shu-zu-de-zui-da-zhi-by-leetcode-solution/
```

第二种的思路的话就是动态规划了。

没特别理解吧，主要是先得想到`令 f[i][j] 表示将数组的前 i 个数分割为 j 段所能得到的最大连续子数组和的最小值`

```python
class Solution:
    def splitArray(self, nums: List[int], m: int) -> int:
        n = len(nums)
        f = [[10**18] * (m + 1) for _ in range(n + 1)]
        sub = [0]
        for elem in nums:
            sub.append(sub[-1] + elem)
        
        f[0][0] = 0
        for i in range(1, n + 1):
            for j in range(1, min(i, m) + 1):
                for k in range(i):
                    f[i][j] = min(f[i][j], max(f[k][j - 1], sub[i] - sub[k]))
        
        return f[n][m]

链接：https://leetcode.cn/problems/split-array-largest-sum/solutions/345417/fen-ge-shu-zu-de-zui-da-zhi-by-leetcode-solution
```

## [447、回旋镖的数量](https://leetcode.cn/problems/number-of-boomerangs/)

其实顺序和排列组合那块已经想到了，但是怎么存，怎么枚举还是想得有点问题。自己想的时候变成是存三元组了，然后就需要涉及到匹配超过两个的情况，就很恶心。这里其实就应该把距离想象成以点为中心画一个圆，判断下其它点是不是在上面就行了（这个思路其实有想到）。当然不可能无限去枚举这个圆的大小，那就直接拿点挨个去枚举就行了，并且也不需要在判断距离的时候去考虑哪两个点和拐点匹配，所以核心就是距离拐点为一定距离的点的数量有几个，然后排列组合。

```python
class Solution:
    def numberOfBoomerangs(self, points: List[List[int]]) -> int:
        num = 0
        for index, point in enumerate(points):
            distance_dict = {}
            for index1, point1 in enumerate(points):
                if index1 == index:
                    continue
                distance = ((points[index][0]-points[index1][0])**2 + (points[index][1]-points[index1][1])**2)**0.5
                if distance in distance_dict:
                    distance_dict[distance] += 1
                else:
                    distance_dict[distance] = 1
            for key, value in distance_dict.items():
                num += value * (value - 1)
        return num
```

题解：

```python
class Solution:
    def numberOfBoomerangs(self, points: List[List[int]]) -> int:
        ans = 0
        for p in points:
            cnt = defaultdict(int)
            for q in points:  # 这里不用特地跳拐点自身，因为已经说了是互不相同的点
                dis = (p[0] - q[0]) * (p[0] - q[0]) + (p[1] - q[1]) * (p[1] - q[1])  # 距离也不用再开方
                cnt[dis] += 1
            for m in cnt.values():  # key也可以不用拿，只在乎value个数
                ans += m * (m - 1)
        return ans
```

**==在python中，dict就是哈希表==**

[**==Python collections模块之defaultdict()详解==**](https://blog.csdn.net/chl183/article/details/107446836)

## [474、一和零](https://leetcode-cn.com/problems/ones-and-zeroes/)

dp问题，题还是做得不够多，得补；思路对了，但是手法上有大问题。

空间上**，不需要用三维数组进行实现，只需要使用二维数组加内层循环倒序**可实现。（dp的基本节省空间办法，但是这个要注意是0-1背包还是完全背包这种区别，因为**不同的背包问题内部的循环可能是正序可能是倒序**）这里还有一个是最开始开的三维，然后三维的顺序还有讲究，最初实现的时候把列表的序号放在最里面，导致每次都得循环到里面进下一层...这样也浪费时间，另外就是java初始化（new int）之后默认为0，不需要再初始化一遍...

```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int[][] dp = new int[m + 1][n + 1];
        int length = strs.length;
        for (int i = 0; i < length; i++) {
            int[] zerosOnes = getZerosOnes(strs[i]);
            int zeros = zerosOnes[0], ones = zerosOnes[1];
            for (int j = m; j >= zeros; j--) {
                for (int k = n; k >= ones; k--) {
                    dp[j][k] = Math.max(dp[j][k], dp[j - zeros][k - ones] + 1);
                }
            }
        }
        return dp[m][n];
    }

    public int[] getZerosOnes(String str) {
        int[] zerosOnes = new int[2];
        int length = str.length();
        for (int i = 0; i < length; i++) {
            zerosOnes[str.charAt(i) - '0']++;
        }
        return zerosOnes;
    }
}
```

## [483、最小好进制](https://leetcode-cn.com/problems/smallest-good-base/)

数学我谢谢你大爷勒！！！大概写下思路吧

首先是全1的进制能联想到等比数列（确实想到了，但不知道接下去咋做），感觉按照题解的思路来走的话，应该是先要把搜索的范围缩小下来，因为如果是在10^18范围里面去找肯定不现实。不过n的范围给了，k的范围也给了，是不是能够把m范围定下来？m的范围一定是比较小的，可以有效减少枚举的数量（隐含的限制条件，m为整数）。

![image-20210618142750291](../images/LeetCode的菜鸡实录.assets/image-20210618142750291.png)

然后就有了上面这个结论，其实第一步等比数列的求和是想到了的，恒等变形也能做，但是放缩这一步的确想不到。（从结果反推去思考可能是要得到一个系数k用来吧m+1次里面的1去掉）。然后一通折腾取m的最大上届和k的最小下届就能得到一个对m的限制范围。这个范围拿到之后其实就可以确定整体的解决思路了，就是找一个限制范围然后枚举。

m的枚举范围知道了，但是k的取值或者说是范围还是没有确定下来，所以需要也给一个限制才能在良好的时间开销下进行搜索。下面这个是真的一点都没想到。

![image-20210618143355450](../images/LeetCode的菜鸡实录.assets/image-20210618143355450.png)

就反正是一通操作把k的取值给定下来了，这就保证了在已知n，m做枚举的情况下，可以快速得到k，然后进行校验。

**题解描述：**

![image-20210618143551645](../images/LeetCode的菜鸡实录.assets/image-20210618143551645.png)

**题解代码：**(范围开到long就够了)

```java
class Solution {
    public String smallestGoodBase(String n) {
        long nVal = Long.parseLong(n);
        int mMax = (int) Math.floor(Math.log(nVal) / Math.log(2));
        for (int m = mMax; m > 1; m--) {
            int k = (int) Math.pow(nVal, 1.0 / m);
            long mul = 1, sum = 1;
            for (int i = 0; i < m; i++) {
                mul *= k;
                sum += mul;
            }
            if (sum == nVal) {
                return Integer.toString(k);
            }
        }
        return Long.toString(nVal - 1);
    }
}
```

## [494、目标和](https://leetcode-cn.com/problems/target-sum/)

又是一道dp背包的题，不过这题稍微要拐个弯，一下子就没反应过来。

首先先要确定一个问题就是，正负和最终和的关系。因为$positive-negative=target$，$positive+negative=sum_{array}$，所以$sum_{array}-target=2*negative$。这个条件是非常重要的，如果没发现这个规律就做不了dp。其实这个性质对于正负二项的这种情况应该都是成立的。同时，这里依赖于这个负数和还可以做一个特殊情况的判断，即当要求的负数和为负数（多个正数的和打死也不可能为负数啊）或者不为整数（多个整数的和打死也不可能为小数）时，那必定没有可以实现的组合，直接返回0就行了。

在确定了负数的和之后，问题就好办了，相当于找对应价值的组合方法（必须填满）。进入dp模式。

$dp[i][j]$，i表示当前用到的nums数组元素个数（从前向后），j表示负数和的值，dp值表示用nums数组前n个元素组成$j$的可能方案数。

跟474一样，没必要开个二维数组，开个一维的就够了。**初始状态0个元素只有组成0才可能**，因此初始dp[0]=1。（这个很重要）

后面的就按照dp方程来呗，因为是方案数，所以是不用当前元素的方案数（上一层）和使用当前元素的方案数（上一层左侧）之和。（对应表示的含义就是取和不取当前这个元素的组成情况数量总和）因为要用到的左侧元素是上一层的，所以这里的内层循环还是要用倒序。（这里其实正序和倒序应该是对应0-1背包问题和完全背包问题的两类情况，之后找机会再补一下这个彻底理解一下，**目前就按照要用的是上一层的左侧还是这一层的左侧去区分内层是否需要使用到倒序循环**）

```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int negative = Arrays.stream(nums).sum() - target;  //还能这么写，涨姿势了
        if (negative % 2 != 0 || negative < 0) return 0;  //无法整除或者为负为特殊情况，必不可能存在可实现结果
        negative /= 2;
        int[] dp = new int[negative + 1];
        dp[0] = 1;  //重要初始条件
        for (int i = 0; i < nums.length; i++) {
            for (int j = negative; j >= nums[i]; j--) {
                dp[j] += dp[j-nums[i]];
            }
        }
        return dp[negative];
    }
}
```

## [518、零钱兑换 II](https://leetcode-cn.com/problems/coin-change-2/)

看题知dp，其实第一时间也没反应过来，不过不管用几次实际上都一样的。

***coins* 的每个元素可以选取多次，且不考虑选取元素的顺序**

$dp[i][j]$表示方案数量，i表示已经使用的硬币种类数，j表示组成的金额，最终实际上就是要得到$dp[coins.length][amount]$。

状态转移方程：使用i-1种硬币达到j价值的方案数加上使用i种硬币达到j-coins[i]的方案数。

因为要用到第i种硬币达到较小数值的方案数，所以内层使用正序循环。

**这题最重要的其实就是重复计算问题**，即2+1=3和1+2=3因为不需要考虑选取的顺序，所以实际上两者是同一种方案。

**==关于重复计算的问题：==**（按照给定的硬币数组的顺序确定组合的顺序，可以保证对于每种组合方案存在唯一的排序）

![image-20210610142206858](../images/LeetCode的菜鸡实录.assets/image-20210610142206858.png)

代码如下：

```java
class Solution {
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;
        for (int i = 0; i < coins.length; i++) {
            for (int j = coins[i]; j <= amount; j++) {
                dp[j] = dp[j - coins[i]] + dp[j];
            }
        }
        return dp[amount];
    }
}
```

## [704、二分查找](https://leetcode.cn/problems/binary-search/)

题就是基础的二分，没啥可说的。

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] > target:
                right = mid - 1
            elif nums[mid] < target:
                left = mid + 1
            else:
                return mid
        return -1
```

- 二分的查找范围：left <= right
- 查找结束条件：left > right

### ==二分法的使用前提：==

- 有序数组
- 数组中无重复元素（因为一旦有重复元素，使用二分查找法返回的元素下标可能不是唯一的）

### [==二分法的两种实现==](https://github.com/youngyangyang04/leetcode-master/blob/master/problems/0704.二分查找.md)：

1. $[left,right]$：定义 target 是在一个在左闭右闭的区间里

   - while (left <= right) 要使用 <= ，因为left == right是有意义的，所以使用 <=
   - if (nums[middle] > target) right 要赋值为 middle - 1，因为当前这个nums[middle]一定不是target，那么接下来要查找的左区间结束下标位置就是 middle - 1

   ```python
   class Solution:
       def search(self, nums: List[int], target: int) -> int:
           left, right = 0, len(nums) - 1  # 定义target在左闭右闭的区间里，[left, right]
   
           while left <= right:
               middle = left + (right - left) // 2
               
               if nums[middle] > target:
                   right = middle - 1  # target在左区间，所以[left, middle - 1]
               elif nums[middle] < target:
                   left = middle + 1  # target在右区间，所以[middle + 1, right]
               else:
                   return middle  # 数组中找到目标值，直接返回下标
           return -1  # 未找到目标值
   ```

2. $[left,right)$：定义 target 是在一个在左闭右开的区间里

   - while (left < right)，这里使用 < ,因为left == right在区间[left, right)是没有意义的
   - if (nums[middle] > target) right 更新为 middle，因为当前nums[middle]不等于target，去左区间继续寻找，而寻找区间是左闭右开区间，所以right更新为middle，即：下一个查询区间不会去比较nums[middle]

   ```python
   class Solution:
       def search(self, nums: List[int], target: int) -> int:
           left, right = 0, len(nums)  # 定义target在左闭右开的区间里，即：[left, right)
   
           while left < right:  # 因为left == right的时候，在[left, right)是无效的空间，所以使用 <
               middle = left + (right - left) // 2
   
               if nums[middle] > target:
                   right = middle  # target 在左区间，在[left, middle)中
               elif nums[middle] < target:
                   left = middle + 1  # target 在右区间，在[middle + 1, right)中
               else:
                   return middle  # 数组中找到目标值，直接返回下标
           return -1  # 未找到目标
   ```

二分的话其实不会直接考很直白的题，一般都是配合着别的算法一起。比如贪心+二分，所以主要是要确定对什么做二分。

应用案例可参考：2517 礼盒最大甜蜜度

## [752、打开转盘锁](https://leetcode-cn.com/problems/open-the-lock/)

题解中给了两种方法，一种是广度优先搜索，还有一种是启发式搜索（A*算法）。这里第二种算法就直接放弃了。

对于广度优先搜索来说，本题的转化过程并不是一个严格的树形结构，实际上下层的结点仍然可以通过一次转动到达上层的结点中，但是为了保证旋转的次数最少，在这里实际上是在每一步中都穷举了，所以对于出现过的结点，在下面其实就限制了变化状态的可能情况，不再进入。对于死亡数字可使用hash表进行存储来判断。

![image-20210626002052193](../images/LeetCode的菜鸡实录.assets/image-20210626002052193.png)

两个注意点：![image-20210626002115929](../images/LeetCode的菜鸡实录.assets/image-20210626002115929.png)

思路上是简单的，但是即便看了题解再去写还是有不会的地方...

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        if ("0000".equals(target)) {
            return 0;
        }

        Set<String> dead = new HashSet<String>();
        for (String deadend : deadends) {
            dead.add(deadend);
        }
        if (dead.contains("0000")) {
            return -1;
        }

        int step = 0;
        Queue<String> queue = new LinkedList<String>();
        queue.offer("0000");
        Set<String> seen = new HashSet<String>();
        seen.add("0000");

        while (!queue.isEmpty()) {
            ++step;  // 之所以这么写是为了方便计算步数
            int size = queue.size();
            for (int i = 0; i < size; ++i) {
                String status = queue.poll();
                for (String nextStatus : get(status)) {  // 实现下一个状态的获取
                    if (!seen.contains(nextStatus) && !dead.contains(nextStatus)) {
                        if (nextStatus.equals(target)) {
                            return step;
                        }
                        queue.offer(nextStatus);
                        seen.add(nextStatus);
                    }
                }
            }
        }

        return -1;
    }

    public char numPrev(char x) {
        return x == '0' ? '9' : (char) (x - 1);
    }

    public char numSucc(char x) {
        return x == '9' ? '0' : (char) (x + 1);
    }

    // 枚举 status 通过一次旋转得到的数字
    public List<String> get(String status) {  // 通过枚举每个数字位上的前后选择，全部保存为列表返回，在主函数中通过读取处理
        List<String> ret = new ArrayList<String>();
        char[] array = status.toCharArray();
        for (int i = 0; i < 4; ++i) {
            char num = array[i];
            array[i] = numPrev(num);
            ret.add(new String(array));
            array[i] = numSucc(num);
            ret.add(new String(array));
            array[i] = num;
        }
        return ret;
    }
}
```

## [773、滑动谜题](https://leetcode-cn.com/problems/sliding-puzzle/)

跟752，也就是昨天的那道一样，也是bfs或者A*搜索。

主要就是判断终止和写法上的问题，实在没忍住，n天没交了，抄了一题缓一缓，真的自闭。

放代码然后简单注解下吧....

```java
class Solution {
    int[][] neighbors = {{1, 3}, {0, 2, 4}, {1, 5}, {0, 4}, {1, 3, 5}, {2, 4}};

    public int slidingPuzzle(int[][] board) {
        Set<String> seen = new HashSet<>();
        Queue<String> queue = new LinkedList<>();
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 3; j++) {
                sb.append(board[i][j]);
            }
        }
        String init = sb.toString();
        if ("123450".equals(init)) {
            return 0;
        }
        int step = 0;
        seen.add(init);
        queue.add(init);
        while (!queue.isEmpty()) {
            step++;
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                String temp = queue.poll();
                for (String next : get(temp)) {
                    if (!seen.contains(next)) {
                        if ("123450".equals(next)) {
                            return step;
                        }
                        queue.add(next);
                        seen.add(next);
                    }
                }
            }
        }
        return -1;
    }

    public List<String> get(String status) {
        List<String> ans = new ArrayList<String>();
        char[] array = status.toCharArray();
        int x = status.indexOf('0');
        for (int y : neighbors[x]) {
            swap(array, x, y);
            ans.add(new String(array));
            swap(array, x, y);
        }
        return ans;
    }

    public void swap(char[] array, int x, int y) {
        char temp = array[x];
        array[x] = array[y];
        array[y] = temp;
    }
}
```

首先是最上面那个nieghbors，其实就对应表示了每个位置下标的可能相邻下标。python里面的列表可以装不同长度的，java的这个也行，这个要注意。==**java二维数组，内层数组存储的长度可以不同**==

然后就是终止的判断条件，实际上就是一直循环到所有状态都出现在seen中之后就算中止了，因为再也变化不到别的状态了。一开始我以为是把所有的状态都枚举出来然后判断，但是题目中说了是有无法到达状态的情况，那么就不能用所有方法枚举的形式来做，对于bfs来说，就是一直循环到队列为空，然后在循环内部根据是否出现判断是否插入队列之中。

初始状态的情况需要单独判断，因为一进循环就step++了，所以在外面单独判断。

for (int y : neighbors[x]) 这个是用y循环neighbors下标x取到的数组中的值，也就是在遍历所有可能交换的下标。

StringBuffer和char数组这些都属于实现技巧上的东西了。

按理来说昨天的题跟今天一毛一样应该是要写出来了，但是稍微变了一点点就不知道咋写，加上实现是真的不熟。不过这个类型的题目算是认识了，以后再碰到应该不至于没有思路就是了，至于写法可能写多了会熟悉的吧。

## [815、公交路线](https://leetcode-cn.com/problems/bus-routes/)

我会bfs了，不你没完全会。我吐了，还要优化建图，不过说实话看这个数据量和范围感觉也是用原始的表去做bfs一定会超时。这题在知道搜索之后主要的难度就是在于如何实现优化建图。

**建图思路：**

由于求解的目标是最少乘坐的公交车数量，对于同一辆公交车，乘客可以在其路线中的任意车站间无代价地移动，于是我们可以把公交路线当作点。如果两条公交路线有相同车站，则可以在这两条路线间换乘公交车，那么这两条公交路线之间可视作有一条长度为1的边。这样建出的图包含的点数即为公交路线的数量，记作n。

注意到原本的起点车站和终点车站可能同时位于多条公交路线上，因此在新图上可能有多个起点和终点。对于这种情况，我们**初始可以同时入队多个点，并在广度优先搜索结束后检查到各个终点的最短路径，取其最小值才是最少换乘次数**。

![image-20210628172200388](../images/LeetCode的菜鸡实录.assets/image-20210628172200388.png)

![image-20210628184432083](../images/LeetCode的菜鸡实录.assets/image-20210628184432083.png)

```java
class Solution {
    public int numBusesToDestination(int[][] routes, int source, int target) {
        if (source == target) {
            return 0;
        }

        int n = routes.length;
        boolean[][] edge = new boolean[n][n];
        Map<Integer, List<Integer>> rec = new HashMap<Integer, List<Integer>>();
        for (int i = 0; i < n; i++) {  // 遍历所有公交线路，记录每一个车站属于哪些公交线路
            for (int site : routes[i]) {
                List<Integer> list = rec.getOrDefault(site, new ArrayList<Integer>());  // 没有则新建，有则读取并插入；站点号属于哪些公交线路
                for (int j : list) {
                    edge[i][j] = edge[j][i] = true;  // i和j两条公交线路相通（建图）
                }
                list.add(i);
                rec.put(site, list);
            }
        }

        int[] dis = new int[n];
        Arrays.fill(dis, -1);
        Queue<Integer> que = new LinkedList<Integer>();
        for (int bus : rec.getOrDefault(source, new ArrayList<Integer>())) {  // 初始点所在的公交线路全部作为起点
            dis[bus] = 1;  // 初始点到这几条公交线路只需一辆公交车（同站上下情况已特判）
            que.offer(bus);
        }
        while (!que.isEmpty()) {
            int x = que.poll();
            for (int y = 0; y < n; y++) {
                if (edge[x][y] && dis[y] == -1) {  // 因为是bfs，所以第一次访问到的一定是最少的换乘公交数
                    dis[y] = dis[x] + 1;
                    que.offer(y);
                }
            }
        }

        int ret = Integer.MAX_VALUE;
        for (int bus : rec.getOrDefault(target, new ArrayList<Integer>())) {  // 比较终点所属的多条公交线路中，换成次数最少的最为最终结果
            if (dis[bus] != -1) {
                ret = Math.min(ret, dis[bus]);
            }
        }
        return ret == Integer.MAX_VALUE ? -1 : ret;
    }
}
```

上面的代码里面建图部分最为重要，另外由于是以公交线路作为结点进行计算处理的，所以起点和终点需要判断所有所在的公交线路，在起点时所有的公交线路都要先插入队列，而在终点时，需要将所有的公交线路进行比较，其中最小的数值才是结果。

中间过程的计算实际上是把问题从a站到b站需要多少辆公交车改成了从a'公交线路到b'公交线路需要多少辆公交车（因为同公交车上乘坐不增加换乘数），从而实现了结点的缩减，也就让搜索的范围更加，简化了计算。

## [852、山脉数组的峰顶索引](https://leetcode-cn.com/problems/peak-index-in-a-mountain-array/)

我是傻逼，我是傻逼，我是傻逼！明显要logn就是二分，然后稍微拐个弯居然就不会做了。

因为不是有序数组，所以二分的条件肯定不是判断是否相等了，那判断啥呢？观察前后两部分的规律可以发现一个是上升一个是下降的。那就用上升和下降（即当前元素是否小于下一个元素判断下一个二分的范围），如果mid < mid + 1，那说明到mid + 1都还是上升的，需要在mid + 1到right里面找，如果mid > mid + 1，那说明mid到right都是下降的，在left到mid里面找，然后看一下最后返回是要哪个数值注意一下最终返回就可以了。

==**类似山峰数组，套路就是用前后元素的关系去判断，常规二分是当前值与目标值判断**==

```java
class Solution {
    public int peakIndexInMountainArray(int[] arr) {
        int mid = (arr.length - 1) / 2, left = 0, right = arr.length - 1;
        while(left < right){
            if(arr[mid] < arr[mid + 1]){
                left = mid + 1;
            } else{
                right = mid;
            }
            mid = left + (right - left) / 2;
        }
        return left;
    }
}
```

## [877、石子游戏](https://leetcode-cn.com/problems/stone-game/)

**题目类型：区间dp**

**数学方法**：按照相邻间隔分为两组，第一个选的人根据比较第一组和第二组的元素和，就可以确定选头还是尾，进而保证获胜。因为选取的过程中先手一方取掉一个可以保证后手一方只能拿到第二组的元素。

![fig1](https://assets.leetcode-cn.com/solution-static/877/877_fig1.png)

**动态规划（dp）方法**：i表示数组起始位置下标，j表示数组终止位置下标，$dp[i][j]$表示当剩下的石子堆为下标 *i* 到下标 *j* 时，当前玩家与另一个玩家的石子数量之差的最大值，注意当前玩家不一定是先手 Alex。最终问题的解其实就是dp[0][piles.length - 1]与0之间的关系，若大于0则 Alex 赢，否则输（由于总石子的个数为奇数所以必定分出胜负，不存在等于的情况）。

对于状态转移方程分为三部分：

- 当i>j时，数组无意义，所以dp[i][j]=0

- 当i=j时，这个时候只剩下了最后一堆石头，当前玩家只能取走这堆石子，有 $dp[i][i]=piles[i]$。这个应该是类似于递归的最根部情况？反正dp到最底下肯定要有个值，或者说是dp的初始值肯定要存在，那对于只剩一堆石子的情况当然插值就是piles[i]，那相应地最大值也就是piles[i]。

- 当i<j时，当前玩家的决策是从两种选取方案中决定的，选取其中的最优情况，即$dp[i][j]=max(piles[i] - dp[i+1][j], piles[j] - dp[i][j-1])$

==**区间dp是一类问题，第一次碰到，理解也不是很彻底，以后碰到再复习吧**==

## [879、盈利计划](https://leetcode-cn.com/problems/profitable-schemes/)

这道题跟474一样也是三维的dp数组的一道题，不过多拐了点弯。

首先可以同474一样简化为二维进行处理，二维$dp[i][j]$的含义是人数上限为i，利润至少为j的方案种数，所以最后的对应结果就是$dp[n][minProfit]$。然后因为含义是利润至少为j，在程序的过程中，对于后面的大于当前profit的情况，实际上处理是完全相同的，那么唯一有区别的就在前面。**在当前profit大于最低利润时，实际上就是跟上一行的0与正上方之和**。对于上一行的0应该这么理解，我又一堆人可以干活，又不是要利润恰好为0，是利润至少为0，所以相当于是现在集合中的子集。（用同样人数进行至少利润的行应该是单调递减的，相当于条件不断收紧）可以用下面的打一下然后理解。每一行分别表示用0个人用一个人，每一列表示对应的至少价值，在每一行里面从左到右是递减的。

打印最后一次的dp数组：

![打印输出](../images/LeetCode的菜鸡实录.assets/image-20210610012926032.png)

输入样例：

100 100 [2,5,36,2,5,5,14,1,12,1,14,15,1,1,27,13,6,59,6,1,7,1,2,7,6,1,6,1,3,1,2,11,3,39,21,20,1,27,26,22,11,17,3,2,4,5,6,18,4,14,1,1,1,3,12,9,7,3,16,5,1,19,4,8,6,3,2,7,3,5,12,6,15,2,11,12,12,21,5,1,13,2,29,38,10,17,1,14,1,62,7,1,14,6,4,16,6,4,32,48] [21,4,9,12,5,8,8,5,14,18,43,24,3,0,20,9,0,24,4,0,0,7,3,13,6,5,19,6,3,14,9,5,5,6,4,7,20,2,13,0,1,19,4,0,11,9,6,15,15,7,1,25,17,4,4,3,43,46,82,15,12,4,1,8,24,3,15,3,6,3,0,8,10,8,10,1,21,13,10,28,11,27,17,1,13,10,11,4,36,26,4,2,2,2,10,0,11,5,22,6]

然后用第二行举例，实际上就是表示用一个人实现价值至少为多少的集合数量。然后第二行第一个值19的含义是有19个任务只要一个人就能干，干了反正不是负利润；第二行第二个值16表示一个人干的活里面有16个任务是一个人干了然后价值不为0的，19和16之间的差值就是有3个任务是一个人就能干，然后干了价值还为0。

```java
class Solution {
    public int profitableSchemes(int n, int minProfit, int[] group, int[] profit) {
        int[][] dp = new int[n + 1][minProfit + 1];
        for (int i = 0; i <= n; i++) {
            dp[i][0] = 1;
        }
        int len = group.length, MOD = (int)1e9 + 7;
        for (int i = 1; i <= len; i++) {
            int members = group[i - 1], earn = profit[i - 1];
            for (int j = n; j >= members; j--) {
                for (int k = minProfit; k >= 0; k--) {
                    dp[j][k] = (dp[j][k] + dp[j - members][Math.max(0, k - earn)]) % MOD;
                }
            }
            if (i == len){
                for (int k = 0; k <= n; k++){
                    for (int j = 0; j <= minProfit; j++){
                        System.out.print(dp[k][j] + ",");
                    }
                    System.out.println();
                }
                System.out.println();
            }
        }
        return dp[n][minProfit];
    }
}
```

实现代码：

```java
class Solution {
    public int profitableSchemes(int n, int minProfit, int[] group, int[] profit) {
        int[][] dp = new int[n + 1][minProfit + 1];
        for (int i = 0; i <= n; i++) {
            dp[i][0] = 1;
        }
        int len = group.length, MOD = (int)1e9 + 7;
        for (int i = 1; i <= len; i++) {
            int members = group[i - 1], earn = profit[i - 1];
            for (int j = n; j >= members; j--) {
                for (int k = minProfit; k >= 0; k--) {
                    dp[j][k] = (dp[j][k] + dp[j - members][Math.max(0, k - earn)]) % MOD;
                }
            }
        }
        return dp[n][minProfit];
    }
}
```

写到最后其实还有点似懂非懂。。。也没办法了只能等后面有机会再理解了。连着几天dp了，今天其实理解的还是差在正好用j个人和用j个人（可以有人不用）的区别上面吧，下面的图是关于这个问题的解释，本质体现在初始化和最终结果上，如果是正好用j个人，那肯定只有用0个人的时候是0（最初的情况），如果是用j个人没强制是否用完，那实际上第一行全部都初始为1，如果是正好用j个人，最终输出就要把各个可能的人数做一遍统计，如果是用j个人，那就已经默认把用多少人的情况考虑在里面了，直接输$dp[n][minProfit]$就行。

![image-20210610014017019](../images/LeetCode的菜鸡实录.assets/image-20210610014017019.png)

## [977、有序数组的平方](https://leetcode.cn/problems/squares-of-a-sorted-array/)

这题其实读题面的时候没理解，什么叫“非递减顺序”，反正结合给的示例理解了下，应该就是小于等于号连接吧。然后又因为知道是双指针的题，所以思路很好想。

正常暴力的做法肯定就是先平方后排序，那么时间复杂度就会到$O(nlog_n)$，但实际上由于存在大小关系，所以是能将时间复杂度压缩到$O(n)$的。

因为结果中还是要求非递减顺序，所以相当于要找数组中绝对值最小的数（相等的里面最后一个）。

按照这个思路，自己实现了以下的代码：

```python
class Solution:
    def sortedSquares(self, nums: List[int]) -> List[int]:
        last = 10**5
        for index in range(len(nums)):
            if abs(nums[index]) > abs(last):
                break
            else:
                last = nums[index]
        right = index
        left = index - 1
        result_list = []
        while left >= 0 and right < len(nums):
            if abs(nums[left]) < abs(nums[right]):
                result_list.append(nums[left]**2)
                left -= 1
            else:
                result_list.append(nums[right]**2)
                right += 1
        while left >= 0:
            result_list.append(nums[left]**2)
            left -= 1
        while right < len(nums):
            result_list.append(nums[right]**2)
            right += 1
        return result_list
```

[官方题解](https://leetcode.cn/problems/squares-of-a-sorted-array/solutions/447736/you-xu-shu-zu-de-ping-fang-by-leetcode-solution/)里面的双指针给了两种思路：

1. 根据正数和负数将nums拆成两半，那么在平方之后，两个数组均满足降序（或等于），然后依次比较并插入结果中即可

2. 设置头尾为双指针，然后倒序插入结果中。（好处在于不用处理边界情况）

   ```python
   class Solution:
       def sortedSquares(self, nums: List[int]) -> List[int]:
           n = len(nums)
           ans = [0] * n
           
           i, j, pos = 0, n - 1, n - 1
           while i <= j:
               if nums[i] * nums[i] > nums[j] * nums[j]:
                   ans[pos] = nums[i] * nums[i]
                   i += 1
               else:
                   ans[pos] = nums[j] * nums[j]
                   j -= 1
               pos -= 1
           
           return ans
   
   链接：https://leetcode.cn/problems/squares-of-a-sorted-array/solutions/447736/you-xu-shu-zu-de-ping-fang-by-leetcode-solution/
   ```

第一种思路其实和自己写的差不多，第二种的话也有想到，但倒序插入这个没想好，其实就是知道结果有多长，知道最后数组最后一位的下标，然后从尾往前插。

## [1049、最后一块石头的重量 II](https://leetcode-cn.com/problems/last-stone-weight-ii/)

这道题和494明明就是一毛一样啊，然后没做出来，吐了好吧。

首先是所谓的消去计算，本质上就是在加正负号，这个是好看出来的；然后是得到的结果用在下一步的运算里面的时候，这时候可能为正可能为负（被减数和减数的区别），如果为正，那相当于符号不变，**如果为负，那就是相当于之前的式子加了个括号外面多个符号呗**。我tm这都没看出来

分析出来这个之后其实就好做了，问题和494一样，一部分正数一部分负数，正数-负数=最后一块石头的重量。又因为有和494一样的正负号求和式的性质，所以本质上只要拿到正数或者负数中间的一组就足够了，然后因为正数项和比较多，负数项和比较少，所以肯定是弄个负数和了。负数和的具体数值实际上并不知道，不过它的范围是可以确定的，$0~sum_{array}/2$向下取整。至此问题变成了求数组项中小于等于$sum_{array}$一半的最大和。

开一个int型的dp数组，初始时没有任何数可以加，因此默认情况都为0。然后在转移的情况时，最大重量的获取有两种情况，用当前石头和不用当前石头，如果是不用当前石头，那就是上面照抄，如果用当前石头，那就是上面左侧的重量加上当前石头的重量；反正这两个里面取最大的一个结果，因为想要的数值其实是在范围中要求越大越好。这样的话内部循环由于要用到上面一层左侧的结果，所以还是用倒序的顺序。

最后输出结果也tm很绝，因为dp数组表示的含义是不超过这个下标数值的最大数组元素和，所以只需要用整个数组的和减去两倍的负数项和就行了。（负数项的和实际上就是dp[sum/2]）

```java
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int sum = 0;
        for(int i : stones){
            sum += i;
        }
        int negative = sum / 2;
        int[] dp = new int[negative + 1];
        for (int i = 1; i <= stones.length; i++) {
            for (int j = negative; j >= stones[i - 1]; j--) {
                dp[j] = Math.max(dp[j - stones[i - 1]] + stones[i - 1], dp[j]);
            }
        }
        return sum - 2 * dp[negative];
    }
}
```

说难其实第一层弯转过来之后也不难，总的来说还是这类问题理解不到位，后续这题最好还是补一下，可以结合494一起理解。

## [1155、掷骰子等于目标和的方法数](https://leetcode.cn/problems/number-of-dice-rolls-with-target-sum/description/)

这题其实状态的转移是想到了的，但思路只写了递归，肯定超时的。然后自己想的时候，状态转移的DP不知道咋定义，想的是个三维的。

状态转移方程：$f(i,j)$表示用$i$个骰子投掷出和为$j$的方法数

$f(i,j)=\sum^{k}_{x=1}f(i-1,j-x)$

边界条件：$f(0,0)=1$和$f(i,j)=0$ if $j<0$

最终答案为$f(n,target)$

明确状态转移方程之后其实就简单了，然后倒序遍历也是DP常规的处理思路。

```python
class Solution:
    def numRollsToTarget(self, n: int, k: int, target: int) -> int:
        dp = [1]
        for i in range(target):
            dp.append(0)
        for i in range(n):
            for j in range(target,0,-1):
                dp[j] = 0  // 这一步不能少，一开始处理的时候就是漏了，要先清空当前位的值，不然后面+=会影响到
                for m in range(max(j-k,0),j):
                    dp[j] += dp[m]
                    dp[j] %= (10**9 + 7)
            dp[0] = 0
        return dp[-1]
```

这里其实还有一种写法是把$dp[0]=0$直接合并到循环里面，就是下面这种。

```python
class Solution:
    def numRollsToTarget(self, n: int, k: int, target: int) -> int:
        mod = 10**9 + 7
        f = [1] + [0] * target
        for i in range(1, n + 1):
            for j in range(target, -1, -1):
                f[j] = 0
                for x in range(1, k + 1):
                    if j - x >= 0:
                        f[j] = (f[j] + f[j - x]) % mod
        return f[target]
```

## [==1156、单字符重复子串的最大长度==](https://leetcode.cn/problems/swap-for-longest-repeated-character-substring/)

真得是写得火大加一口老血吐出来，改了半天才实现对了逻辑。

自己实现的其实和这个[题解](https://leetcode.cn/problems/swap-for-longest-repeated-character-substring/solutions/2294588/python3javacgotypescript-yi-ti-yi-jie-sh-uq9g/?envType=daily-question&envId=2023-10-27)比较像，但是写法不好，所以最后又臭又长。题解里说这是双指针，但我感觉应该是三指针？这个思路比较好理解，就是字母数量有限，先扫一遍确定各个字母的数量，然后去扫字符串。但是我想得那个是扫固定的26遍（虽然可以优化跳一些不存在的，但是还是扫n次整个串级别的时间；题解的实现里面除了第一个，后面都不需要再扫整个串了），对应每个字母，但实际上没有必要。题解是整体只扫一遍，并且扫的时候还针对连续字符做了一个跳过（这个跳过其实我的实现里也有做，只是没有能够想到整体只扫一遍的思路）

```python
class Solution:
    def maxRepOpt1(self, text: str) -> int:
        cnt = Counter(text)
        n = len(text)
        ans = i = 0
        while i < n:
            j = i
            while j < n and text[j] == text[i]:
                j += 1
            l = j - i
            k = j + 1
            while k < n and text[k] == text[i]:
                k += 1
            r = k - j - 1
            ans = max(ans, min(l + r + 1, cnt[text[i]]))
            i = j  
            # 可以挪到第一次找到的不一样的字符上，因为前面的字符都相同，截掉几个后肯定没已经计算过的长
        return ans
```

官方题解也是这个思路，但是描述上解释为滑动窗口。

```python
class Solution:
    def maxRepOpt1(self, text: str) -> int:
        count = Counter(text)
        res = 0
        i = 0
        while i < len(text):
            # step1: 找出当前连续的一段 [i, j)
            j = i
            while j < len(text) and text[j] == text[i]:
                j += 1

            # step2: 如果这一段长度小于该字符出现的总数，并且前面或后面有空位，则使用 cur_cnt + 1 更新答案
            cur_cnt = j - i
            if cur_cnt < count[text[i]] and (j < len(text) or i > 0):
                res = max(res, cur_cnt + 1)

            # step3: 找到这一段后面与之相隔一个不同字符的另一段 [j + 1, k)，如果不存在则 k = j + 1
            k = j + 1
            while k < len(text) and text[k] == text[i]:
                k += 1
            res = max(res, min(k - i, count[text[i]]))
            i = j
        return res
```

**==这题肯定得二刷==**

## [1239、串联字符串的最大长度](https://leetcode-cn.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/)

想到了位运算，也想到了题解的第二种迭代+位运算的方法，但不会写....

因为是限定的26个英文字母（只有小写），所以完全可以当成26位的2进制数去处理。

题解里给的第一种方法是回溯，第二种是迭代，其实本质上都是差不多的。

以第二种为例来解释，对于每个新增的字符串，都需要同已知的字符串中的所有可能成立的串进行匹配判断出是否可以拼接，对于26位的2进制数实际上就是取与看是否有为1的位数。将所有的这些数塞到一个集合里面不断递增，最后集合中最长的就是结果的最大长度。

```java
class Solution {
    public int maxLength(List<String> arr) {
        int ans = 0;
        List<Integer> masks = new ArrayList<Integer>();
        masks.add(0);
        for (String s : arr) {
            int mask = 0;
            for (int i = 0; i < s.length(); ++i) {  // 判定当前字符是否有效
                int ch = s.charAt(i) - 'a';
                if (((mask >> ch) & 1) != 0) { // 若 mask 已有 ch，则说明 s 含有重复字母，无法构成可行解（右移）
                    mask = 0;
                    break;
                }
                mask |= 1 << ch; // 将 ch 加入 mask 中（或等于同+=，左移）
            }
            if (mask == 0) {
                continue;
            }
            int n = masks.size();  // 判断加入集合中是否有效
            for (int i = 0; i < n; ++i) {
                int m = masks.get(i);
                if ((m & mask) == 0) { // m 和 mask 无公共元素
                    masks.add(m | mask);
                    ans = Math.max(ans, Integer.bitCount(m | mask));
                }
            }
        }
        return ans;
    }
}
```

## [1465、切割后面积最大的蛋糕](https://leetcode.cn/problems/maximum-area-of-a-piece-of-cake-after-horizontal-and-vertical-cuts/)

这玩意第一反应就是贪心，然后一下子感觉又不会这么简单。仔细分析之后感觉确实是贪心就可以了。因为一开始考虑的$1*7$和$2*3$这种关系，就是某一侧的最大值是不是有可能不是组成最大面积的那个。不过仔细思考之后发现是可以用最大乘最大这个规律的。因为最大的面积如果一条边的大小确定，那另一条边又是垂直切的，所以对于各块来说大小关系是确定的。

自己的实现：

```python
class Solution:
    def maxArea(self, h: int, w: int, horizontalCuts: List[int], verticalCuts: List[int]) -> int:
        horizontalCuts.sort()
        verticalCuts.sort()
        horizontalCuts.insert(0,0)
        horizontalCuts.append(h)
        verticalCuts.insert(0,0)
        verticalCuts.append(w)
        list1, list2 = [], []
        for i in range(len(horizontalCuts) - 1):
            list1.append(horizontalCuts[i + 1] - horizontalCuts[i])
        for i in range(len(verticalCuts) - 1):
            list2.append(verticalCuts[i + 1] - verticalCuts[i])
        max1 = max(list1)
        max2 = max(list2)
        return (max1 * max2) % (10**9 + 7)
```

当然自己写的这个时间肯定还是浪费的，因为要往队列的头上去插东西，虽然对于python来说也就是一行的事情。。。这里反正优化的形式一开始也没想到，然后看了题解之后恍然大悟。

```python
class Solution:
    def maxArea(self, h: int, w: int, horizontalCuts: List[int], verticalCuts: List[int]) -> int:
        def calMax(arr, boardr):  # 怎么压缩操作的，两个常量不用插队列头
            res, pre = 0, 0
            for i in arr:
                res = max(i - pre, res)
                pre = i
            return max(res, boardr - pre)
        horizontalCuts.sort()
        verticalCuts.sort()
        return (calMax(horizontalCuts, h) * calMax(verticalCuts, w)) % (10 ** 9 + 7)
```

## [1713、得到子序列的最少操作次数](https://leetcode-cn.com/problems/minimum-operations-to-make-a-subsequence/)

这题感觉太综合太乱了，一堆前置知识。。。就先只抓最长递增子序列讲吧。

因为是让target成为子序列，所以可以确定的是最少的操作次数就是arr里面的一个什么子串跟target本身长度的差值（这个确实想到了）。但是虽然确定了是要找到这样的一个序列，但是都是乱序也没法比较判断啥的。因为target里面是没重复的，所以就会有用target本身的下标去做值，把arr里面的值全部换掉。这样换完之后target就是一个从0开始的逐个递增的数组，那在arr里面要找的就是一个最长（严格）递增子序列。至此，这个题目的第一阶段简化就实现了，后面是另一个常规问题，求一个数组中的最长递增子序列的长度。

[300. 最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)这道题目描述的就是求最长递增子序列长度的问题，题目给了两种解法，第一种是动态规划（后面做到这道题目的时候再看），第二种就是贪心+二分。

先大概描述一下为啥这里贪心的实现在逻辑上没问题。如果我这个值必最后的那个数要大，或者本身就还是个空的，那我肯定就是直接插到最后就行了，相当于在把数组长度撑开，也就是在更新能够达到的最大长度；但是当最后的那个值比当前值要大，那实际上就是断了嘛，不是直接变得更长，而是相当于回到前面中间的一个位置开始慢慢累增。每一次写入都是找比它小的最大（最右边）的那个数的下一个位置进行填入，目的就是说让这个数列上升地越慢越好，这样不断更新。如果正好是说又有比原本数列那种大的可以接下去，就直接加在后面，还是可以扩充长度，因为之前的那个值是做替换，长度还是保留着的（0,2,12先碰到4把2换掉了，然后再碰到14，还是可以直接加后面扩充长度，实际上序列是0,2,12,14效果是等价的）

这边上面一段解释也是比较乱，是写题解之前半猜半凑想出来的一种想法，目前来看算是能通，后面等到有机会的时候还是再翻出来捋一捋这道题目吧，最好是把300和1143整完，可能理解起来会更通顺一点。

```java
class Solution {
    public int minOperations(int[] target, int[] arr) {
        int n = target.length;
        Map<Integer, Integer> pos = new HashMap<Integer, Integer>();
        for (int i = 0; i < n; ++i) {
            pos.put(target[i], i);
        }
        List<Integer> d = new ArrayList<Integer>();
        for (int val : arr) {
            if (pos.containsKey(val)) {
                int idx = pos.get(val);
                int it = binarySearch(d, idx);
                if (it != d.size()) {
                    d.set(it, idx);
                } else {
                    d.add(idx);
                }
            }
        }
        return n - d.size();
    }

    public int binarySearch(List<Integer> d, int target) {
        int size = d.size();
        if (size == 0 || d.get(size - 1) < target) {
            return size;
        }
        int low = 0, high = size - 1;
        while (low < high) {
            int mid = (high - low) / 2 + low;
            if (d.get(mid) < target) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }
}
```

代码放在这里了，但是我感觉下一次再看的时候能不能看懂都是个问题，尤其是下面二分的那块怎么理解好，high=mid这个，反正现在都蛮晕的，等之后头脑清醒的时候再说吧。

## [1833、雪糕的最大数量](https://leetcode-cn.com/problems/maximum-ice-cream-bars/)

md刷题刷的魔怔了，明明是个傻逼贪心都认不出，居然还拿dp写，偏偏那个dp还有点忘了。

最简单的思路就是排序+从大到小选，反正就要数量最多嘛。

```java
class Solution {
    public int maxIceCream(int[] costs, int coins) {
        Arrays.sort(costs);  // Arrays，不是Array
        int count = 0;
        int n = costs.length;
        for (int i = 0; i < n; i++) {
            int cost = costs[i];
            if (coins >= cost) {
                coins -= cost;
                count++;
            } else {
                break;
            }
        }
        return count;
    }
}
```

官方题解的第二种感觉上是空间换时间？先顺序扫一遍做统计，然后再用值去扫（可以降低到线性）。想明白了也算是简单的

![image-20210702204116892](../images/LeetCode的菜鸡实录.assets/image-20210702204116892.png)

```java
class Solution {
    public int maxIceCream(int[] costs, int coins) {
        int[] freq = new int[100001];
        for (int cost : costs) {
            freq[cost]++;
        }
        int count = 0;
        for (int i = 1; i <= 100000; i++) {
            if (coins >= i) {
                int curCount = Math.min(freq[i], coins / i);  // 这一步比较巧妙，对于硬币够多的时候前面成立，硬币不够后面成立
                count += curCount;
                coins -= i * curCount;
            } else {
                break;
            }
        }
        return count;
    }
}
```

以后看题的思路顺序还是应该**先贪心后dp**，其实是根据题目具体要求来的，不过肯定贪心比dp优先就是了。

==**另外想到的dp表达含义的问题对应的是879，这里标注下**==

## [1838、最高频元素的频数](https://leetcode-cn.com/problems/frequency-of-the-most-frequent-element/)

这破题好像思路很多？看题解有前缀和有滑动窗口，还有二分枚举区间长度的。

最简单的想法（我连这个都没想到），直接对求到和的那个数进行枚举，因为就这么多种可能，完全可以枚举出来，这个时间复杂度是O(n^2)，可能跟我一开始想到的开个差值数组存差不多？其实差值数组那块的想法跟前缀和有点像，但是没掌握。

然后就是滑动窗口，这个算是模板做法吧，贴个评论里面的模板总结：（当时只想到了3，4那个只能算沾了一点点边。。。）

![image-20210719154226203](../images/LeetCode的菜鸡实录.assets/image-20210719154226203.png)

其实想到了后面三步就很好做了，相当于是一个模板题，代码：

```java
class Solution {
    public int maxFrequency(int[] nums, int k) {
        Arrays.sort(nums);
        int left=0,res=0,total=0;
        for(int i=1;i<nums.length;i++){
            total+=(nums[i]-nums[i-1])*(i-left);
            while(total>k){
                total-=nums[i]-nums[left];
                left++;
            }
            res=Math.max(res,i-left);
        }
        return res+1;
    }
}
```

```java
class Solution {
    public int maxFrequency(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length;
        long total = 0;
        int l = 0, res = 1;
        for (int r = 1; r < n; ++r) {
            total += (long) (nums[r] - nums[r - 1]) * (r - l);
            while (total > k) {
                total -= nums[r] - nums[l];
                ++l;
            }
            res = Math.max(res, r - l + 1);
        }
        return res;
    }
}
```

前缀和和二分枚举如果有必要的话到时候再到题目里看吧，目前来说先用这个把滑动窗口弄懂都够呛了。

## ==滑动窗口解题模板==

![image-20210719154357175](../images/LeetCode的菜鸡实录.assets/image-20210719154357175.png)

## [1877、数组中最大数对和的最小值](https://leetcode-cn.com/problems/minimize-maximum-pair-sum-in-array/)

想明白了很简单一个题，主要是对于贪心的最优证明。

第一反应肯定是大配小才能让数对和比较小嘛，那最好想到的一种手段就是直接排个序一个个前后配对（就是贪心的思想，保证每对都尽可能小）。然后我自己是大概考虑了一下四个数的几种情况然后感觉贪心是对的。就比如a,b,c,d四个数，要么就(a,d),(b,c)两对，要么就是(a,c),(b,d)两对，为什么前面拿个比后面那个要好呢，因为结果是取得所有数对和里面的最大值。(a,c)虽然比(a,d)小了也比(b,c)小了，但是这么一弄，另外一对(b,d)可是比(a,d)和(b,c)都要大了，也就是说整体考虑来讲(a,c),(b,d)的最大数对和是要比(a,d),(b,c)大的，那你当中一对小没有意义啊，因为取得是所有数对的最大值。然后超过四个的没有细想，但是感觉应该也是符合这个规律的，搭配完后肯定会有一个数对会比原本所有的数对都要大。(a,b,c,d,e,f)要么就是(a,f)配完回到四个数的情况；要么就是(a,e)配完剩下(b,f),(c,d)，这里面(c,d)相当于可以不看，又变成四个数的情况；(a,d)这种么应该也是保证会有更大的出现的。

上面是当初我做的时候的思路，下面是题解给的：

![image-20210720125100441](../images/LeetCode的菜鸡实录.assets/image-20210720125100441.png)

可能刚开始看有点绕，但是多看畸变明白了还是很好懂得，大概的思想就类似于跷跷板，这边大的保证了小的就出问题。因为是反证法，只要有一个反例就行了（对于任意拆分方法，不妨假设不存在任何一组u,v使得...），所以$u<v$还有$x_v \geq x_{n+1-k^{'}}$都算是含在任意拆分方法里面了，只要找到一组u,v产生矛盾就行。（不存在任何一组大于等于就是所有u,v都小于）然后推了后面发现并不是都小于的，满足小于所需的情况数量不足，所以就与原假设矛盾。

贪心题知道了就好做，但是问题在于如何很好的证明。

## [1893、检查是否区域内所有整数都被覆盖](https://leetcode-cn.com/problems/check-if-all-the-integers-in-a-range-are-covered/)

暴力AC睡大觉

[差分数组](https://blog.csdn.net/qq_44786250/article/details/100056975)（数据结构题），这个是一个博客的解释。其实追溯其本质就是前后两个数进行计算得到的新数组，如果对原本的数组中的一段进行处理的话，差分数组中的值只有首尾会受到影响，中间的值不会被改变，因为中间的值左侧和当前位置都等效变换了，所以差分不变，头尾的话因为是跟前后比所以会发生变化。

这道题目里面对于原数组的变化实际上就是覆盖次数的描述，一段表示一种覆盖。因为这里是必加的，所以差分数组肯定是前正后负，如果过程大于0，则至少覆盖一次，如果小于等于0则未被覆盖。（这里用差分数组还可以实现到计算覆盖的次数）

![image-20210724003602324](../images/LeetCode的菜鸡实录.assets/image-20210724003602324.png)

![image-20210724003622092](../images/LeetCode的菜鸡实录.assets/image-20210724003622092.png)

![image-20210724003633030](../images/LeetCode的菜鸡实录.assets/image-20210724003633030.png)

![image-20210724003650785](../images/LeetCode的菜鸡实录.assets/image-20210724003650785.png)

代码如下：

```java
class Solution {
    public boolean isCovered(int[][] ranges, int left, int right) {
        int[] diff = new int[52];   // 差分数组（多开一位，因为要计算最后的那个数）
        for (int[] range : ranges) {
            ++diff[range[0]];
            --diff[range[1] + 1];  // 在范围结束的后一位作减法，因为范围结束的那一位还是能够覆盖到的
        }
        // 前缀和
        int curr = 0;
        for (int i = 1; i <= 50; ++i) {
            curr += diff[i];
            if (i >= left && i <= right && curr <= 0) {
                return false;
            }
        }
        return true;
    }
}
```

### 前缀和

关于前缀和，这个其实也在题解里面看到很多次了，如果有机会补在这里

## [2171、拿出最少数目的魔法豆](https://leetcode.cn/problems/removing-minimum-number-of-magic-beans/)

这题其实看了下提示后思路是ok了，但是死脑筋，没彻底转过来。

先说下核心思想吧，最终就是分两拨，一拨是0，另一拨是原本中的某一个值。

为啥一定是原本袋中有的数目作为另一拨的值，如果比某一个值更小，那一定比它大的都要多拿，必定不是最优。

当我们能枚举另一拨中的值时，要拿出的数目其实可以反推出来。因为原始豆子的总数知道，结果的豆子总数也能算出来，相减就是拿出来的数目。

那为了能更快的计算出有多少比它大的，最简单的方式就是排序。（这里自己一开始写的时候是想用hash表统计，但是发现无序的情况下还是不方便算，所以又对key排序，其实直接对整个beans的list排个序就行了）

自己的丑陋代码：

```python
class Solution:
    def minimumRemoval(self, beans: List[int]) -> int:
        num_dict = {}
        max_num = 0
        sum_num = 0
        for number in beans:
            sum_num += number
            max_num = max(max_num, number)
            if number in num_dict.keys():
                num_dict[number] += 1
            else:
                num_dict[number] = 1
        min_number = sum_num
        count1 = 0
        index = 0
        key_list = list(num_dict.keys())
        if len(key_list) == 1:
            return 0
        key_list.sort()
        for key in key_list:
            temp_number = sum_num - key_list[index]*(len(beans) - count1)
            min_number = min(min_number, temp_number)
            count1 += num_dict[key]
            index += 1
        return min_number
```

题解：

```python
class Solution:
    def minimumRemoval(self, beans: List[int]) -> int:
        n = len(beans)
        beans.sort()
        total = sum(beans) # 豆子总数
        res = total # 最少需要移除的豆子数
        for i in range(n):
            # 大于等于自己的所有beans最终都会拿成自己，其余会归零
            res = min(res, total - beans[i] * (n - i)) # 这里面隐含了多个同值的一定第一个最划算
        return res

链接：https://leetcode.cn/problems/removing-minimum-number-of-magic-beans/solutions/1270306/na-chu-zui-shao-shu-mu-de-mo-fa-dou-by-l-dhsl/
```

## [2517、礼盒的最大甜蜜度](https://leetcode.cn/problems/maximum-tastiness-of-candy-basket/)

这题看到的时候，思路反正完全是错的，看了题解之后才写，然后里面的实现还踩了下坑。

看了题解之后自己思考问题的想法：

首先，如果给出了一个价格差，要想确定这个能不能满足是比较简单的（可以在排过序的队列里，从第一个开始找，只要满足一定是可以找到的）。那么问题实际上就会被转化为要怎么样去枚举可能的价格差。确定价格差的步骤一定是走二分查找的，这个也没什么问题。既然要确定是不是满足在排过序的队列里会比较方便，那干脆一开始就直接排一下序得了。这样的话其实价格差的上限和下限都可以得到。上限的话就是价格的最大值减去价格的最小值，下限其实就是最接近的两个价格之差（这个下限其实需要遍历一次列表才能拿到，相较于后面的二分来说并不划算，所以题解里是用了最小有效值作二分查找的下限）

**题解中一个评论的思路：**

```
最大甜蜜度！看到最大，首先考虑二分。然后瞄一眼时间复杂度，思路方向应该对着呢。

然后考虑check函数怎么写，题目问什么，我们就二分什么。 题目问最大的甜蜜度是什么，因此二分甜蜜度。

那check(mid)中，传入的参数就是甜蜜度。那怎么去check它呢？

我们要挑选的k个礼物中，任意两种糖果价格绝对差的最小值是我们的甜蜜度。我们模拟一下这个过程。遍历整个price数组，为了使我们挑选的礼物足够的多，我们先将数组进行排序，然后从左往右挑选。如果前后两个礼物的价格差大于我们二分的甜蜜度，则认为当前礼物是合法的，立马计入到答案中去。
```

自己的实现：

```python
class Solution:
    def maximumTastiness(self, price: List[int], k: int) -> int:
        price.sort()
        min_threashold = 10 ** 9
        max_threashold = price[-1] - price[0]
        for i in range(len(price) - 1):
            min_threashold = min(price[i + 1] - price[i], min_threashold)    
        while min_threashold <= max_threashold:  # 这么写，结果是也能拿到，但是肯定要多算一次
            mid = (min_threashold + max_threashold) // 2
            if self.check_valid(price, mid, k):
                min_threashold = mid + 1
            else:
                max_threashold = mid - 1
        return max_threashold

    def check_valid(self, price, gap, k):
        index = 1
        temp = price[0]
        while k != 1 and index != len(price):
            if price[index] - temp >= gap:
                k -= 1
                temp = price[index]
            index += 1
        return True if k == 1 else False
```

上面这个自己的实现和题解的实现，区别有三个：第一个是二分下边界的确定；第二个是二分部分；第三个是while的循环。

题解代码：

```python
class Solution:
    def maximumTastiness(self, price: List[int], k: int) -> int:
        price.sort()
        left, right = 0, price[-1] - price[0]  # 遍历一遍list拿二分的下边界不划算，直接进二分
        while left < right:  # 二分的模板
            mid = (left + right + 1) // 2  # 注意+1
            if self.check(price, k, mid):
                left = mid  # 因为上面做了+1，所以这里直接mid就行
            else:
                right = mid - 1
        return left  # 上面的操作最终结果就是left

    def check(self, price: List[int], k: int, tastiness: int) -> bool:
        prev = -inf
        cnt = 0
        for p in price:
            if p - prev >= tastiness:
                cnt += 1
                prev = p
        return cnt >= k
```

[题解分析（带图）](https://leetcode.cn/problems/maximum-tastiness-of-candy-basket/solutions/2292300/javapython3tan-xin-pai-xu-er-fen-cha-zha-fxvv/)

**==二分的模板==**

**==同类型题目的思路：看到最大最小，可以联想二分查找考虑是否可行，然后思考check函数如何实现==**

python中的for和while的速度：for比while在同样**==访问列表获取值的情况下，for的速度优于while==**

[对比python中for和while运行速度的参考案例：](https://zhuanlan.zhihu.com/p/442896327)

在每次循环中，`while` 实际上比 `for` 多执行了两步操作：边界检查和变量 `i` 的自增。即每进行一次循环，while 都会做一次边界检查 (`while i < n`）和自增计算（`i +=1`）。这两步操作都是显式的纯 Python 代码。

`for` 循环不需要执行边界检查和自增操作，没有增加显式的 Python 代码（纯 Python 代码效率低于底层的 C 代码）。当循环的次数足够多，就出现了明显的效率差距。

## [2520、统计能整除数字的位数](https://leetcode.cn/problems/count-the-digits-that-divide-a-number/)

真得就是模拟...自己还在那边想了半天优化

题解：

```python
class Solution:
    def countDigits(self, num: int) -> int:
        return sum(num % int(i) == 0 for i in str(num))
```

这个题解反正思路也没啥可探讨的，无非就是python写法怎么取巧

自己想的，感觉是优化了一点点的...

首先是如果每一位都需要做判断的话，会有很多重复计算，因此这里想到的是以空间换时间，就是我先把答案算好记下来。（其实最开始还用位运算想更快一点...）

然后这里写的时候有一个问题，就是被2整除的不代表被4整除，被4整除的一定能被2整除。这里面的逻辑关系要理清楚。

```python
class Solution:
    def countDigits(self, num: int) -> int:
        num_list = [0,1,0,0,0,0,0,0,0,0]
        if num % 2 == 0:
            num_list[2] = 1
        if num % 4 == 0:
            num_list[4] = 1
        if num % 8 == 0:
            num_list[8] = 1
        if num % 5 == 0:
            num_list[5] = 1
        if num % 7 == 0:
            num_list[7] = 1
        temp = num
        count = 0
        count3 = 0
        count9 = 0
        sum = 0
        flag = num_list[2] == 1
        while temp != 0:
            tmp = temp % 10
            sum += tmp
            if tmp == 3 or (tmp == 6 and flag):
                count3 += 1
            elif tmp == 9:
                count9 += 1
            elif num_list[tmp]: 
                count += 1
            temp //= 10
        if sum % 3 == 0:
            count += count3
        if sum % 9 == 0:
            count += count9
        return count
```

题解写到一半突然明白为啥速度干不过了，因为自己是默认把它全算了一遍，但是实际上有些数字可能并没有实际出现，相当于做了不必要的计算。继续优化一丢丢：

```python
class Solution:
    def countDigits(self, num: int) -> int:
        num_list = [-1,1,-1,-1,-1,-1,-1,-1,-1,-1]
        temp = num
        count = 0
        count3 = 0
        count9 = 0
        sum = 0
        flag = num_list[2] == 1
        while temp != 0:
            tmp = temp % 10
            if num_list[tmp] == -1:
                if num >> 1 << 1 == num:
                    num_list[2] = 1
                else:
                    num_list[2] = 0
                    num_list[4] = 0
                    num_list[8] = 0
                if num >> 2 << 2 == num:
                    num_list[4] = 1
                    num_list[2] = 1
                else:
                    num_list[4] = 0
                    num_list[8] = 0
                if num >> 3 << 3 == num:
                    num_list[8] = 1
                    num_list[4] = 1
                    num_list[2] = 1
                else:
                    num_list[8] = 0
                if num % 5 == 0:
                    num_list[5] = 1
                if num % 7 == 0:
                    num_list[7] = 1
            sum += tmp
            if tmp == 3 or (tmp == 6 and flag):
                count3 += 1
            elif tmp == 6:
                if num_list[2] == 1:
                    count3 += 1
                elif num_list[2] == -1:
                    if num >> 1 << 1 == num:
                        num_list[2] = 1
                        count3 += 1
                    else:
                        num_list[2] = 0
            elif tmp == 9:
                count9 += 1
            elif num_list[tmp] == 1: 
                count += 1
            temp //= 10
        if sum % 3 == 0:
            count += count3
        if sum % 9 == 0:
            count += count9
        return count
```

相比于上面的，这个其实把数字之间的倍数关系用到了极致，就是算一次得到了结果，就把能获得的信息都记录下来，用来避免后续重复的计算。

## [2558、从数量最多的堆取走礼物](https://leetcode.cn/problems/take-gifts-from-the-richest-pile/)

这道题题面看完其实就想到了最大堆，然后剩下的感觉就是模拟计算了。然后在最大堆取出1的时候就能够提前终止。

自己的实现：

```python
class Solution:
    def pickGifts(self, gifts: List[int], k: int) -> int:
        import heapq
        length = len(gifts)
        gifts = [-gift for gift in gifts]
        heapq.heapify(gifts)
        for i in range(k):
            old = -heapq.heappop(gifts)
            if old == 1:
                return length
            new = int(old ** 0.5)
            heapq.heappush(gifts, -new)
        sum = 0
        for i in range(length):
            sum -= heapq.heappop(gifts)
        return sum
```

这个里面其实就需要知道一下**==python中的最小堆的库heapq==**，然后需要知道这个库是不支持最大堆的。如果需要用到最大堆，可以在最小堆插入和取出的时候取反，达成最大堆的效果。

官方题解：

```python
class Solution:
    def pickGifts(self, gifts: List[int], k: int) -> int:
        q = [-gift for gift in gifts]
        heapify(q)
        while k:
            x = heappop(q)
            heappush(q, -int(sqrt(-x)))
            k -= 1
        return -sum(q)
```

思路一毛一样，就是写法上有差异。

## [2645、构造有效字符串的最少插入数](https://leetcode.cn/problems/minimum-additions-to-make-valid-string/)

```python
# TODO
# 懒病犯了
```

这题其实题面都没读明白，和前一天的2696不一样，因为字符串匹配上之后是不删的。

第三种题解的思路其实想到一点了，就是先想办法求最终的组数再减去字符串长度。

留个坑在这，以后填吧。还有另两种思路也得看下。

## [2696、删除子串后的字符串最小长度](https://leetcode.cn/problems/minimum-string-length-after-removing-substrings/)

这都没想到写的暴力...

想到栈就没结束了

```python
class Solution:
    def minLength(self, s: str) -> int:
        stack = []
        for i in s:
            if len(stack) == 0:
                stack.append(i)
            else:
                if stack[-1] + i == "AB" or stack[-1] + i == "CD":
                    stack = stack[:-1]
                else:
                    stack.append(i)
        return len(stack)
```

官方题解：

这个写法上更巧妙，逻辑也合并了下

```python
class Solution:
    def minLength(self, s: str) -> int:
        stack = []
        for c in s:
            stack.append(c)
            if len(stack) >= 2 and ((stack[-2] == 'A' and stack[-1] == 'B') or (stack[-2] == 'C' and stack[-1] == 'D')):
                stack.pop()
                stack.pop()
        return len(stack)

链接：https://leetcode.cn/problems/minimum-string-length-after-removing-substrings/solutions/2591242/shan-chu-zi-chuan-hou-de-zi-fu-chuan-zui-968c/
```

## [2698、求一个整数的惩罚数](https://leetcode.cn/problems/find-the-punishment-number-of-an-integer/)

真烦哪，我好菜啊！

这道题，问题的转换其实很简单，最后就变成一个字符串切分成多个连续子串求和能否为目标值。

然后dfs的那种思路是真没想到，思路上有沾到过一点递归，但是还是没想出来，看了点题解之后用自己理解的写了。

自己写的屎：

```python
class Solution:
    def punishmentNumber(self, n: int) -> int:
        sum = 0
        for i in range(1,n+1):
            num = 1
            temp = i
            while temp//10:
                num += 1
                temp //= 10
            // 上面这个其实是用位数做了限制条件，和为2位，不可能加数为3位
            string = str(i**2)
            if self.check(string, num, i):
                sum += i**2
        return sum

    def check(self, string: str, num: int, target: int) -> bool:
        if len(string) == 0:
            return False
        if target == int(string):
            return True
        if target > int(string):
            return False
        temp = 0
        for i in range(num):
            if i == len(string):
                return False
            temp *= 10
            temp += int(string[i])
            if self.check(string[i+1:], num, target - temp):
               return True
        return False
```

check其实就是一个字符串string，切分最多不超过前num位，剩下的字符串能否达到新的目标值。其实这里面一堆都可以优化。

题解：

```python
class Solution:
    def punishmentNumber(self, n: int) -> int:
        def dfs(s: str, pos: int, tot: int, target: int) -> bool:
            if pos == len(s):  // 整个字符串s都遍历完了
                return tot == target
            sum = 0
            for i in range(pos, len(s)):
                sum = sum * 10 + int(s[i])
                if sum + tot > target:  // 这里和自己写的边界思想不同，用是否超过target作为条件
                    break  // 只要超过就不用继续向后了，因为肯定更超过target值
                if dfs(s, i + 1, sum + tot, target):  // 没超过target值还有希望，进去博一博
                    return True
            return False
        res = 0
        for i in range(1, n + 1):
            if dfs(str(i * i), 0, 0, i):
                res += i * i
        return res
```

这里面的dfs就是一个字符串s，前pos位求得的和为tot，对剩下的位进行计算，能否达到target值。

## [2707、字符串中的额外字符](https://leetcode.cn/problems/extra-characters-in-a-string/)

看题面其实已经知道是个DP了，但是还是没想出来DP方程是啥。看了提示后确定的，dp[i]表示s[0:i]的最优划分剩余字符数，然后就明确从前往后扫就可以了。在每个i中，把所有dictionary中的word都扫一遍，如果是末尾，那么就可以往前取捷径比最小值。

自己写的：

```python
class Solution:
    def minExtraChar(self, s: str, dictionary: List[str]) -> int:
        dp = [0 for _ in range(len(s) + 1)]
        for i in range(1, len(s) + 1):
            dp[i] = dp[i-1] + 1
            for word in dictionary:
                word_len = len(word)
                if i - word_len < 0:
                    continue
                elif s[i-word_len:i] == word:
                    dp[i] = min(dp[i],dp[i-word_len])
        return dp[len(s)]
```

[官方题解](https://leetcode.cn/problems/extra-characters-in-a-string/solutions/2590667/zi-fu-chuan-zhong-de-e-wai-zi-fu-by-leet-f0lu/)：

**题解中还涉及到一个字典树的优化**

```python
class Trie:
    def __init__(self):
        self.children = [None for _ in range (26)]
        self.isEnd = False
    
    def insert(self, word):
        node = self
        for ch in word:
            k = ord(ch) - ord('a')
            if node.children[k] == None:
                node.children[k] = Trie()
            node = node.children[k]
        node.isEnd = True

    def track(self, node, ch):
        k = ord(ch) - ord('a')
        if node == None or node.children[k] == None:
            return None, False
        node = node.children[k]
        return node, node.isEnd

class Solution:
    def minExtraChar(self, s: str, dictionary: List[str]) -> int:
        n = len(s)
        d = [sys.maxsize] * (n + 1)
        d[0] = 0
        trie = Trie()
        for e in dictionary:
            trie.insert(reversed(e))
        for i in range (1, n + 1):
            d[i] = d[i - 1] + 1
            node = trie
            for j in range(i - 1, -1, -1):
                node, ok = trie.track(node, s[j])
                if ok:
                    d[i] = min(d[i], d[j])
        return d[n]
```

## [2765、最长交替子数组](https://leetcode.cn/problems/longest-alternating-subarray/)

这题咋说呢，也没啥特别的思路，就是逻辑有没有全理到。

略微说坑的点，无非就是一开始题面没完全读懂，一定要是差从1开始的；另外一个就是1和-1来回交替怎么记，自己写的时候是用了一个变量，但看了题解之后发现是可以和长度绑定的。

首先是题解里面的第一种，双层循环的方法，这个就是暴力嘛，把所有的都扫一遍。这个反正肯定能算出来，但是会有子数组被重复计算的情况，算是暴力解法吧。

```python
class Solution:
    def alternatingSubarray(self, nums: List[int]) -> int:
        res = -1
        n = len(nums)
        for firstIndex in range(n):
            for i in range(firstIndex + 1, n):
                length = i - firstIndex + 1
                if nums[i] - nums[firstIndex] == (length - 1) % 2:
                    res = max(res, length)
                else:
                    break
        return res

链接：https://leetcode.cn/problems/longest-alternating-subarray/solutions/2610815/zui-chang-jiao-ti-zi-xu-lie-by-leetcode-2aevc/
```

其实想到了子数组会重复计算的情况就知道是要解决前一次扫完子数组，下一次外层循环应该从哪里开始继续。自己写的时候反正是写了一团的逻辑。

自己写的：

```python
class Solution:
    def alternatingSubarray(self, nums: List[int]) -> int:
        cond = 1
        last = 0
        res = 0
        for i in range(len(nums) - 1):
            if nums[i + 1] - nums[i] == cond:
                cond *= -1 
                res = max(res, i - last + 2)
            else:
                if last != i:
                    res = max(res, i - last + 1)
                    if nums[i + 1] - nums[i] == 1:
                        last = i
                        cond = -1
                    else:
                        last = i + 1
                        cond = 1
                else:
                    last += 1
        return res if res != 0 else -1
```

题解：

```python
class Solution:
    def alternatingSubarray(self, nums: List[int]) -> int:
        res = -1
        firstIndex = 0
        for i in range(1, len(nums)):
            length = i - firstIndex + 1
            if nums[i] - nums[firstIndex] == (length - 1) % 2:
                res = max(res, length)
            else:
                if nums[i] - nums[i - 1] == 1:
                    firstIndex = i - 1
                    res = max(res, 2)
                else:
                    firstIndex = i
        return res

链接：https://leetcode.cn/problems/longest-alternating-subarray/solutions/2610815/zui-chang-jiao-ti-zi-xu-lie-by-leetcode-2aevc/
```

## [2788、按分隔符拆分字符串](https://leetcode.cn/problems/split-strings-by-separator/)

这题没什么逻辑啥的，就是模拟，然后特殊情况其实在示例里也给到了，就是分隔符之间没有字符串的情况

```python
class Solution:
    def splitWordsBySeparator(self, words: List[str], separator: str) -> List[str]:
        result_list = []
        for word in words:
            temp_word = ""
            for character in word:
                if character != separator:
                    temp_word += character
                elif len(temp_word) != 0:
                    result_list.append(temp_word)
                    temp_word = ""
                else:
                    continue
            if len(temp_word) != 0:
                result_list.append(temp_word)
        return result_list
```

题解的写法更短...

```python
class Solution:
    def splitWordsBySeparator(self, words: List[str], separator: str) -> List[str]:
        res = []
        for word in words:
            res += [sub for sub in word.split(separator) if len(sub)]
        return res

链接：https://leetcode.cn/problems/split-strings-by-separator/solutions/2595926/an-fen-ge-fu-chai-fen-zi-fu-chuan-by-lee-bti9/
```



## [剑指 Offer 38. 字符串的排列](https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/)

肯定是回溯，关键是写法上，另外官方题解里面有给出一种空间复杂度能到O(1)的方法，但是实在没心情看了，这傻逼题目死活不过，艹了。

- 回溯可以设置访问数组判断使用状态
- 回溯进入时修改状态，回溯退出时还原状态，保证下一次的状态正常

另外有一个很重要的限制条件是，这里要求所有的序列中不能有重复的，当然你可以到插入答案的时候再判断是不是能加入（傻逼方法，并且交了一次还炸了，真的心态爆炸）

常规回溯部分：

![image-20210622145534156](../images/LeetCode的菜鸡实录.assets/image-20210622145534156.png)

限制不重复部分：

![image-20210622145556189](../images/LeetCode的菜鸡实录.assets/image-20210622145556189.png)

==**关于判断条件的解释**==：(!vis[j-1] 这个特别注意理解；因为这里判断进入的是跳过条件，所以如果左侧的未使用，也就是vis[j-1]为false才要跳过，因为一定保证先填的应该是相同字符中未填入的最左)

![image-20210622151633906](../images/LeetCode的菜鸡实录.assets/image-20210622151633906.png)

```java
class Solution {
    List<String> rec;
    boolean[] vis;

    public String[] permutation(String s) {
        int n = s.length();
        rec = new ArrayList<String>();
        vis = new boolean[n];
        char[] arr = s.toCharArray();
        Arrays.sort(arr);
        StringBuffer perm = new StringBuffer();
        backtrack(arr, 0, n, perm);
        int size = rec.size();
        String[] recArr = new String[size];
        for (int i = 0; i < size; i++) {
            recArr[i] = rec.get(i);
        }
        return recArr;
    }

    public void backtrack(char[] arr, int i, int n, StringBuffer perm) {
        if (i == n) {
            rec.add(perm.toString());
            return;
        }
        for (int j = 0; j < n; j++) {
            if (vis[j] || (j > 0 && !vis[j - 1] && arr[j - 1] == arr[j])) {
                continue;
            }
            vis[j] = true;
            perm.append(arr[j]);
            backtrack(arr, i + 1, n, perm);
            perm.deleteCharAt(perm.length() - 1);
            vis[j] = false;
        }
    }
}
```

另附[StringBuffer](http://c.biancheng.net/view/852.html)相关信息

## [剑指 Offer 42. 连续子数组的最大和](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)

很久没刷之后居然第一时间没想起这个是动态规划，不过凭借见到几次这种类型的题还是写出来了。题解里面给了两种方法，主要讲下第二种吧。

第一种方法（动态规划）：**连续子数组保证顺序的情况下，对于下一个元素只有选择并加入之前的数组和从它另起一个新数组两种情况。**（这个感觉应该就是动态规划的核心，情况二选一/***多选一***？）既然有了这个限制就可以很简单的得到动态规划的转移方程了，如下：![image-20210717231241837](../images/LeetCode的菜鸡实录.assets/image-20210717231241837.png)

因为是一维的方程，类似于二维方程可以简化空间到一维，一维方程可以简化到一个元素进行维护，从而使得**空间复杂度为O(1)**。（从这个角度说，代码写烦了。）题解代码如下：

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int pre = 0, maxAns = nums[0];  // 特别注意这里的pre=0，对应上式处理了i-1最初始的情况
        for (int x : nums) {
            pre = Math.max(pre + x, x);
            maxAns = Math.max(maxAns, pre);
        }
        return maxAns;
    }
}
```

第一种方法还是蛮简单的，只管这道题的话是足够了，但是这类题还可能有变体，所以要有第二种方法。

第二种方法（分治）：==线段树区间合并法解决**多次询问**的「区间最长连续上升序列问题」和「区间最大子段和问题」==

整体的流程跟归并排序有点像？拆分没啥好说的，就是淡出分就行了，奇数就给个固定的分割，主要是向上回溯的处理：

![image-20210718000342684](../images/LeetCode的菜鸡实录.assets/image-20210718000342684.png)

四个量的设置呢，看完感觉很有道理，但是让我自己想可能想不到。合并后的区间最大子段和实际上只有三种情况，要么全在左边，要么全在右边，要么横跨两个（从这个角度考虑像是动态规划的有限种情况？）全在左和全在右都好处理，横跨的话就是左边的右跟右边的左拼在一起才可能实现。感觉其实还没有彻底理解，不过这个思路还是应该要有的，如果没记错的话本科上算法课的时候貌似也有碰到过类似的题目。

![image-20210718001531524](../images/LeetCode的菜鸡实录.assets/image-20210718001531524.png)

分治方法（线段树）主要针对的是大规模查询，对于同一堆数据多次查询处理有利。

![image-20210718002004416](../images/LeetCode的菜鸡实录.assets/image-20210718002004416.png)

代码如下：

```java
class Solution {
    public class Status {
        public int lSum, rSum, mSum, iSum;

        public Status(int lSum, int rSum, int mSum, int iSum) {
            this.lSum = lSum;
            this.rSum = rSum;
            this.mSum = mSum;
            this.iSum = iSum;
        }
    }

    public int maxSubArray(int[] nums) {
        return getInfo(nums, 0, nums.length - 1).mSum;
    }

    public Status getInfo(int[] a, int l, int r) {
        if (l == r) {
            return new Status(a[l], a[l], a[l], a[l]);
        }
        int m = (l + r) >> 1;
        Status lSub = getInfo(a, l, m);
        Status rSub = getInfo(a, m + 1, r);
        return pushUp(lSub, rSub);
    }

    public Status pushUp(Status l, Status r) {
        int iSum = l.iSum + r.iSum;
        int lSum = Math.max(l.lSum, l.iSum + r.lSum);
        int rSum = Math.max(r.rSum, r.iSum + l.rSum);
        int mSum = Math.max(Math.max(l.mSum, r.mSum), l.rSum + r.lSum);
        return new Status(lSum, rSum, mSum, iSum);
    }
}
```

看评论好像还有种前缀和的方法？结果等于当前前缀和减去之前的最小前缀和。

## [剑指 Offer 45. 把数组排成最小的数](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

这题大方向其实还蛮好想的，字典序的解决，但是实际上一写就全是bug。其实这里面就只是要拐个小弯，不是直接比a和b两个字符串，而是比较ab和ba的拼接之后的字符串的大小。

![image-20210811165357735](../images/LeetCode的菜鸡实录.assets/image-20210811165357735.png)

然后，思想上就结了，后面都是实现的问题。实现上就是改一下快排的具体实现，下面是python的代码。

```python
class Solution:
    def minNumber(self, nums: List[int]) -> str:
        def quick_sort(l , r):
            if l >= r: return
            i, j = l, r
            while i < j:
                while strs[j] + strs[l] >= strs[l] + strs[j] and i < j: j -= 1
                while strs[i] + strs[l] <= strs[l] + strs[i] and i < j: i += 1
                strs[i], strs[j] = strs[j], strs[i]
            strs[i], strs[l] = strs[l], strs[i]
            quick_sort(l, i - 1)
            quick_sort(i + 1, r)

        strs = [str(num) for num in nums]
        quick_sort(0, len(strs) - 1)
        return ''.join(strs)
```

这里面主要难理解的就是while那边的三行，其实本质上就是把l当成了标志位去比较。

随手看到写的，后面还是用java或者别的再写的时候再详细理解下。

## [剑指 Offer 52. 两个链表的第一个公共节点==快慢指针==](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

简单题而且是常规思路，还是没想出来...快慢指针经典问题，题型有印象但是做法忘了。

快慢指针应该是有好多个版本，这个算是最简单的吧？

两个链表的长度不同，如果要用O(1)的空间去找到公共结点或者完成判断是否存在公共结点就一定要让两个结点在循环多次之后保证一定能同时到达某个相对位置。如果只考虑两个链表自身是肯定没办法实现这个情况的，那么两个链表虽然不一样长，但是两个链表的长度和应该是个定值啊，所以就会产生快慢指针的做法和思路。

既然是要通过在两个列表上都移动产生最终到达同一相对位置的状态，那么每个指针就要在自己这里到达结尾之后，成为下一个链表的队首。如果不存在公共节点，那不管怎么移动都不可能在过程中产生相同，当两个指针都到达另一个链表的末尾时，因为同时为null而产生了相同的情况，这个时候指针的返回结果也就是null可以直接返回（找到相同的公共节点那肯定两个指针就相同了，所以也直接返回其中的一个指针就可以了）这里如果两个指着一开始就都为空就直接返回null（网上查了一下，好像直接判断null==null返回的也是true？所以实际上连这个特判可能也不需要？）

如果存在公共节点，假设公共段长度为c，两个链表非公共段一个为a一个为b，那么在交换之后，a+c+b==b+c+a，也能保证在第一个公共节点的时候匹配到然后返回。

实际上知道了快慢指针的做法或者有了这个交换的思路就好做了，下面的代码是官方题解，写的还比较取巧，因为赋值运算的优先级比后面的运算低，所以相当于是根据pA和pB的情况分别赋予下一时刻的值。

代码如下：

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) {
            return null;
        }
        ListNode pA = headA, pB = headB;
        while (pA != pB) {
            pA = pA == null ? headB : pA.next;
            pB = pB == null ? headA : pB.next;
        }
        return pA;
    }
}
```

## [面试题 10.02. 变位词组](https://leetcode-cn.com/problems/group-anagrams-lcci/)

妄想用暴力过，果然是不显示的。我永远讨厌哈希...

算是数据结构题吧，用哈希去搜就能做，题解里面给的两种方案无非就是用的键不一样。其实键这个已经想到了，只是一直也不用哈希也不会也不熟。。。

第一种方案，直接用排序好的字符串做哈希的键（就跟我用暴力法想的一样）。

map.getOrDefault(A, B);  获取以A为键的值，如果没有则创建B作为返回值。

代码：

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<String, List<String>>();
        for (String str : strs) {
            char[] array = str.toCharArray();
            Arrays.sort(array);
            String key = new String(array);
            List<String> list = map.getOrDefault(key, new ArrayList<String>());
            list.add(str);
            map.put(key, list);
        }
        return new ArrayList<List<String>>(map.values());
    }
}
```

第二种方案说是用计数做，但是实现上看起来好像也是排序之后的字符串当键？

代码：

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<String, List<String>>();
        for (String str : strs) {
            int[] counts = new int[26];
            int length = str.length();
            for (int i = 0; i < length; i++) {
                counts[str.charAt(i) - 'a']++;
            }
            // 将每个出现次数大于 0 的字母和出现次数按顺序拼接成字符串，作为哈希表的键
            StringBuffer sb = new StringBuffer();
            for (int i = 0; i < 26; i++) {
                if (counts[i] != 0) {
                    sb.append((char) ('a' + i));
                    sb.append(counts[i]);
                }
            }
            String key = sb.toString();
            List<String> list = map.getOrDefault(key, new ArrayList<String>());
            list.add(str);
            map.put(key, list);
        }
        return new ArrayList<List<String>>(map.values());
    }
}
```

上下两种方法在时间和空间复杂性上略有不同。

其实还有一种数学方法，用到了质数分解唯一性，大概思路就是说找26个质数对应字母，然后用分解唯一性确定字符串的组合，也是一个只考虑出现字母而不考虑出现顺序的方法。

## [面试题 16.06. 最小差](https://leetcode-cn.com/problems/smallest-difference-lcci/)

排序后双指针即可解决，思想上还算简单。

主要就提一嘴测试的数据范围吧，只说两个输入是int内，没说差是int内啊，然后就上个long就行了（long long太大没必要）；另外可以一碰到0就弹出，可能可以稍微加快一下速度？

```c++
class Solution {
public:
    int smallestDifference(vector<int>& a, vector<int>& b) {
        sort(a.begin(), a.end());
        sort(b.begin(), b.end());
        int sign1 = 0, sign2 = 0, a_size = a.size(), b_size = b.size();
        long ans = INT_MAX;
        while (sign1 < a_size && sign2 < b_size){
            long temp = (long)abs(a[sign1] - b[sign2]);
            if (temp == 0){
                return 0;
            }
            ans = min(ans, temp);
            if (a[sign1] > b[sign2]) {
                sign2 ++;
            } else {
                sign1 ++;
            }
        }
        return ans;
    }
};
```



## 字节跳动双月模拟笔试

1. ![](../images/LeetCode的菜鸡实录.assets/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.04.45.png)

   ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.06.26.png)

   ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.06.36.png)

2. ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.18.47.png)

   ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.18.57.png)

3. ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.26.52.png)

   ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.27.02.png)

   ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.27.11.png)

   ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.27.19.png)

4. ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.33.10.png)

   ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.33.19.png)

   ![](../images/LeetCode的菜鸡实录.assets/截屏2022-02-19 19.33.26.png)

## 网易8/20笔试

1. ![3AAFDF683598D4BBCA695A13F3430CF4](../images/LeetCode的菜鸡实录.assets/3AAFDF683598D4BBCA695A13F3430CF4-1327547.jpg)

2. ![A73DBE8F5E03460F4355FD2A8735AC19](../images/LeetCode的菜鸡实录.assets/A73DBE8F5E03460F4355FD2A8735AC19-1327567.jpg)

3. ![4B461EEA523E771D14692F0EA3854F9E](../images/LeetCode的菜鸡实录.assets/4B461EEA523E771D14692F0EA3854F9E-1327595.jpg)

   位运算从高到低。刚开始所有的都放在一个set里面，然后从最高位往下判断；如果set中的数超过k个并且当前位有超过k个数为1，则缩减set使之只保留当前位为1的数；如果set中的数超过k个，但当前位为1的数不足k个，则不缩减set，继续判断下一位。直到某一时刻，set中的数不足k个，那么就在上一位的set中随机选k个就得到结果。（因为set中的元素数量只会减少或不变，所以当第一次出现不足k个就意味着后面那些位全是0了，所以不用遍历完每一位，可以提前剪枝）

4. ![6F3FA76777FAC2F560434DC289ACA39E](../images/LeetCode的菜鸡实录.assets/6F3FA76777FAC2F560434DC289ACA39E-1327611.jpg)

[题解参考1](https://www.nowcoder.com/discuss/1022172)

# 剑指offer

## 2.3.1 数组

### 面试题3：数组中重复的数字

#### 题目一：找出数组中重复的数字

这道题目其实本身给的限制条件是很充分的，所有的数字都限制在了0~n-1的范围内（变型的话，也可以是用一个函数从x映射到唯一y，本质上也就是多了一步映射）

最简单的思路：

- 冒泡循环，直接每个找重复的就行了，时间复杂度$O(n^2)$，空间复杂度$O(1)$
- 然后就是排序完之后一次循环找，排序$O(nlogn)$，一遍循环$O(n)$，所以整体的时间复杂度就是$O(nlogn)$，空间复杂度$O(1)$
- 如果用时间换空间的思想的话，可以通过一个哈希表以一次遍历完成，时间复杂度可以到$O(n)$，但是空间复杂度为O(n)

事实上，存在时间复杂度为$O(n)$，空间复杂度为$O(1)$的解法应该是最理想的，那么这样的解法也确实是存在的，当然这里面其实还是基于已经有很多限制因素在里面所以才能达成这样的一个复杂度。

**相似的题：[找出数组中所有重复元素](https://blog.csdn.net/qq_23594799/article/details/113357583)**  （确实是找出所有重复元素，但是之所以能实现是因为**存在每个元素值出现一次或者两次这个前提**，如果没有这个前提使用之前的思路是没有办法做到找出所有重复元素的）这道题目虽然思路和上面相近，但是实现方法还是不完全一样的（有些元素出现两次其它元素出现一次可以通过正负性来作为标志判别）。

```java
public List<Integer> findDuplicates(int[] nums) {
	List<Integer> list = new ArrayList<>();
  for(int i = 0; i < nums.length; i++) {
    int num = Math.abs(nums[i]);  // 不在乎这一位是什么，内容仅表示下标，如果下标对应的访问过就已经是负的，下标表数字
    if (nums[num - 1] > 0) {
      nums[num - 1] *= -1;
    } else {
      list.add(num);
    }
  }
  return list;
}
```

上面的情况是要求在不使用额外空间的情况下使用尽可能小的时间复杂度完成查找重复元素（只需要找到整个数组中重复元素中的任意一个即可），但是上面方法的问题在于，如果没有用额外空间保存原始数组，在使用算法之后，原先的保存顺序就会发生改变。那么如果不允许使用额外空间，同时又限制不能改变原始数组的顺序能否找到重复元素呢？其实是可以的。

本题实现（2022/06/30）：

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            while (nums[i] != i) {
                if (nums[nums[i]] == nums[i]) {
                    return nums[i];
                } else {
                    int index = nums[i];  // 此处交换实现多次出问题，主要是nums[i]更换值之后需要保留下标
                    nums[i] = nums[i] + nums[nums[i]];
                    nums[index] = nums[i] - nums[index];
                    nums[i] = nums[i] - nums[index];
                   /*
                    // 理想实现
                    int temp = nums[i];
                    nums[i] = nums[temp];
                    nums[temp] = temp;
                    */
                }
            }
        }
        return 0;
    }
}
```

##### 一道扩展（面试手撕）：给定一个无序数组，找出数组中的重复元素，将重复的部分放到数组末尾

限制时间复杂度$O(nlong_n)$，空间复杂度$O(1)$

输入：1,1,2,2,3  输出：1,2,3,1,2

因为时间复杂度给到了$O(nlog_n)$，所以可以直接先对数组进行一次排序，因此相当于可以默认原始的数组是有序的。

之后就只需要维护两个指针进行交换。第一个指针是遍历指针，在访问的过程中使用一个变量记录上一次的访问变量从而判断是否有重复。如果没有重复则放置在第二个指针处，第二个指针也是从头开始遍历，每当填充完一个变量向后一位。由于题目并没有要求数组中重复部分放在数组的末尾是否需要有序，因此可以直接交换两个指针上的值。



#### ==**题目二：不修改数组找出重复的数字**==

基本的思路是类似于二分的方式进行实现。因为只要求找到一个重复的元素就可以了，那么在n+1长的数组中只有1~n范围内的数字就造成了至少有一个数字会出现两次，当然可以不止两次。如果把整个数组按照数组中某个特定的值统计大于该值的数和小于该值的数，一定会存在一段超过填充完整区间内数值的数量。通过这个形式可以把多出来的某一个元素给找到。当然这样做没办法保证能找到所有的元素，因为如果在1~2的范围里，没有1，但是有两个2，从统计数量上来说也是对的，但是实际上也是存在重复元素。不过对于这种局部内的特殊情况并不会影响整体中找到一个的判断，因为在3~n的范围里面一定是n-1个，还是超过个数的，所以总会有一个超过的能找得到。这样的做法类似于对整个数组在二分，每次二分统计大于小于特定数值的元素数量时，需要进行一次循环。所以最后二分的次数和每次内部对所有元素的循环叠加在一起就是$O(nlogn)$的时间复杂度，空间复杂度还是$O(1)$的。这样也就是实现了在要求不拷贝原数组且保证顺序的情况下找到一个重复的元素。

其实不管上面的第一种要求还是下面的第二种要求，我感觉找到一个随机的重复元素可能实际意义都只是去确定这个数组中是否存在重复元素？如果要做去重或者统计什么元素应该还是用不了上面这两种方法的吧。**反正思路是有这样的两种思路，主要是在数量和范围上做文章，所以以后发现元素数量和范围关系差别固定并且不大的时候可以联想一下这两题用的思路和方法。**

==PS：这里的第二种要求还有个重点就是，二分查找不只是有序数组中找值可以用，在有条件限制的情况下，对于无序数组也能用，其实现的方式就可以是通过统计数量来做到的==

本题实现（2022/06/30）：**演化的二分查找应用**

```java
// ----- 不修改数组找出重复的数字（剑指offer题目三-2）
int[] nums = {2,3,5,4,3,2,6,7};
int number = getDuplicateNumberKeepOrder(nums, nums.length - 1);
System.out.println("Duplicate number is:" + number);
// ----- 不修改数组找出重复的数字（剑指offer题目三-2）

public static int getDuplicateNumberKeepOrderCore(int[] nums, int start, int end) {
    while (end >= start) {
        int middle = end - (end - start) / 2;
        int count = countRange(nums, start, middle);
        if (start == end) {
            if (count > 1) {
                return start;
            } else {
                break;
            }
        }
        if (count > middle - start + 1){
            end = middle;
        } else {
            start = middle + 1;
        }
    }
    return -1;
}

public static int countRange(int[] nums, int start, int end) {
    int count = 0;
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] >= start && nums[i] <= end) {
            count ++;
        }
    }
    return count;
}
```


### 面试题4：二维数组中的查找

首先，如果是对于一个无序的二维数组，那么要在其中查找一个值基本就要靠遍历完成了，不过这里所给的二维数组并非无序而是满足上小下大，左小右大的条件（左上角最小，右下角最大）。在这样一个有序的数组中进行查找的，我们可以很简单的根据一个子数组的左上角元素对于这个子数组中包含元素的范围作出一个初步的判断（即子数组中的值一定大于等于子数组最左上角元素的数值）。不过如果仅考虑这个性质要想实现对于特定元素的查找并不够充分。因为当子数组并没有包含所有可能的大于给定查找值的元素，子数组的左下角元素也可能大于给定的查找元素，因此，如果仅根据子数组本身内部的性质并不能实现最简化搜索空间的目的。

这道题目解决的关键在于如何理解这个有序的二维数组，[参考该题解的分析](https://blog.csdn.net/weixin_39889618/article/details/123128486)，**这个有序的二维数组如果从矩阵的右上角开始看，会发现是一棵==二叉搜索树==**（性质满足但并非就是，因为中间是有重叠元素的，所以严格意义上并不是二叉搜索树），左边比根节点小，右边比根节点大，这个性质是非常重要的，因为具有二叉搜索树的性质可以保证我们能够以更快速（类似于二分查找）的方式实现对于待查找元素的定位从而确定该元素是否存在与给定的二维数组中。既然符合二叉搜索树的性质，在查找的过程中我们就可以以子数组右上角的元素作为比较的标志进行查找（这一操作相当于通过对子树的根节点中的值进行比较，确定待查找的数可能存在于左子树中还是右子树中）。运用这个性质我们在比较子数组右上角元素的数值时，如果小于待查找元素，则下移一行，如果大于待查找元素，则左移一列。设原始二维矩阵的行数和列数分别为N和M，那么最多可能的移动行树不超过N+M，所以使用该方法在有序的二维数组中进行查找的时间复杂度不超过$O(N+M)$，而空间复杂度为$O(1)$。

**这道题目中最重要的知识点我觉得就是这种类型的有序二维数组可以被看作一棵二叉搜索树这一点**，这个性质对于满足该条件的二维数组是通用的。当然除了使用子数组的右上角进行比较，也可以使用子数组的左下角元素进行比较，当然移动的方向会有所不同。

本题实现（2022/06/30）：**该类有序二维数组具有二叉搜索树的性质**

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {  // 输入有效性的判断，n>=0,m>=0
            return false;
        }
        int height = matrix.length, width = matrix[0].length;
        int row = 0, column = width - 1;
        while (row < height && column >= 0) {
            if (matrix[row][column] == target) {
                return true;
            } else if (matrix[row][column] < target) {
                row ++;  // 直接++就行，只要是最后一行就肯定找不到了，直接用循环条件跳出
            } else if (matrix[row][column] > target) {
                column --;  // 直接--就行，只要是第一列就肯定找不到了，直接用循环条件跳出
            }
        }
        return false;
    }
}
```

## 2.3.2 字符串

c++ **常量字符串**（**字符串常量**），一个没有命名的字符串且为常量，存放在**静态数据区**。

### 面试题5：替换空格

这个问题实际上就是考察对字符串的插入操作的优化的一个问题。正常处理的方式如果从头开始检测空格的话，那么当有多个空格存在于原始字符串中，就会需要对空格后的元素做多次移动处理，这必然会导致有些元素被重复进行了多次的移动。

要想避免对于这些元素的重复移动就需要对移动的策略作出一些修改，即**从后向前遍历**（这么做的本质是保证每个元素都将仅使用一次就被移动到其最终应该所处的目标位置，避免对于同一元素的位置进行多次的移动，从而实现减少操作次数）。正常使用从前向后进行遍历的操作在运行的过程中的时间复杂度是$O(n^2)$的，因为需要两重循环；而将策略修改为从后向前进行遍历之后，最多只需要对数组进行两次遍历即可，时间复杂度可以降低到$O(n)$级别。对于数组的第一次遍历的作用在于统计原数组中所包含的空格个数，并以此计算出数组替换空格之后所需要的空间，确定新的数组尾所在的位置。对于数组的第二次遍历实现将数组中的元素偏移到指定位置，同时将空格替换为要求的内容。

另外这个题还有一个第一步就应该意识到的点就是这个数组在替换空格之后长度会发生改变，正是因为数组长度会发生改变，才需要对原数组采用从后向前的遍历方式避免对于相同元素进行多次移动。

本题实现（2022/06/30）：

```java
public static String replaceSpace(String s) {
        char[] str = s.toCharArray();
        int count = 0;
        for (int i = 0; i < str.length; i++) {
            if (str[i] == ' ') {
                count += 1;
            }
        }
        char[] strs = new char[str.length + 2 * count];
        for (int i = 0; i < str.length; i++) {
            strs[i] = str[i];
        }  // 这里其实没必要赋值，完全可以放到下面的里面去赋值，因为肯定没法原地处理
        for (int i = str.length - 1; i >= 0 && count > 0; i--) {  // 这里的count>0是模仿c++的原地处理的思路，实际上应该是没有上面的赋值，全部放在这个循环里面赋值掉 
            if (strs[i] != ' ') {
                strs[i + 2 * count] = strs[i];
            } else {
                strs[i + 2 * count] = '0';
                strs[i + 2 * count - 1] = '2';
                strs[i + 2 * count - 2] = '%';
                count --;
            }
        }
        return new String(strs);
    }
```

这道题略微特殊，如果不是用c++写用不了特性的话只能仿那个思路。

首先是按照[官方题解](https://leetcode.cn/problems/ti-huan-kong-ge-lcof/solution/mian-shi-ti-05-ti-huan-kong-ge-by-leetcode-solutio/)的内容，相当于假设全是空格，直接新建一个3倍长度的新数组，这样后面可以保证一次弄完。然后从屁股往前面塞。

```java
class Solution {
    public String replaceSpace(String s) {
        int length = s.length();
        char[] array = new char[length * 3];
        int size = 0;
        for (int i = 0; i < length; i++) {
            char c = s.charAt(i);
            if (c == ' ') {
                array[size++] = '%';
                array[size++] = '2';
                array[size++] = '0';
            } else {
                array[size++] = c;
            }
        }
        String newStr = new String(array, 0, size);
        return newStr;
    }
}
```

[另一个题解](https://leetcode.cn/problems/ti-huan-kong-ge-lcof/solution/mian-shi-ti-05-ti-huan-kong-ge-ji-jian-qing-xi-tu-/)：因为java没办法完全原地处理，所以有最暴力的写法，没啥难点，主要是java的char还有String，StringBuilder这几个之间的关系和转化

```java
class Solution {
    public String replaceSpace(String s) {
        StringBuilder res = new StringBuilder();
        for(Character c : s.toCharArray())
        {
            if(c == ' ') res.append("%20");
            else res.append(c);
        }
        return res.toString();
    }
}
```

==**整理一下java的char和String之间的转换**==（先不涉及StringBuilder，之后在别的里面具体整理这三个之间的关系）：

从String到char数组：

```java
String s = "abc";
char a[] = s.toCharArray();
```

从char数组到String：

```java
char a[] = {'a', 'b', 'c'}；
String s = new String(a);
```

## 2.3.3 链表

[c++ 指向指针的指针（多级间接寻址） ](https://www.runoob.com/cplusplus/cpp-pointer-to-pointer.html) P57

几类典型的单向链表问题： P57

- 从尾到头打印链表
- 删除链表的节点
- 链表中倒数第k个节点
- 反转链表
- 合并两个排序的链表
- 两个链表的第一个公共节点

### 面试题6：从尾到头打印链表

输入一个链表的头节点，从尾向头反过来打印出每个节点的值。

这个其实大体思路应该算蛮简单的，从尾到头就是倒序嘛。栈、递归都可以干这活。

- 使用栈：从头到尾遍历链表，并将每个节点中的内容保存到栈中，然后利用栈先进后出的性质获得倒序
- 使用递归：基本思路就是先递归到下一个节点，然后打印当前节点的内容

使用栈需要考虑链表特别长的情况，如果函数调用的层级很深，那么有可能导致函数调用栈溢出。用栈基于循环实现鲁棒性会更好。

主要其实是从这个问题里面引出关于循环和递归两种实现方法的讨论。

本题实现（2022/07/01）：

下面的是基于递归实现的

```java
class Solution {
    ArrayList<Integer> list = new ArrayList();  // 写在整个类的成员变量里面，允许多个方法的访问；注意类别里面是Integer
    public int[] reversePrint(ListNode head) {
        iter(head);
        int[] ans = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            ans[i] = list.get(i);  // ArrayList的访问不是数组一样的list[i]，而是用get
        }
        return ans;
    }
    public void iter(ListNode node) {  // 本身上面那个函数太反人类了，不好递归，因此自己弄一个函数方便递归
        if (node == null) {
            return;
        }
        iter(node.next);
        list.add(node.val);
        return;
    }
}
```

下面的是基于辅助栈实现的

```java
// stack
class Solution {
    public int[] reversePrint(ListNode head) {
        Stack<Integer> stack = new Stack();  // stack的几个常用函数
        while (head != null) {
            stack.push(head.val);
            head = head.next;
        }
        int[] ans = new int[stack.size()];
        int index = 0;
        while(!stack.empty()){
            ans[index++] = stack.pop();
        }
        return ans;
    }
}
// linkedlist
class Solution {
    public int[] reversePrint(ListNode head) {
        LinkedList<Integer> stack = new LinkedList<Integer>();  // LinkedList的常用函数方法
        while(head != null) {
            stack.addLast(head.val);
            head = head.next;
        }
        int[] res = new int[stack.size()];
        for(int i = 0; i < res.length; i++)
            res[i] = stack.removeLast();
    return res;
    }
}
```

评论的最优化方法：

```java
class Solution {
    int[] res;  // 先定义
    int i = 0;
    int j = 0;
    public int[] reversePrint(ListNode head) {
        solve(head);
        return res;
    }
    public void solve(ListNode head){
        if(head == null){
            res = new int[i];  // 到尾巴初始化（其实想到了在这初始化，只是没想到怎么给后面的访问，定义在外面即可）
            return;
        }
        i++; 
        solve(head.next);
        res[j] = head.val;
        j++;  // 尾巴的时候是0，每出来一层+1，实现倒序
    }
}
```

## 2.3.4 树

树的三种遍历方式：前序遍历（先序遍历）、中序遍历、后序遍历    

给出 先序+中序 或 中序+后序 可以唯一确定一棵二叉树（给出两种遍历方法，含中序即可）  实现？

三种遍历方式的实现方法：递归、循环

**宽度优先遍历**

二叉搜索树：左子节点总是小于或等于根节点，而右子节点总是大于或等于根节点

可以平均在$O(logn)$的时间内根据数值在二叉搜索树中找到一个节点。

**二叉树的两个特例：[堆（最大堆——根节点的值最大、最小堆——根节点的值最小）](https://blog.csdn.net/qq_41111491/article/details/104484870)、红黑树**

需要快速找到最大值或最小值的问题可以用堆解决（相比于排序，感觉堆的主要优势在于维护成本低，多次操作效率好）

构造最小堆：

```java
PriorityQueue<Integer> minheap = new PriorityQueue<>();
```

使用无参构造，元素在队列中默认按照从小到大的顺序排列，可保证每次出队列的元素为队列中的最小元素。

构造最大堆：

```java
PriorityQueue<Integer> maxheap = new PriorityQueue<>(Collections.reverseOrder());
```

将排序方法指定为反序，即元素从大到小排列，可保证每次出队列的元素为队列中最大的元素。

PS：Collections.sort()默认升序

**红黑树**是把树中的节点定义为红、黑两种颜色，并通过规则确保**从根节点到叶节点的最长路径的长度不超过最短路径的两倍**。

$h <= 2log(n + 1)$，其中h为红黑树高度，n为红黑树的节点个数。红黑树插入、查询、删除的时间复杂度均为$O(n)$，与AVL相同，但统计性能更好。

### 面试题7：重建二叉树

不写代码直接模拟的话应该是没啥问题的，主要是代码实现里面的一些细节。

因为给的是前序和中序，所以树的根节点可以直接取前序的第一个节点就能够确定下来了（相对的，如果是给的中序和后序，那么树的根节点就可以用后序的最后一个节点进行确定）

书上的思路：两个数组的头和尾，每次输入都将数组为空或者非法序列当作异常进行抛出。在从中序序列中找到根节点后，将中序序列分为左右两半，然后递归构建子树。由于返回的是根节点，所以直接将当前节点的左指针指向左子树（中序序列根节点之前的序列）递归返回的节点，当前节点的右指针指向右子树（中序序列根节点之后的序列）递归返回的节点。整个函数的返回值为当前子树的节点（**非空指针**）

[**另外一种思路**](https://blog.csdn.net/weixin_45405425/article/details/107905382)：使用书上的思路在构建左子树与右子树调用递归之前，先要对传入的内容是否为空进行判断，而使用这个博客的思路可能更为简洁。**它在每次处理两个字符串的时候，对两个字符串是否为空进行判断，如果为空则直接返回空指针**。这样操作的好处是使用递归构建左右子树时，不需要在调用递归函数之前进行额外的判断处理，因为如果序列为空则会直接返回空指针，叶节点的左右指针本来也是指向为空。当然这个博客的实现代码里面也不是非常严谨，因为如果注意细节的话需要对非法序列的情况进行判断，这个博客的代码应该是默认了两个序列都是合法序列。

上面两种思路的差别应该是因为语言和实现方式所导致的。书上的思路使用c++的指针进行实现，首尾指针都给出，无法实现传入的数组长度为0，而博客的思路使用java进行实现，传入的就是一维的数组，这个数组的长度可以为0。

本题实现（2022/07/01）：

```java
class Solution {
    HashMap<Integer, Integer> map = new HashMap<>();  // 如果不用hashmap存储，后面每次用遍历找节点会超时
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i],i);
        }
        return myBuildTree(preorder, inorder, 0, preorder.length - 1, 0, inorder.length - 1);
    }
    public TreeNode myBuildTree(int[] preorder, int[] inorder, int preorderStart, int preorderEnd, int inorderStart, int inorderEnd) {
        if (preorderStart > preorderEnd || inorderStart > inorderEnd) {  // 设置的返回条件应该鲁棒，对于还有一个节点的情况仍然可以使用下面的递归正常处理，因此只有当彻底溢出的时候直接return null比较合适
            return null;
        }
        int head = map.get(preorder[preorderStart]);
        int index = head - inorderStart;
        TreeNode node = new TreeNode(preorder[preorderStart]);
        node.left = myBuildTree(preorder, inorder, preorderStart + 1, preorderStart + index, inorderStart, inorderStart + index - 1);
        node.right = myBuildTree(preorder, inorder, preorderStart + index + 1, preorderEnd, inorderStart + index + 1, inorderEnd);
        return node;
    }
}
```

更简化的版本：

```java
class Solution {
    int[] preorder;  // 存一个就够了，inorder本质上只提供一个定位的信息，放在hashmap里就行了
    HashMap<Integer, Integer> dic = new HashMap<>();
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        this.preorder = preorder;
        for(int i = 0; i < inorder.length; i++)
            dic.put(inorder[i], i);
        return recur(0, 0, inorder.length - 1);
    }
    TreeNode recur(int root, int left, int right) {
        if(left > right) return null;                          // 递归终止
        TreeNode node = new TreeNode(preorder[root]);          // 建立根节点
        int i = dic.get(preorder[root]);                       // 划分根节点、左子树、右子树
        node.left = recur(root + 1, left, i - 1);              // 开启左子树递归
        node.right = recur(root + i - left + 1, i + 1, right); // 开启右子树递归
        return node;                                           // 回溯返回根节点
    }
}
```

官方题解里面还有个迭代的版本，感觉特别难理解，如果后面再看可以考虑按评论里的这个理解。

![image-20220701180406705](../images/LeetCode的菜鸡实录.assets/image-20220701180406705.png)

### 面试题8：二叉树的下一个节点

主要就是要对二叉树的中序遍历的顺序要熟悉，然后对于不同的情况分类处理就可以了。这个问题中的树还给了指向父节点的指针。

- 如果该节点存在右子节点，那么下一个节点就是该节点的右子节点，往左边一路走下去就行了
- 如果该节点不存在右子节点，且该节点是其父节点的左子节点，那么下一个节点就是该节点的父节点
- 如果该节点不存在右子节点，且该节点是其父节点的右子节点，那么就需要从其父节点一直往上走，直到某一时刻当前节点是父节点的左子节点，那么要寻找节点的下一个节点就是当前节点的父节点（这里面还有一种特殊情况，就是如果这个节点本身就是最后一个了，那么在不断向上的过程中，最后一定会到达根节点，而根节点不存在父节点，这个情况是需要特别注意的）

实际实现来说的，第二和第三种情况是存在共通之处的，可以合并在一起进行处理。代码的话就看书上P66、P67就行了。向上找节点的条件是**父节点不为空并且当前节点是父节点的右子节点**，这个判断条件如果一次都不执行，那么就是第二种情况，所以实现里面的话相当于第二第三种情况合并在一起了。

## 2.3.5 栈和队列

栈：先进后出

队列：先进先出

树的宽度优先遍历（层序遍历）使用队列实现

操作系统给每个线程创建一个栈用来存储函数调用时各个函数的参数、返回地址及临时变量等。

### 面试题9：用两个栈实现队列

经典问题，基本思想就是插入的时候全部都插到第一个栈里面，然后出来的时候如果第二个栈里面有东西就直接出第二个，如果第二个栈里面没东西就把第一个栈里面的东西全部倒到第二个栈里面然后再从第二个栈里面出来。

代码实现里面反正入栈很简单没啥东西，出栈的逻辑判断什么的注意下就可以了。（如果再进一步，栈的空间有限制，那么可能就需要多加一个判断条件，即判断当前操作-入栈 是否会使内容溢出就ok了）

**相关题目：**

用两个队列实现一个栈

思想上来说就是如果要入栈，那就往当前里面有东西的队列最后插。如果要出栈，那么就需要把一个栈里面除了最后一个元素的内容全部挪到另外一个队列里，然后原本的队列里面只剩一个的时候，就是要出栈的元素。两个队列就是在元素出栈的时候不断换来换去，保证队列里面的最后一个就是下一次要出栈的元素。

本题实现（2022/07/01）：

```java
// Stack
class CQueue {
    Stack<Integer> stack1, stack2;
    public CQueue() {
        stack1 = new Stack();
        stack2 = new Stack();
    }
    
    public void appendTail(int value) {
        stack1.push(value);
    }
    
    public int deleteHead() {
        if (!stack2.empty()) {
            return stack2.pop();
        }
        if (stack1.empty()) {
            return -1;
        }
        while (!stack1.empty()) {
            stack2.push(stack1.pop());
        }
        return stack2.pop();
    }
}
// LinkedList（用这个似乎速度更快）
class CQueue {
    LinkedList<Integer> A, B;
    public CQueue() {
        A = new LinkedList<Integer>();
        B = new LinkedList<Integer>();
    }
    public void appendTail(int value) {
        A.addLast(value);
    }
    public int deleteHead() {
        if(!B.isEmpty()) return B.removeLast();
        if(A.isEmpty()) return -1;
        while(!A.isEmpty())
            B.addLast(A.removeLast());
        return B.removeLast();
    }
}
```

LinkedList可以实现和Stack一样的功能，不过LinkedList似乎速度更快。别的没啥注意为空返回-1就行。

## 2.4 算法和数据操作

六种算法和数据操作的思路：
- 循环和递归：基于递归的实现方法代码比较简洁，但性能不如基于循环的实现方法
- 排序和查找：二分查找、归并排序、快速排序
- 回溯法：二维数组（迷宫或棋盘）搜索路径；回溯法适合用递归的代码实现，如果要求不能使用递归实现，再考虑用栈来模拟递归的过程
- 动态规划：如果面试题是求某个问题的最优解，并且该问题可以分为多个子问题，那么我们可以尝试用动态规划。用自上而下的递归思路分析动态规划问题（子问题存在重叠），用自下而上的循环代码实现（把子问题的最优解先算出来并用数组保存下来，基于子问题的解计算大问题的解）
- 贪婪算法：在分解子问题的时候存在某个特殊的选择，如果采用这个特殊的选择将一定能得到最优解
- 位运算：把数字表示成二进制之后对0和1的操作，总共有与、或、异或、左移和右移5种位运算

## 2.4.1 递归和循环

### 面试题10：费波那契数列

费波那契数列：**f(0) = 0**; f(1) = 1; f(n) = f(n-1) + f(n-2), n>1

经典问题，三种解法：

- 递归，效率低，书写简便，时间复杂度$O(2^n)$，空间复杂度$S(n)$，求费波那契数列n的值，对应二叉树的高度为n-1，树中的节点个数为$2^n-1$
- 循环代替递归，使用自下而上的循环代码，通过已经计算出的子问题得到大问题的解。（这个实现可以对比下leetcode70的那道爬楼梯，写法和顺序要注意，感觉用leetcode70自己的那段代码更好理解一点，每次都是把不要的先丢了，然后用挪好之后的去计算当前最新的就可以了）
- 快速幂：时间复杂度可以降低到$O(logn)$，但是实现有点复杂。**通项公式记住** 是矩阵的n-1次幂左上角的值，代码实现用位运算判断n的每一位上为0还是1，为1则乘，为0不处理，每步右移的同时要伴随矩阵的自乘

本题实现（2022/07/02）：

动态规划（循环）

```java
// 下面这种写法需要特判0的情况
class Solution {
    public int fib(int n) {
        if (n == 0)
            return 0;
        int a = 0, b = 0, c = 1;
        for (int i = 1; i < n; i++) {
            a = b;
            b = c;
            c = (int)((a + b) % (1e9 + 7));  // 如果用科学计数法就需要进行类型转换
        }
        return c;
    }
}
// 下面这种写法无需特判0的情况
class Solution {
    public int fib(int n) {
        int a = 0, b = 1, sum;
        for(int i = 0; i < n; i++){
            sum = (a + b) % 1000000007;
            a = b;
            b = sum;
        }
        return a;
    }
}
```

矩阵快速幂：（快速幂实现还是不熟练）

```java
class Solution {
    public int fib(int n) {
        if (n == 0)
            return 0;
        if (n == 1)
            return 1;
        // if (n < 2)
        //     return n;
        int[][] matrix = {{1,1},{1,0}};
        int[][] ans = quickMatrixPow(matrix, n - 1);  // 书P76的矩阵，n-1次
        return ans[0][0];
    }

    public int[][] quickMatrixPow(int[][] matrix, int n) {
        int[][] ans = {{1,0},{0,1}};
        while (n != 0) {  // 题解里面是n>0，确实n>0更符合理解
            if ((n & 1) == 1)
                ans = matrixMul(ans, matrix);
            matrix = matrixMul(matrix, matrix);
            n >>= 1;
        }
        return ans;
    }

    public int[][] matrixMul(int[][] matrix1, int[][] matrix2) {
        int[][] ans = new int[2][2];
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                ans[i][j] = (int)(((long)matrix1[i][0] * matrix2[0][j] + (long)matrix1[i][1] * matrix2[1][j]) % 1000000007);  // 这里要注意越界问题，如果不把数字变成long去乘会越界；形式
            }
        }
        return ans;
    }
}
```

快速幂的实现需要熟练，不管是数值型的还是矩阵的。另外要注意快速幂过程中的类型越界以及是否要取模问题。

相似问题：青蛙跳台阶问题（就是leetcode70，这类问题的关键是发现这是个费波那契数列，然后转换完问题的本质之后进行求解）

青蛙跳台阶问题扩展：一次可跳n阶，跳上一个n阶台阶的方法总共有$f(n)=2^{n-1}$种，[相关证明](https://blog.csdn.net/SongSir001/article/details/83277173)

相关问题——$2\times1$小矩形块填充$2\times8$大矩形：这个也是在靠怎么转化问题。如果第一个小矩形块放在最左边，要么横放要么竖放。如果竖着放，就剩下了$2\times7$的矩形块，那么如果把$2\times8$矩形块的填充问题看作f(8)，剩下的问题就是f(7)；如果横着放，因为左下角只有一种方式能填充完整，右边剩下了$2\times6$的矩形块，这样的话剩下的问题就可以看作是f(6)。合并两种情况就有f(8)=f(7)+f(6)，后续的情况可以以此类推，仍然是一个费波那契数列。

这上面不管是青蛙跳台阶还是矩形块的填充，最重要的都是得看得出思考到如果把上一个问题转化问下一个问题，有了这个转化之后就可以有通项公式，然后才能发现是费波那契数列，或者用其它简单的方式完成求解。

本题实现（2022/07/02）：

```java
class Solution {
    public int numWays(int n) {
        int a = 0, b = 0, c = 1;
        for (int i = 0; i < n; i++) {
            a = b;
            b = c;
            c = (a + b) % 1000000007;
        }
        return c;
    }
}
```

## 2.4.2 查找和排序

查找：顺序查找、二分查找、哈希表查找和二叉排序树查找 

要求在排序的数组（或部分排序的数组）中查找一个数字或者统计某个数字出现的次数，可以尝试用二分查找算法。

哈希表最主要的优点是可以在$O(1)$时间内查找某一元素，但是需要额外的空间来实现哈希表

排序：插入排序、冒泡排序、归并排序、快速排序

- 额外空间消耗
- 平均时间复杂度
- 最差时间复杂度

**==快速排序代码的实现==**

```java
import java.util.*;

public class Main {

    public static void main(String[] args) {
        int[] data = {-121, 15, 241, -23, -120, 57, 15161, 19, 781, 111};
        for (int i = 0; i < data.length; i++) {
            System.out.print(data[i] + ",");
        }
        System.out.println();
        System.out.print("===========");
        System.out.println();
        quicksort(data, 0, data.length - 1);
        for (int i = 0; i < data.length; i++) {
            System.out.print(data[i] + ",");
        }
    }

    public static int partition1(int[] data, int start, int end) {
        if (data.length == 0 || start < 0 || end >= data.length) {
            System.out.println("Invalid Parameters!");
            return 0;
        }
        Random rd = new Random();
        int index = rd.nextInt(end - start) + start;
        int temp = data[index];
        data[index] = data[end];
        data[end] = temp;
        int small = start - 1;  // 特别注意
        for (index = start; index < end; ++index) {
            if (data[index] < data[end]) {
                ++small;
                if (small != index) {
                    temp = data[small];
                    data[small] = data[index];
                    data[index] = temp;
                }
            }
        }
        ++small;
        temp = data[small];
        data[small] = data[end];
        data[end] = temp;

        return small;
    }

    public static int partition2(int[] data, int start, int end) {
        if (data.length == 0 || start < 0 || end >= data.length) {
            System.out.println("Invalid Parameters!");
            return 0;
        }
        Random rd = new Random();
        int index = rd.nextInt(end - start) + start;
        int temp = data[index], value = data[index];
        data[index] = data[start];
        data[start] = temp;
        index = start;
        while(start < end) {
            while (start < end && data[start] <= data[index]) {
                ++start;
            }
            while (start < end && data[end] > data[index]) {
                --end;
            }
            temp = data[start];
            data[start] = data[end];
            data[end] = temp;
        }
        if (data[start] <= value) {  // 这里的符号是小于还是小于等于应该都行，因为如果等于的话，这个值放在当前位置还是紧贴着当前位置左面都一样
            temp = data[start];
            data[start] = data[index];
            data[index] = temp;
            return start;
        } else {
            temp = data[start - 1];
            data[start - 1] = data[index];
            data[index] = temp;
            return start - 1;
        }
    }

    public static void quicksort(int[] data, int start, int end) {
        if (start >= end) {
            return;
        }
        int index = partition1(data, start, end);
//        int index = partition2(data, start, end);

        if (index > start) {
            quicksort(data, start, index - 1);
        }
        if (index < end) {
            quicksort(data, index + 1, end);
        }
    }
}
```

快速排序的两种思路实现：

- partition1：选定数组中的一个值作为标准，随后将其移动到队尾，然后从头开始进行比较，处理大于该标准的数，将小于的数换到左边（注意替换次数，不是无脑换，是发现一个数大了就一直走，走到当前都大的这一串数的最后一个时才交换，减少交换次数）
- partition2：选定数组中的一个值作为标准，将其移动到头部，随后设置一个头和一个尾指针向中间探测，发现左边的大了，右边的小了就交换左右指针，到最后指针会重合，然后判断下最后一个值大于还是小于标准，进行交换并确定返回的下标位置。

**==上面的这两种方法的思路和代码必须熟记背诵==**

选定数组中的一个值作为标准可以不用随机，直接取第一个或者最后一个就ok的。

快速排序虽然总体的平均效率是最好的，但并非任何时候都是最优的算法。如果数组本身已经排好序，而每一轮排序的时候都以最后一个数字作为比较的标准，此时快速排序的效率只有$O(n^2)$

 ### 特殊问题：对公司所有员工的年龄排序

这个问题其实本身限制要求给的很多了所以才能够实现到时间效率为O(n)。首先默认这个年龄的范围实在一个正常有限的范围内的，其次允许使用辅助空间，因为员工的总人数非常多，所以这里假设员工的年龄在0~99之间，那么就可以用一个长度为100的数组把每个年龄有多少人给统计下来，然后填到原本的数组里面（相当于便向完成了一次排序）。这个肯定是要有特殊限制条件或者给一些特殊性质才有可能做到时间效率达到$O(n)$完成的，这里实际上也就是用到了一个用空间换取时间的思想？

只需要先扫一遍年龄然后统计到记录岁数人数的数组里面，随后再扫一遍把年龄填到数组里面就行了（注意这里的第二个数组虽然是二重循环，但是保证每个位置只会写一次，所以是O(n)），整个相当于扫了两次大的数组。填的时候注意是从小往大填的（或者从大往小填的，排序顺序嘛）所以直接就对年龄从小到达遍历作为外层循环，内层循环的下标表示记录年龄数量数组的下标。（每个位置填且仅填一次）

### 面试题11：旋转数组的最小数字

这个首先是要想到用二分加速，其次就是要把每种边界情况都要考虑到。

一开始看这个题目的时候反应就是顺序往后去找，然后看到第一个往下降的那就找到了。

这个方法有两个问题，一个是速度，还有一个是特殊情况的判断（本身就是递增序列，移动0个元素）

如果只是用循环去实现，那么时间复杂度就是$O(n)$级别的，但是通过观察可以发现，这个数组里面是部分有序的。所以对于看到有序的数组或者部分有序的数组，必须要马上和二分联系到一起。

然后就是这个二分怎么找，可以观察到对于非特殊情况，都是数组的头部大于数组的尾部。因此可以通过对中间数值进行判断确定其归属与前半部分数组还是后半部分数组。如果中间值大于头部，那肯定归属于前半部分数组，最小值一定在后面，如果中间值小于尾部，那么最小值就在左边（**或者就是当前这个**）。**最后左右两个指针合到一起，右边的指针就会指向最小值**。

特殊情况：

- 最左和最右和中间相等，这个时候就只能通过顺序查找确定了（如果中间不和最左最右相等那么是还有机会判断出来所在位置的）
- 本身就是递增数组（即，移动0个元素），这个时候第一个值就是最小值

综合上面的所有情况就能得到最终的结果。

特别注意实现中，indexMid的初始化，将其初始化为index1可以确保算法本身能够满足自身是递增数组的情况。

本题实现（2022/07/02）：

不建议看自己的这个实现，写得太恶心了

```java
class Solution {
    public int minArray(int[] numbers) {
        if (numbers[0] < numbers[numbers.length - 1] || numbers.length == 1) {  // 特判数组长度为1的特殊情况
            return numbers[0];
        } else {
            int start = 0, end = numbers.length - 1;
            while (start <= end) {
                int middle = end - (end - start) / 2;
                if (end - start == 1) {
                    return numbers[end];
                }
                if (numbers[start] == numbers[end] && numbers[start] == numbers[middle]) {
                    int min = numbers[start];
                    for (int i = start + 1; i <= end; i++) {
                        if (numbers[i] < min) {
                            min = numbers[i];
                        } 
                    }
                    return min;
                }
                if (numbers[middle] >= numbers[start]) {  // 严格单调的才能用middle+1或者middle-1
                    start = middle;
                } else if (numbers[middle] <= numbers[end]) {
                    end = middle;
                }
            }
            return -1;
        }
    }
}
// 这个是按书上思路写得
class Solution {
    public int minArray(int[] numbers) {
        if (numbers.length == 1)  // 书上那个处理不了只有一个元素的情况，只能特判
            return numbers[0];
        int start = 0, end = numbers.length - 1, middle = start;
        while (numbers[start] >= numbers[end]) {
            if (end - start == 1) {  // 主要就是这里想不到
                middle = end;
                break;
            }
            middle = end - (end - start) / 2;
            if (numbers[start] == numbers[end] && numbers[start] == numbers[middle]) {
                int min = numbers[start];
                for (int i = start + 1; i <= end; i++) {
                    if (numbers[i] < min) {
                        min = numbers[i];
                    }
                }
                return min;
            }
            if (numbers[middle] >= numbers[start]) {
                start = middle;
            } else if (numbers[middle] <= numbers[end]) {
                end = middle;
            }
        }
        return numbers[middle];
    }
}
```

## 2.4.3 回溯法

回溯法非常适合由多个步骤组成的问题，并且每个步骤都有多个选项。

### 面试题12：矩阵中的路径（回溯法判断存在）

```java
import java.util.*;

public class Main {

    public static void main(String[] args) {
        // ----- 矩阵中的路径 -----
        int[][] matrix = {{'a', 'b', 't', 'g'}, {'c', 'f', 'c', 's'}, {'j', 'd', 'e', 'h'}};
        int height = matrix.length, width = matrix[0].length;
        int[][] visited = new int[height][width];
        String str = "abfca";
        int index = 0;
        for (int i = 0; i < height; i++) {
            for (int j = 0; j < width; j++) {
                if (pathSearch(matrix, i, j, height, width, str, index, visited)) {
                    System.out.println("true");
                    return;
                }
            }
        }
        System.out.println("false");
        return ;
        // ----- 矩阵中的路径 -----
    }

    public static boolean pathSearch(int[][] matrix, int i, int j, int height, int width, String str, int index, int[][] visited) {
        if (i < 0 || i >= height || j < 0 || j >= width) {
            return false;
        }
        boolean hasPath = false;
        if (matrix[i][j] == str.charAt(index) && visited[i][j] == 0) {
            if (index == str.length() - 1) {
                return true;
            }
            visited[i][j] = 1;
            hasPath = pathSearch(matrix, i - 1, j, height, width, str, index + 1, visited) || pathSearch(matrix, i, j - 1, height, width, str, index + 1, visited) || pathSearch(matrix, i + 1, j, height, width, str, index + 1, visited) || pathSearch(matrix, i, j + 1, height, width, str, index + 1, visited);
            if (!hasPath) {
                visited[i][j] = 0;
            }
        }
        return hasPath;
    }
}
```

上面的代码是自己实现的，大概测了一下没啥问题，但是不保证边界全对，书上的情况1和2都测了（另外缺输入的边界判断处理），还是主要参考书上的代码吧。

这道题目其实算是暴力吧（回溯法似乎都是暴力？）主要就是要抓题目的限制条件，只能走上下左右，不允许走重复。

然后还有一个算是一开始读题不仔细没理解好，这个并没有要求一定要从矩阵的边界上开始走，就是说字符串是从矩阵中间开始的也是ok的。

理清楚了思路之后这个实现其实应该还是蛮简单的，这里应该还是写少了，特别不熟练。

- 输入内容的异常处理：矩阵给空的，矩阵的边长异常，字符串给空的。这些其实如果是一个函数里面的话，为了严谨都是需要去做判断预处理的
- 构建访问矩阵：因为要求不能重复访问同一个节点，为了在递归的过程中能够确定之前是否已经访问过了当前这个节点，所以需要一个访问矩阵来记录当前的已访问状态，这个访问矩阵其实开一个boolean就可以了，上面这个自己写的代码用了int，这个其实就是浪费空间了。
- 随后需要有一个标志位用来记录当前匹配到了第几个字符。这里其实在实现的时候还考虑过直接用一个栈进行模拟，其实应该也是ok的不过可能比用一个下标表示更占空间？
- 然后就是循环调用了，由于没有限制要从矩阵的哪个位置开始，所以需要一个二重循环把矩阵的所有节点分别当作起始点进行尝试。（这里其实要注意一点，实现判断的函数要和主函数充分分离，一开始在写的时候，在主函数里面直接就去判断第一位是否有效了，这个虽然实现上也是一种可以的方法，但是并不友好。理想的方式是书上以及上面自己代码的实现方式，主函数直接得到结果，把(当前位的)判断直接放在判断函数里面）。
- 函数内部的调用需要注意边界和返回情况。首先是边界，因为在判断函数进入下层调用之前并不先对边界的情况进行判断，因此在调用函数里面的第一个就是要进行边界的判断，如果边界超出了有效范围那么就返回false（找不到路径）。然后就是如果当前的匹配字符是最后一位，并且能够匹配上，还是没有访问过的，那么就是整个字符串都匹配好了，返回true。如果字符匹配上并且还没访问过但是不是最后一位则需要继续探索。（这里不会出现index超出str边界的情况，因为index都是+1+1增长的，一定会先经过等于最后一个字符，也就是成功返回true的情况，因此不会越界。）在继续探索之前需要将当前的访问位置更新，然后进入相邻的四个方向的递归调用。如果四个递归都失败，那么需要删除进入到当前节点的状态然后进行回退，因此需要根据情况还原访问状态。

整个题目其实思路还算比较好弄明白，主要是代码上面的实现细节和处理。跟上面的快排一样记忆背诵吧，这种回溯法的题目如果认出题型写不对估计是要被气得吐血的。

本题实现（2022/07/08）：

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        boolean[][] visit = new boolean[board.length][board[0].length];
        int index = 0;
        for (int x = 0; x < board.length; x++) {  // 没有限制从哪里开始
            for (int y = 0; y < board[0].length; y++) {
                if (existCore(board, visit, word, x, y, index)) {
                    return true;
                }
            }
        }
        return false;
    }

    public boolean existCore(char[][] board, boolean[][] visit, String word, int x, int y, int index) {
        if (x < 0 || x >= board.length || y < 0 || y >= board[0].length) {
            return false;
        }
        if (word.charAt(index) == board[x][y] && !visit[x][y]) {
            visit[x][y] = true;
            if (index == word.length() - 1) {
                return true;
            } else {
                boolean ans = existCore(board, visit, word, x-1, y, index + 1) || existCore(board, visit, word, x+1, y, index + 1) || existCore(board, visit, word, x, y-1, index + 1) || existCore(board, visit, word, x, y+1, index + 1);
                visit[x][y] = false;  // 状态要回退，虽然其实只有false才需要回退
                return ans;
            }
        } else {
            return false;
        }
    }
}
```

大体上的思想上是没啥问题的，但是细节有很多地方要注意。状态的回退，每个点都可以作为起始。

还有一种更巧妙的实现方式，不需要额外的访问数组记录访问状态。（原地标志位）

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        char[] words = word.toCharArray();
        for(int i = 0; i < board.length; i++) {
            for(int j = 0; j < board[0].length; j++) {
                if(dfs(board, words, i, j, 0)) return true;
            }
        }
        return false;
    }
    boolean dfs(char[][] board, char[] word, int i, int j, int k) {
        if(i >= board.length || i < 0 || j >= board[0].length || j < 0 || board[i][j] != word[k]) return false;
        if(k == word.length - 1) return true;
        board[i][j] = '\0';  // 因为确定了只能是大小写英文字母，所以完全可以把这个位置填成别的东西充当访问标志
        boolean res = dfs(board, word, i + 1, j, k + 1) || dfs(board, word, i - 1, j, k + 1) || 
                      dfs(board, word, i, j + 1, k + 1) || dfs(board, word, i , j - 1, k + 1);
        board[i][j] = word[k];  // 用word中的内容进行还原，因为前面已经确定了是当前位置和word里的字符对应的上才会到这
        return res;
    }
}
```

### 面试题13：机器人的运动范围（回溯法统计数量）

这道题目和上面那道其实完全一样的，实现方式也基本相同。

还是需要一个访问数组的，因为需要确保统计数量的时候不会重复，当然也可以直接用一个int数组直接记次数判断也ok？不过应该是没必要，用boolean的判断下是否访问，然后用一个单独的int变量统计就行了。

不过书上代码和一开始想的有一点不同的是，书上是直接返回数量，而我一开始的想法是用一个另外的变量把统计数量一层层传，应该还是书上的代码会更好一点。

书上这种实现方式其实需要对递归的返回值有比较好的理解才能保证不出错。这里要确定，当前点的数量是从这个点发散出去能走的点的数量加上当前这个点的和。因为整个统计点的数量像是一个树一样，一层一层往上，最后全部归到(0,0)这个点。

**面试题12和面试题13可以算是回溯法的两个典型例子，两种不同的类别，一种是判断存在与否，另外一种是统计数量。对于不同问题的不同实现方式需要记忆理解。尤其是统计数量的，需要理解思想是在返回的时候带回从当前位置出发的统计结果，这样才是比较简便不会出错的实现方式。**

回溯法对坐标和路径的区别，是否存在路径的话需要对访问状态进行还原，而坐标的统计则限制死了不能重复，因此访问状态不存在还原。

本题实现（2022/07/08）：

```java
class Solution {
    public int movingCount(int m, int n, int k) {
        boolean[][] visit = new boolean[m][n];
        int ans = movingCountCore(m,n,k,visit,0,0);
        return ans;
    }

    public int movingCountCore(int m, int n, int k, boolean[][] visit, int x, int y) {
        if (x < 0 || x >= m || y < 0 || y >= n || visit[x][y]) {
            return 0;
        }
        int currentSum = 0, x1 = x, y1 = y;
        while (x1 != 0) {
            currentSum += x1 % 10;
            x1 /= 10;
        }
        while (y1 != 0) {
            currentSum += y1 % 10;
            y1 /= 10;
        }
        if (currentSum > k) {
            return 0;
        } else {
            visit[x][y] = true;
            return 1 + movingCountCore(m,n,k,visit,x-1,y) + movingCountCore(m,n,k,visit,x+1,y) + movingCountCore(m,n,k,visit,x,y-1) + movingCountCore(m,n,k,visit,x,y+1);
        }
    }
}
```

另外一种使用了一些技巧完成的写法：主要是对数位和以及搜索的方向上进行了改进，可参考[此题解](https://leetcode.cn/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/solution/mian-shi-ti-13-ji-qi-ren-de-yun-dong-fan-wei-dfs-b/)

```java
class Solution {
    int m, n, k;
    boolean[][] visited;
    public int movingCount(int m, int n, int k) {
        this.m = m; this.n = n; this.k = k;
        this.visited = new boolean[m][n];
        return dfs(0, 0, 0, 0);
    }
    public int dfs(int i, int j, int si, int sj) {
        if(i >= m || j >= n || k < si + sj || visited[i][j]) return 0;
        visited[i][j] = true;
        return 1 + dfs(i + 1, j, (i + 1) % 10 != 0 ? si + 1 : si - 8, sj) + dfs(i, j + 1, si, (j + 1) % 10 != 0 ? sj + 1 : sj - 8);  // 其实只需要往右和往下走两种情况就能保证遍历到所有的位置
    }
}
```

只向右和向下走就能保证遍历到所有的位置，这个可能不是太好想到并证明。**数位和那个还是有一点用的，稍微记一下**。

## 2.4.4 动态规划与贪婪算法

如果面试题是求一个问题的最优解（通常是求最大值或者最小值），而且该问题能够分解成若干个子问题，并且子问题之间还有更小的子问题，就可以考虑用动态规划来解决这个问题。

在应用动态规划之前要分析能否把大问题分解成小问题，分解后的每个小问题也存在最优解。如果把小问题的最优解组合起来能够得到整个问题的最优解，那么就可以应用动态规划解决这个问题。

**==应用动态规划求解的问题的四个特点==**：

1. 求一个问题的最优解
2. 整体问题的最优解是依赖各个子问题的最优解
3. 把大问题分解成若干个小问题，这些小问题之间还有互相重叠的更小的子问题
4. 从上往下分析问题，从下往上求解问题

目前反正对这四个特点的理解还不是很透彻，如果之后发现了有更好的可以总结使用动态规划求解问题的标志的话可以补充在这里。

### 面试题14：剪绳子

这道题目感觉书上写的不是很好，直接参考网上leetcode的题解吧。

![image-20220528225545320](../images/LeetCode的菜鸡实录.assets/image-20220528225545320.png)

![image-20220528225604462](../images/LeetCode的菜鸡实录.assets/image-20220528225604462.png)

```c++
class Solution {
public:
    /**
     * 1. 确定dp数组下标含义 分拆数字i，可以得到的最大乘积为dp[i];
     * 2. 递推公式 dp[i] = max(dp[i], (i - j) * j, dp[i - j] * j);
     * 3. 初始化 dp[2] = 1;
     * 4. 遍历顺序 从前向后遍历就可以;
     * 5. 推导结果;
     */
    int integerBreak(int n) {
        /* 定义dp数组 */
        vector<int> dp(n + 1);
        /* dp数组初始化 */
        dp[2] = 1;
        /* 从前向后遍历 */
        for (int i = 3; i <= n ; i++) {
            /* j遍历到小于i的值 */
            for (int j = 1; j < i - 1; j++) {
                dp[i] = max(dp[i], max((i - j) * j, dp[i - j] * j));
            }
        }
        return dp[n];
    }
};
```

内层循环还可以继续优化，变成循环到<=i/2

![image-20220528235409306](../images/LeetCode的菜鸡实录.assets/image-20220528235409306.png)

优化之后的题解：

```java
class Solution {
    public int cuttingRope(int n) {
        int[] dp = new int[n + 1];
        dp[2] = 1;
        for(int i = 3; i < n + 1; i++){
            //优化处理！
            for(int j = 1; j <= i / 2; j++){
                dp[i] = Math.max(dp[i], Math.max(j * (i - j), j * dp[i - j]));
            }
        }
        return dp[n];
    }
}
```

dp[i]长度为i的绳子剪成至少两段的最大乘积，因为长度为i，所以分成两段，一段的范围肯定在1~i-1之间，所以内层循环的j的范围是从1~i-1。

两个max，里面的max表示两种可能的情况，即直接分成两段，或者是后半段还需要分成更多段。这两种情况共同构成了分成两段或更多的最大乘积。外层的max是为了确保在所有的分割方式里面取最好的一个作为长度为i的结果，是为了确定j从1~i-1这个范围里面哪个的dp[i]最大。

**动态规划方程：dp[i] = max(dp[i], max(j * (i-j), j * dp[i-j]))**

最大的问题还是要看出怎么分解子问题然后把动态规划方程写出来。

补充：关于书上那几个products初始化的理解，因为在后面判断的时候已经保证了至少会剪一刀，左移这里初始化的products就是剪或者不剪所有情况里面的最大值，那么对于0~3肯定就都是本身了。

贪婪算法：

当n>=5时，尽可能多地剪长度为3的绳子，当剩下的绳子长度为4时，把绳子剪成两段长度为2的绳子。绳子长度为0或1时，最大乘积为0；绳子长度为2时，最大乘积为1；绳子长度为3时，最大长度为2；绳子长度为4时，最大长度为4。具体证明见书上吧。

另外leetcode题解里面有一个贪心详细证明的。

![image-20220529004757417](../images/LeetCode的菜鸡实录.assets/image-20220529004757417.png)

本题实现（2022/07/08）：leetcode——[剑指 Offer 14- I. 剪绳子](https://leetcode.cn/problems/jian-sheng-zi-lcof/)

```java
class Solution {
    public int cuttingRope(int n) {
        int[] dp = new int[n + 1];  // dp[i]表示把长为i的绳子分成至少两段的最大乘积
        dp[1] = 1;
        dp[2] = 1;
        for (int i = 3; i <= n; i++) {
            for (int j = 1; j <= i/2; j++) {  // a * dp[b] >= b * dp[a]; 当b > a时
                /* 
                长为i的身子分成至少两段可以拆分为两种情况：
                1. 绳子分为两段，两段乘在一起得到乘积
                2. 绳子分为超过两段，可以拆分成第一段和后面的至少两段；把后面的至少两段乘积和第一段乘一起得到乘积
                */
                dp[i] = Math.max(Math.max(j*dp[i-j],j*(i-j)),dp[i]);  // 这里要注意，由于是循环确定怎么剪第一刀最划算，所以dp[i]应该取循环过程中最大的
            }
        }
        return dp[n];
    }
}
```

贪心解法：

```java
class Solution {
    public int cuttingRope(int n) {
        // 下面的贪心的情况没法处理2和3，要单独解决
        if (n == 2) {
            return 1;
        }
        if (n == 3) {
            return 2;
        }
        /*
        贪心说到底就是分成每段为3越多越好，根据余数为0,1,2进行三种处理
        1. 为0正好就不动了
        2. 为1这时候3*1<2*2，拆一个补成2*2
        3. 为2时直接乘2，再拆两个1不划算
        */
        int last = n % 3;
        if (last == 0) {
            return (int)Math.pow(3,n/3);
        } else if (last == 1) {
            return (int)Math.pow(3, n/3-1) * 4;
        } else {
            return (int)Math.pow(3, n/3) * 2;
        }
    }
}
```

写起来简单，难在证明和发现贪心的规律。

leetcode上的第二问，含取模问题：

本题实现（2022/07/08）：

```java
class Solution {
    public int cuttingRope(int n) {
        if (n == 2)
            return 1;
        if (n == 3)
            return 2;
        int mod = n % 3;
        int times = n / 3;
        if (mod == 0) {
            long ans = 1;
            while (times != 0) {
                ans *= 3;
                if (ans >= 1000000007) {
                    ans %= 1000000007;
                }
                times--;
            }
            return (int)ans;
        } else if (mod == 1) {
            long ans = 1;
            times--;
            while (times != 0) {
                ans *= 3;
                if (ans >= 1000000007) {
                    ans %= 1000000007;
                }
                times--;
            }
            return (int)((ans * 4) % 1000000007);
        } else {
            long ans = 1;
            while (times != 0) {
                ans *= 3;
                if (ans >= 1000000007) {
                    ans %= 1000000007;
                }
                times--;
            }
            return (int)((ans * 2) % 1000000007);
        }
    }
}
```

自己写的这个就没考虑快速幂了，暴力乘，然后用的long，不会超范围。

看了题解之后写的快速幂：

```java
class Solution {
    public int cuttingRope(int n) {
        if (n == 2)
            return 1;
        if (n == 3)
            return 2;
        int mod = n % 3;
        int times = n / 3;
        long ans = 1;
        if (mod == 0) {
            ans = quickMulMod(3,times);
            return (int)ans;
        } else if (mod == 1) {
            ans = quickMulMod(3,times-1);
            return (int)((ans * 4) % 1000000007);
        } else {
            ans = quickMulMod(3,times);
            return (int)((ans * 2) % 1000000007);
        }
    }

    // 这个快速幂似乎各有各的写法，反正我是背了这种
    public long quickMulMod(long base, int times) {  // 调用的使用传的base是个int，进来因为形参设置了long，所以隐式地做了强制类型转换？  
        long ans = 1;  // 下面ans和base都要累乘，两个都得是long
        while(times != 0) {
            if ((times & 1) == 1) {
                ans *= base;
                ans %= 1000000007;
            }
            base *= base;  // base不管当前位上是不是1都要累乘；
            base %= 1000000007;
            times >>= 1;
        }
        return ans;
    }
}
```

## 2.4.5 位运算

右移时处理最左边位的情形：如果数字是一个无符号数值，则用0填补最左边的n位；如果数字是一个有符号数值，则用数字的符号位填补最左边的n位（主要是负值的情况，最左边补的都是1，如果是正值补0）。

### 面试题15：二进制中1的个数

一开始看到这道题目就当成是直接用位移运算处理的，不过如果直接使用位运算进行处理的话会存在问题。主要的原因是由于整数正负所导致的，正值进行右移时，最左边的位会补0，但是负值进行右移时，最左边的位会补1，这就会导致循环的判断条件失效从而导致死循环。**==负值右移最左边位补1==**（java全是有符号数，只需要区别“>>”有符号右移和“>>>”无符号右移即可，“>>>”不论正负都在高位插0）

因此为了解决这个问题，可以考虑不对数进行右移，而将标志位进行左移作比较。由于修改为了对标志进行左移进行判断，所以结束循环的条件由标志位确定。因为整数的存在在计算机中是有限的位数，因此当左移到相应位数的次数时，最终会还原为0（1挪到最左边的外面去了就是结束的时候，完成了对整数每一位的判断）

代码如下：

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int count = 0;
        int flag = 1;
        while(flag != 0) {
            if ((n & flag) != 0) {
                count ++;
            }
            flag = flag << 1;
        }
        return count;
    }
}
```

但是对于上述的思路仍然可以进一步优化。如果对一个数进行减1，那么最右侧的1位及其右侧所有的位都会获得取反的结果，如果将原数值和减1的数值做位与运算，那么最右侧的1位及其右侧的位都会被置0。因此原本的数中有几个1，就需要进行几次这种运算将数值变为0。这里要特别注意，**减一运算的这个性质对于负值也是符合的**。（==这个-1与原值做与运算可以消尾的性质是通用的，不区分正负==）

```java
// 输出int型整数的二进制，以字符串类型输出
System.out.print(Integer.toBinaryString(-3));
```

使用该优化策略的代码如下：

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int count = 0;
        while(n != 0) {
            ++ count;
            n = (n - 1) & n;
        }
        return count;
    }
}
```

总结：位移运算的时候需要注意运算数值的正负，对于负值进行右移的时候需要特别注意左边位补1而非0，所以如果是要对数进行位移判0作为结束循环的条件，需要万分注意。此外对 * = or / = 2的次方的运算都考虑是否可以用右移运算进行替代，右移运算与 / = 运算等效，左移运算在无溢出且不影响标志位的情况下与 * = 运算等效，但是从速度效率上来说位移运算远优于乘除法。**==数值右移与标志位左移在一定程度上是等价的。==**

本题实现（2022/07/12）：

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int count = 0, sign = 1;
        while (sign != 0) {
            if ((sign & n) != 0) {  // 因为是左移所以只能判是不是0
                count += 1;
            }
            sign = sign<<1;
        }
        return count;
    }
}
```

知道正负会让右移产生影响的问题的话，实现还是没啥问题的。但是更进一步的解法还是没想到。

更优解法，借助-1性质，这个背吧...

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int count = 0;
        while(n != 0) {
            ++ count;
            n = (n - 1) & n;
        }
        return count;
    }
}
```

**==举一反三：把一个整数减去1之后再和原来的整数做位与运算，得到的结果相当于把整数的二进制表示中最右边的1变成0。很多二进制的问题都可以用这种思路解决。==**

相关题目：

- 用一条语句判断一个整数是不是2的整数次方。如果一个整数是2的整数次方，那么它的二进制表示中有且只有一位是1，而其他所有位都是0。可以应用“举一反三”的性质，把这个整数减去1之后再和它自己做与运算，如果此时结果为0，那么该整数即为2的整数次方。（2的整数次方不涉及零和负数的问题）
- 输入两个整数m和n，计算需要改变m的二进制表示中的多少位才能得到n。将m和n做异或，随后统计异或结果中1的位数。

## 3.2 代码的规范性

书写、布局和命名都决定着代码的规范性。

## 3.3 代码的完整性

从**==功能测试==**、**==边界测试==**和**==负面测试==**（考虑各种可能的错误输入）三个方面设计测试用例，以确保代码的完整性。

3种错误处理的方法：

1. 用返回值告知调用者是否出错
2. 当错误发生时设置一个全局变量
3. 异常：程序正常运行的代码块（try模块）和处理异常的代码块（catch模块）。C语言不支持异常。在抛出异常的时候，程序的执行会打乱正常的顺序，对程序的性能有很大的影响。

![1654178837937](../images/LeetCode的菜鸡实录.assets/1654178837937.jpg)

### 面试题16：数值的整数次方

题面来看的话其实不是一个特别难的问题，但是如果只是当成一个简单的乘法循环来做就凉了。首先需要考虑到的是，这里提到的是整数次，并没有说是正数，也就是说指数为零和负数的情况都需要在程序中进行考虑。另外这里还涉及到一个快速幂的做法，即提升运算效率。最后一个题目提了不考虑大数问题，但是如果需要处理大数问题是否有相关解析。

按照指数为正、零和**负**可以先将问题分为三种情况。为正则正常运算即可，如果为负，那么需要先取绝对值之后正常运算，再在最后取倒数；此外还需要考虑如果**底数为0**，因为这样的话分母为0运算出错。如果为0我们可以知道，除非底数为0，否则运算结果均为1。当指数为0且底数为0时，运算在数学上没有意义。

综上，整体的算法流程是：先判断底数为0且指数小于等于0的异常情况，随后判断指数为负则取绝对值，运算正数次方，结束后若指数为负则取倒数，如果指数为正则直接返回。其中运算正数次方可以使用快速幂的方式（在leetcode70爬楼梯的问题里面有一个矩阵快速幂的实现，数值快速的幂的实现与之相似，当然书上的那种实现也是ok的）。

在快速幂的细节上还有两个点需要注意：一个是**用>>1（右移运算）代替除以2**；另一个是**用&（位与）运算判断当前位是否为1**，而不用求余运算做判断。这两个技巧在处理被看作二进制的问题时是通用的。

```java
		public static double fastpow(double base, int exponent) {  // 快速幂
        double result = 1.0;
        if (exponent == 0) {
            return result;
        }
        while (exponent != 0) {
            if ((exponent & 1) == 1) {
                result *= base;
                exponent = exponent >> 1;
                base *= base;
            }
        }
        return result;
    }

    public static double customPow(double base, int exponent) {
        if (base == 0.0 && exponent <= 0) {
            // 抛出异常，非法值无效运算
            return 0.0;
        }
        boolean positive = true;
        if (exponent < 0) {
            positive = false;
            exponent *= -1;
        }
        double result = fastpow(base, exponent);
        if (!positive) {
            result = 1.0 / result;
        }
        return result;
    }
```

本题实现（2022/07/12）：

```java
class Solution {
    public double myPow(double x, int n) {
        if (x == 0 && n != 0)  // 特殊情况判断需要同时考虑底数和指数
            return 0;
        if (x != 0 && n == 0)  // 特殊情况判断需要同时考虑底数和指数
            return 1;
        double ans;
        long m = (long)n;  // 给的范围是能-2^31的，如果还用int，在负max的时候就会越界；这里不用强转类型
        if (m < 0) {
            ans = powPositive(x, -m);
            if (ans == 0) {
                return -1;
            }
            ans = 1 / ans;   
        } else {
            ans = powPositive(x, m);
        }
        return ans;
    }
    
    public double powPositive(double x, long n) {
        double ans = 1;
        while (n != 0) {
            if ((n & 1) != 0) {
                ans = ans * x;
            }
            x = x * x;
            n >>= 1;
        }
        return ans;
    }
}
```

题解里面的简洁写法：

```java
class Solution {
    public double myPow(double x, int n) {
        if(x == 0) return 0;
        long b = n;  // 类型向上不需要强转，类型向下需要强转
        double res = 1.0;
        if(b < 0) {
            x = 1 / x;
            b = -b;
        }
        while(b > 0) {
            if((b & 1) == 1) res *= x;
            x *= x;
            b >>= 1;
        }
        return res;
    }
}
```

### 面试题17：打印从1到最大的n位数

这道题目的考点其实就在于大数问题的处理。

**==大数问题的处理思路：使用字符串或者数组表达大数。==**

因为使用字符串表达数字了，所以对于最终循环的终止条件和打印数字都需要使用特殊的函数完成功能。

对于循环的终止条件，一开始我考虑的时候觉得就是只要判断最前面的一位，也就是index=0的时候是不是从9变到0就行了。确实大体上是走这样的一个思路，但是在实际实现的时候怎么才能确定是从9变到0，肯定是因为后面的一位向前进位了，那么为了确保不会出现诸如99...0+1出现进位的情况，整个问题还是需要从最低位开始，一位一位向上进位，最终循环到最高位（也就是index=0）判断是否进位，如果进位则循环终止，已经到达了最大的n位数。

另一个对于大数的打印则是需要对最高位上面为0的内容进行忽略，即从index=0开始向后，在第一个位置上不为0的开始进行输出。因为在存储的时候就没有使用倒序（即index=0表示最高位），因此在打印的时候也就只需要从前向后打就可以了。

此外，书上还提到了另一种实现的方式即通过递归，这个可能思路和实现知道就可以了，具体来说的话感觉是不是可能会碰到递归栈溢出的情况？递归的基本思路就是从前向后设置，每次都是从0到9，然后在递归的内部判断是否到达了最高位，如果达到了最高位则调用打印函数用于输出结果。（因为每一位上都是严格按照从0到9的顺序进行的，从最高位到最低位递归，所以最终在输出结果的时候保证了是从小到大有序的）这个递归的代码其实还有个漏洞，前面的使用循环的实现方法一开始把数组全部设置为0，在Increment函数中会现在最低位做nSum++，这样可以保证是从1开始而不是从0开始的，但是这个递归的代码就没考虑这个问题了，打印出来的应该是从0开始的。

扩展：使用char型字符表示十进制数字浪费空间，如何进一步改进。[bitmap算法](https://blog.csdn.net/kongmin_123/article/details/82222023)，BCD编码，压缩BCD编码。这里面bitmap的应用大概是每一位十进值数字用十个bit表示？（[参考博客](https://blog.csdn.net/weixin_41042404/article/details/86760688)，没看懂，内容不确定对）

**如果面试题是关于n位的正数并且没有限定n的取值范围，或者输入任意大小的正数，那么这道题目很可能是需要考虑大数问题的。字符串是一种简单、有效地表示大数的方法**

本题抄写：（2022/07/13）

```java
class Solution {
    int[] res;
    int nine = 0, count = 0, start, n;
    char[] num, loop = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'};
    public int[] printNumbers(int n) {
        this.n = n;
        res = new int[(int)Math.pow(10, n) - 1];
        num = new char[n];
        start = n - 1;
        dfs(0);
        return res;
    }
    void dfs(int x) {
        if(x == n) {
            String s = String.valueOf(num).substring(start);
            if(!s.equals("0")) res[count++] = Integer.parseInt(s);  // Integer.parseInt(String s)
            if(n - start == nine) start--;
            return;
        }
        for(char i : loop) {
            if(i == '9') nine++;
            num[x] = i;
            dfs(x + 1);
        }
        nine--;
    }
}
```

凉吧凉吧....

Integer.parseInt() 这个方法可以记一下，传进去一个String，吐出来一个int

**[相关题目](https://blog.csdn.net/tttzzztttzzz/article/details/86841237)：**定义一个函数，在该函数中可以实现任意两个整数的加法。

这个问题需要考虑的有，两个数字的正负，最终结果的正负，输出负数结果，如何对一正一负做减法。

基本的逻辑就是先对两个数判断正负，如果都正或者都负，那么后面的运算就是加法，只需要注意最前面标志位符号的正负就可以了。如果两个数一个正一个负，那么就需要先把两个数都变成正，然后比较大小，用大的减小的得到结果，然后加上符号。其中实现里面减法的处理可以注意一下，本质上应该是一样的。（这里要注意，因为已经对两个数绝对值的大小进行了比较和判断，因此不会出现借不到位这种情况，如果不在进行计算之前先比较两个数绝对值的大小就进行减法就可能会出现减法之后向前借位拿不到的问题）

### 面试题18：删除链表的节点

#### 题目一：在$O(1)$时间内删除链表节点

这个问题要求在时间复杂度$O(1)$中完成这个任务，因此常规的从头节点开始遍历然后找到要删除节点并连接前后节点的方式无法实现。要在$O(1)$的时间复杂度下完成这个任务需要新的思路。

由于题目已经给出了待删除节点的指针，**==因此可以将删除该节点的任务等效的修改为将当前节点的值修改为其下一节点的值并删除下一个节点==**。这样就能够在$O(1)$的时间复杂度内完成这个任务。

在知道了这个思路之后就是对于几种边界情况的处理。首先是如果删除的节点没有后续节点（即待删除节点为当前列表的尾节点），这种情况就只能顺序遍历列表然后找到待删除节点，并将其上一节点指向null了；第二种是如果删除的链表中只有这一个节点，那么需要将链表的头指针设置为null。此外，这个问题由于需要在$O(1)$的时间复杂度里面完成，因此无法对链表中是否存在该节点进行校验（需要时间复杂度为$O(n)$），因此需要由调用者保证提供节点指针的正确性

#### 题目二：删除链表中重复的节点（已排序）

这道题目思路其实没啥新的，主要是实现的细节上面。连续的多个都要一次性删掉。如果删到尾巴和如果头就要删。

本题实现（2022/07/13）：

leetcode上面的题目题面和书上要求不一样

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        ListNode ans = head;
        if (head.val == val) {
            ans = head.next;
            return ans;
        }
        ListNode last = head;
        head = head.next;
        while (head != null) {
            if (head.val == val) {
                last.next = head.next;
                return ans;
            }
            last = head;
            head = head.next;
        }
        return ans;
    }
}
// 题解代码
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        if(head.val == val) return head.next;
        ListNode pre = head, cur = head.next;
        while(cur != null && cur.val != val) {
            pre = cur;
            cur = cur.next;
        }
        if(cur != null) pre.next = cur.next;
        return head;
    }
}
```

**==实现代码里面的细节很重要（自己写一遍）==**

### 面试题19：正则表达式匹配

正则表达式的问题都可以用有限状态机进行处理。其中最重要的无非就是判断 . 和 \* 这两个符号。最主要就是要处理 \*，因为 \* 是跟在别的字符后面的，所以相当于要提前往前进行探测。

本质上是始终在根据第一位或者前两位（带\*）来匹配。具体见书P125,P126

本题实现（2022/07/13）：

这个自己写的变成到处补窟窿

还是看题解比较好，这个书上的其实写的也是递归，而且还是用的c++，跟java差别蛮大的。题解里面用动态规划做的思路比较好（但不会，摆）

![image-20220714000903760](../images/LeetCode的菜鸡实录.assets/image-20220714000903760.png)

```java
class Solution {
    public boolean isMatch(String A, String B) {
        int n = A.length();
        int m = B.length();
        boolean[][] f = new boolean[n + 1][m + 1];

        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= m; j++) {
                //分成空正则和非空正则两种
                if (j == 0) {
                    f[i][j] = i == 0;
                } else {
                    //非空正则分为两种情况 * 和 非*
                    if (B.charAt(j - 1) != '*') {
                        if (i > 0 && (A.charAt(i - 1) == B.charAt(j - 1) || B.charAt(j - 1) == '.')) {
                            f[i][j] = f[i - 1][j - 1];
                        }
                    } else {
                        //碰到 * 了，分为看和不看两种情况
                        //不看
                        if (j >= 2) {
                            f[i][j] |= f[i][j - 2];
                        }
                        //看
                        if (i >= 1 && j >= 2 && (A.charAt(i - 1) == B.charAt(j - 2) || B.charAt(j - 2) == '.')) {
                            f[i][j] |= f[i - 1][j];
                        }
                    }
                }
            }
        }
        return f[n][m];
    }
}
```

### 面试题20：表示数值的字符

这道题目其实也可以用正则表达式去判断数是否合规，所以算是上面一道题目的衍生吧。

数值的表示最关键的问题在于如何拆解，对于数值可以大致分为整数部分，小数部分和指数部分。其中整数部分和指数部分可正可负，而小数部分不能包含正负号。

$A[.[b]][e|EC]$或$.B[e|EC]$，其中$A$为数值的整数部分，$B$紧跟着小数点为数值的小数部分，$C$紧跟着'e'或者'E'为数值的指数部分。

代码参考书上（最好二刷自己写一遍）

**面试题19和面试题20算是完全相同类型的两道题目，可以放到一起进行复习（2022/07/15：咋说呢，如果按递归来做可能比较类似，但是如果上面那个用动态规划还是不大一样的。主要像是因为都可以用状态机来理清思路）**

本题实现（2022/07/15）：

感天动地，除了一个空字符串漏了，别的居然没啥问题

```java
class Solution {
    public boolean isNumber(String s) {
        s = s.trim();  // 去除字符串前后的全部空格
        if (s.length() == 0) {
            return false;
        }
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == 'e' || s.charAt(i) == 'E') {
                if (i >= 1 && i != s.length() - 1)
                    return (isZheng(s.substring(0, i)) || isXiao(s.substring(0,i))) && isZheng(s.substring(i+1));
                else
                    return false;
            }
        }
        return isZheng(s) || isXiao(s);
    }
    public boolean isZheng(String s) {  // 判断是不是一个整数
        if (s.charAt(0) == '+' || s.charAt(0) == '-') {
            return isShu(s.substring(1));
        }
        return isShu(s);

    }
    public boolean isXiao(String s) {  // 判断是不是一个小数
        int i = 0;
        if (s.charAt(i) == '+' || s.charAt(i) == '-') {
            if (s.length() == 1) {
                return false;
            }
            i++;
        }
        int temp = i;
        for (; i < s.length(); i++) {
            if (s.charAt(i) == '.') {
                if (temp == i) {
                    return isShu(s.substring(i + 1));
                } else if (i == s.length() - 1) {
                    return isShu(s.substring(temp, i));
                } else {
                    return isShu(s.substring(temp,i)) && isShu(s.substring(i + 1, s.length()));
                }
            }
        }
        return false;
    }
    public boolean isShu(String s) {  // 判断是不是至少一位的数字
        if (s.length() < 1) {
            return false;
        }
        for (int i = 0; i < s.length(); i++) {
            if ((int)s.charAt(i) < (int)'0' || (int)s.charAt(i) > (int)'9') {  // ASCII码比较
                return false;
            }
        }
        return true;
    }
}
```

一个是还是要注意异常情况的判断，java主要是空字符串，没有空指针的问题。

String类的substring注意下标是从第一个数字前0开始递增的。

leetcode上面都是用状态机做的，感觉不是特别好理解，还是书上的分成段进行处理比较方便。

### 面试题21：调整数组顺序使奇数位于偶数前面

这道题目其实思路还是蛮容易想到的——双指针/类似快排

判断奇偶使用位运算代替mod（加速）

基础的实现非常简单，没啥好说的，主要是这里提到的关于扩展性的考虑。相当于把判断函数当成一个参数传入，然后进行调用，这样对于最外侧的实现大的功能的函数就不需要过多修改，只需要直接修改决定判断条件的传入函数就可以了。（这个应该算是工程上的一种优化，从算法上来说前面那种双指针能做出来就可以了）

本题实现（2022/07/15）：

```java
class Solution {
    public int[] exchange(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            while (left < nums.length && (nums[left] & 1) == 1) {  // 前边必须要有边界条件限制，不然全奇直接越界
                left ++;
            }
            while (right >= 0 && (nums[right] & 1) == 0) {
                right --;
            }
            if (left < right) {
                nums[left] = nums[left] + nums[right];
                nums[right] = nums[left] - nums[right];
                nums[left] = nums[left] - nums[right];
            }
        }
        return nums;
    }
}
// 题解
class Solution {
    public int[] exchange(int[] nums) {
        int i = 0, j = nums.length - 1, tmp;
        while(i < j) {
            while(i < j && (nums[i] & 1) == 1) i++;  // 用这个边界限制条件比用边界更好
            while(i < j && (nums[j] & 1) == 0) j--;
            tmp = nums[i];
            nums[i] = nums[j];
            nums[j] = tmp;
        }
        return nums;
    }
}
```

这道题目其实一开始想的时候居然联想到partition函数上去了，虽然好像也有点道理，不过其实没那么烦。

**==另外就是，把解题的思路顺序要整理下，这样方便看到新的问题时的思考。==**

## 3.4 代码的鲁棒性

在面试中，最简单也是最使用的防御性编程就是在函数入口添加代码以验证用户输入是否符合要求。

指针——空指针

字符串——字符串内容为空

### 面试题22：链表中倒数第k个节点

这道题目的思路其实之前看到过所以是知道的，就是用两个始终等距的指针一起走。其中走在前面的那个指针领先走在后面的那个指针  k-1步，这样就可以保证当前面的指针到达队尾是，后面的指针正好指在倒数第k个节点上。通过这种思路可以保证只使用一次遍历就能够得到结果，而如果要先求取链表的长度再计算倒数第k个节点是从前向后数的第几个节点，就需要用两次循环完成。

思路上没有问题之后主要就是程序的鲁棒性和异常的判断。什么情况下会导致出现异常，链表中不足k个节点怎么办？

另外还有一个需要注意的就是int和unsigned int，这个应该是c++里面的，但是java后面也要注意是否存在这种问题（java不存在unsigned这种问题，所有的数都是带符号位的）。对于unsigned int进行-1的操作，得到的会是一个极大正值，因为越界（这个问题在c++stl的笔记中，关于.size()函数的返回结果有描述，返回值是一个unsigned int而不是int，这里对于常用的数据结果的返回类型必须明确）。

**相关题目：求链表的中间节点。如果链表中的节点总数为奇数，则返回中间节点；如果节点总数是偶数，则返回中间两个节点的任意一个。**

这个其实也是一道快慢指针的题目。可以设置一个指针一次走一步，一个指针一次走两步，这样当快指针到链表末尾时，慢指针正好在链表中间。（**==快慢指针的应用==**）

**==举一反三：当我们用一个指针遍历链表不能解决问题的时候，可以尝试用两个指针来遍历链表。可以让其中一个指针遍历的速度快一些（比如一次在链表上走两步），或者让它先在链表上走若干步。==**

本题实现（2022/07/16）：

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode fast = head;
        for (int i = 0; i < k - 1; i++) {
            if (fast.next != null) {
                fast = fast.next;
            } else {
                return null;
            }
        }
        // 一种是先走k-1，然后两个一起走，最后前面的那个在尾部停；另外一种是先走k，然后两个一起走，最后前面的出去了停
        while (fast.next != null) {  // 最后走的时候不是fast超出去结束，是fast在最后一个，那么后面那个正好在倒数第k个上
            head = head.next;
            fast = fast.next;
        }
        return head;
    }
}
```

这题思路是见过的，没啥。主要是异常和边界的判断。

### 面试题23：链表中环的入口节点

这道题目算是“判断链表中是否有环”的升级版，即不仅要知道有没有环，还要找到环的入口节点。

可以把这个问题拆分为两步来解决：

1. 判断链表中是否包含环
2. 找到环的入口

判断链表中是否存在环由快慢指针完成，快指针每次走两步，慢指针一次走一步，如果快指针走到了null，那么就说明链表中无环，如果快慢指针指向了同一个节点，那么链表中就存在环。

在确定链表中有环之后，为了找到环的入口，需要知道环的长度为多少。在快慢指针重合时，指针指向的节点一定位于环中，因此只需要从这个节点开始并计数，当再次回到这个节点时，就可以得到环中节点的数量了。

这里其实还有一个巧妙的方法可以找到入口，但是这个方法可能走的距离会比在环里面一圈要更长。

![fig1](../images/LeetCode的菜鸡实录.assets/jianzhi_II_022_fig1.png)

![image-20220604201533273](../images/LeetCode的菜鸡实录.assets/image-20220604201533273.png)

![image-20220604201550329](../images/LeetCode的菜鸡实录.assets/image-20220604201550329.png)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode slow = head, fast = head;
        while (fast != null) {
            slow = slow.next;
            if (fast.next != null) {  // 避免出现到了null指向next的情况（快慢指针走两步的写法）
                fast = fast.next.next;
            } else {
                return null;
            }
            if (fast == slow) {
                ListNode ptr = head;
                while (ptr != slow) {  // 主要是这里的优化实现，当然如果不用这个优化也ok，直接记录之后绕一圈计数
                    ptr = ptr.next;
                    slow = slow.next;
                }
                return ptr;
            }
        }
        return null;
    }
}
```

书上代码的思路更常规，不需要特别多的数学推理（但是第一段判断是否有环的部分感觉还是leetcode上面的这个更加清晰）

### 面试题24：反转链表

这个题目主要就是考指针的操作严谨性。如果只考虑两个节点的话，会因为指针重新指向的操作导致断链，因此需要三个指针分别表示当前节点、前一节点和下一节点。

反转链表的头节点是原本链表的尾节点，尾节点的判断条件是指向下一个的指针为null。

具体实现代码看书上吧，主要是逻辑要清楚，**链子连得是中间节点和左边**。

测试情况：

- 输入的链表头指针是nullptr
- 输入的链表只有一个节点
- 输入的链表有多个节点

本题扩展：[递归实现](https://blog.csdn.net/weixin_42817333/article/details/124864581)；别的没啥问题，就是初始的第一步，prev指针是null要注意下

本题实现（2022/07/17）：

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode preNode = null;
        ListNode curNode = head;
        ListNode nextNode = head.next;  // 书上的写法是进到while里面，一开始就拿nextNode，这样可以避免最后null.next的情况（另外如果写在循环里面，外面就不用单独留一步，这样可以不用特判空链表）
        while (nextNode != null) {
            curNode.next = preNode;
            preNode = curNode;
            curNode = nextNode;
            nextNode = nextNode.next;
        }
        curNode.next = preNode;
        return curNode;
    }
}
// 改良版
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode preNode = null;
        ListNode curNode = head;
        ListNode ansNode = null;
        while (curNode != null) {
            ListNode nextNode = curNode.next;  // 放到里面取就不会有null.next的问题
            if (nextNode == null) {  // 到了末尾，拿到要返回的新的链表头节点
                ansNode = curNode;
            }
            curNode.next = preNode;  // 到了末尾不过处理还是要做的
            preNode = curNode;
            curNode = nextNode;
        }
        return ansNode;
    }
}
```

别的没啥问题，就是链表为空还是要注意下。（主要还是看写法是否合适，之前那种取next的方式就要处理空链表，如果把next放到里面获取就可以不用单独处理null）

### 面试题25：合并两个排序的链表

实现思路来说的话是ok的，主要问题在于实现上的一些细节。

首先一开始确实想到了是对两个链表的头节点分别比对，但是并没有一开始就考虑递归。

实现代码看书上P147吧，蛮简单的，二刷可以再写下。

本题实现（2022/07/18）：

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode head = null;
        ListNode ans = null;
        while (l1 != null && l2 != null) {
            if (l2.val < l1.val) {
                if (head == null) {
                    head = l2;
                    l2 = l2.next;
                    ans = head;
                } else {
                    head.next = l2;
                    l2 = l2.next;
                    head = head.next;
                }
            } else {
                if (head == null) {
                    head = l1;
                    l1 = l1.next;
                    ans = head;
                } else {
                    head.next = l1;
                    l1 = l1.next;
                    head = head.next;
                }
            }
        }
        while (l1 != null) {
            if (head == null) {
                head = l1;
                l1 = l1.next;
                ans = head;
            } else {
                head.next = l1;
                l1 = l1.next;
                head = head.next;
            }
        }
        while (l2 != null) {
            if (head == null) {
                head = l2;
                l2 = l2.next;
                ans = head;
            } else {
                head.next = l2;
                l2 = l2.next;
                head = head.next;
            }
        }
        return ans;
    }
}
// 可以参考书上，用递归写
```

题解写法：多建一个节点，换来代码的无限精简和美好

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dum = new ListNode(0), cur = dum;  // 多一个dum节点，用来避免头节点的特判，不存具体内容，返回next
        while(l1 != null && l2 != null) {
            if(l1.val < l2.val) {
                cur.next = l1;
                l1 = l1.next;
            }
            else {
                cur.next = l2;
                l2 = l2.next;
            }
            cur = cur.next;
        }
        cur.next = l1 != null ? l1 : l2;
        return dum.next;
    }
}
```

### 面试题26：树的子结构

这道题目大体上的思路是没问题的，只是实现上还是缺乏一些意识。原本设想的解法里面还是没有考虑到递归，但是实际上递归是一种非常简便的解决方法。一开始想的其实就是根左右的先序遍历顺序在树里面找子结构的根节点，然后再对下面的遍历去找。但是这样一个是要涉及到顺序什么的问题，还有一个是子结构并不一定就是那个节点的完整子树，所以也会有问题。而要解决这个问题实际上依赖递归是非常简单的。**==递归的思想==**

使用递归的思想解决这个问题的话，就只需要考虑当前节点这一个情况是否匹配就可以了。

递归实现思路：

首先是在树中找子结构的根节点，这个也是用递归进行实现，按照根左右的顺序在树中搜索。如果找到了根节点是匹配的，那么就进入对子结构内部的判断。对于子结构内部的判断可以从当前节点判断再做递归完成，即判断当前节点是否匹配，然后看左子树是否匹配子结构的左节点，右子树是否匹配子结构的右节点。

这里面要主要对于树为空的判断，如果子结构为空是可以接受的，因为这代表子结构匹配完，应该返回匹配成功，但如果是本身树的那个对应节点是null了那就出错了，说明树里面并不包含这个子结构，所以匹配失败。

**[==对于两个double类型数值的相等判断：==](https://www.freesion.com/article/6612686809/)**

1. 转换为字符串（引用类.toString()），使用string的equals方法判断
2. 使用Double.doubleToLongBits（==）
3. 对两个数值进行相减，绝对值小于很小的值（比如0.0000001）；Math.abs(d1-d2) < dis

本题实现（2022/07/18）：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        if (A == null || B == null) {
            return false;
        }
        /*
        // 这下面因为是后考虑的有相同值的节点，所以冗余了
        if (A.val == B.val) {
            return compare(A,B) || isSubStructure(A.left, B) || isSubStructure(A.right, B);
        }
        return isSubStructure(A.left, B) || isSubStructure(A.right, B);
        */
        return compare(A,B) || isSubStructure(A.left, B) || isSubStructure(A.right, B);
    }
    public boolean compare(TreeNode A, TreeNode B) {
        if (B == null) {
            return true;
        }
        if (A == null) {
            return false;
        }
        if (A.val != B.val) {
            return false;
        }
        return compare(A.left, B.left) && compare(A.right, B.right);
    }
}
```

这个错了一次是因为没考虑有相同值的点，还是要尽量考虑进去。

题解写法：

```java
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        return (A != null && B != null) && (recur(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B));
    }
    boolean recur(TreeNode A, TreeNode B) {
        if(B == null) return true;
        if(A == null || A.val != B.val) return false;
        return recur(A.left, B.left) && recur(A.right, B.right);
    }
}
```

基本是一样的，主要就是自己写法一开始没考虑相同值节点，后补的，然后写的有冗余。去掉之后一样的。

## 4.2 画图让抽象问题形象化

### 面试题27：二叉树的镜像

这道题目其实理解清楚运算的规则之后是比较好实现的。对于镜像的处理完全可以通过每个节点上交换左右子树即可完成，这可以通过递归简单的实现。

在实现中，由于使用递归，递归终止的判断条件需要确定，当左右节点都为空时，即走到了叶节点，这时就是递归终点。此外在每次进行递归的时候，由于递归内部会直接使用左右指针，因此在当前递归进入下一层之前需要先对当前节点的左右子节点是否存在进行判断，如果不存在则不需要继续进入。此外为了避免异常的情况，需要在递归中加入对当前节点是否为空的判断，这个判断主要是为了避免在输入中造成的问题。在递归中这个情况应该是不会出现的，因为在进入更深层的递归之前，都会对节点进行判断。

本题实现（2022/08/08）：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        TreeNode ans = new TreeNode(root.val);
        ans.left = mirrorTree(root.right);
        ans.right = mirrorTree(root.left);
        return ans;
    }
}
```

下面这个是另一种写法，算是原位操作；整个逻辑是先把下面的镜像处理掉，然后再挂到上面的结点上，这样就不会产生混淆了。

```java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        TreeNode left = mirrorTree(root.left);
        TreeNode right = mirrorTree(root.right);
        root.left = right;
        root.right = left;
        return root;
    }
}
```

### [面试题28：对称的二叉树](https://leetcode.cn/problems/dui-cheng-de-er-cha-shu-lcof/solution/dui-cheng-de-er-cha-shu-by-leetcode-solu-rgks/)

这道题目最好还是参考leetcode上面的题解，理解的更加清楚一点。书上的做法虽然也ok，但解释上还是题解更清晰。

首先这个问题想到就应该是递归，因为对两棵二叉树的判断可以最简化为两个节点的判断和属于节点下的子结构的判断。以这个思路可以很简单地完成递归判断的代码。其中对于递归的判断条件要特别注意。

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return check(root, root);
    }

    public boolean check(TreeNode p, TreeNode q) {
        if (p == null && q == null) {  // 都空了说明匹配，当前的递归可以返回匹配成功
            return true;
        }
        if (p == null || q == null) {  // 只有一个为空，另一个还有子结构，说明匹配失败
            return false;
        }
        // 当前节点的值相等，并且相应的子结构镜像匹配
        return p.val == q.val && check(p.left, q.right) && check(p.right, q.left);
    }
}
```

使用上面这种递归的实现方式来说，只要在递归的条件判断处没有问题，那么判断的代码是很容易实现的，也不需要考虑部分节点缺少子结构可能造成的遍历结果顺序的问题。书上的改进前的做法其实可能是有问题的，因为在某种特定的排列方式下是否有可能使得不同的两棵树具有相同的顺序（需要中序+前序或者中序+后序才能确定唯一的一棵二叉树）。书上改进后的做法相当于是把二叉树每个节点的左右都补齐（可能理解不完全对，好像有些节点没有左右子节点也补了），反正就是应该以某种方式保证了这个顺序的遍历结果是唯一的。

既然可以用递归的方式来解决这个问题，那么肯定也可以通过迭代来完成。

「方法一」中我们用递归的方法实现了对称性的判断，那么如何用迭代的方法实现呢？首先我们引入一个队列，这是把递归程序改写成迭代程序的常用方法。初始化时我们把根节点入队两次。每次提取两个结点并比较它们的值（队列中每两个连续的结点应该是相等的，而且它们的子树互为镜像），然后将两个结点的左右子结点按相反的顺序插入队列中。当队列为空时，或者我们检测到树不对称（即从队列中取出两个不相等的连续结点）时，该算法结束。

这个实现的思路本质上是跟递归一样的，只是通过队列吧递归的实现方式用队列使用迭代完成了。代码如下：

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return check(root, root);
    }

    public boolean check(TreeNode u, TreeNode v) {
        Queue<TreeNode> q = new LinkedList<TreeNode>();
        q.offer(u);
        q.offer(v);
        while (!q.isEmpty()) {
            u = q.poll();
            v = q.poll();
            if (u == null && v == null) {  // 因为下面入队的时候无脑.left和.right，因此叶子节点的左右子节点的null也会被插到队列中
                continue;
            }
            if ((u == null || v == null) || (u.val != v.val)) {
                return false;
            }

            q.offer(u.left);
            q.offer(v.right);

            q.offer(u.right);
            q.offer(v.left);
        }
        return true;
    }
}
```

最主要的还是这个递归的思路和递归内的判断条件，这个问题其实一开始是考虑到递归的，但是并没有一下子想到把两个放到一起，而是分别去做，比如先把一棵树变成镜像再判断，这种肯定就是多此一举了。**==递归的思想和实现==**

本题实现（2022/08/08）：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) {
            return true;
        }
        return symCore(root.left, root.right);
    }

    public boolean symCore(TreeNode node1, TreeNode node2) {
        if (node1 == null && node2 == null) {
            return true;
        }
        if (node1 == null || node2 == null) {
            return false;
        }
        if (node1.val != node2.val) {
            return false;
        }
        return symCore(node1.left, node2.right) && symCore(node1.right, node2.left);
    }
}
```

解法二：辅助栈

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return symCore(root, root);
    }

    public boolean symCore(TreeNode node1, TreeNode node2) {
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(node1);
        queue.offer(node2);
        while (!queue.isEmpty()) {
            node1 = queue.poll();
            node2 = queue.poll();
            if (node1 == null && node2 == null) {
                continue;  // 区别于递归，这里两个null并不代表到最后了，只有队列中为空才算结束
            }
            if (node1 == null || node2 == null || node1.val != node2.val) {
                return false;
            }
            queue.offer(node1.left);
            queue.offer(node2.right);
            queue.offer(node1.right);
            queue.offer(node2.left);
        }
        return true;
    }
}
```

### 面试题29：顺时针打印矩阵

这道题目主要是一个逻辑和对不同情况条件进行处理的任务，如果把一个问题拆分成小的问题，然后保证对不同的情况都可以处理。

回忆了一下，这似乎还是道老题？做过好几次了应该

```java
/*
int[][] matrix = new int[4][4];
int temp = 1;
for (int i = 0; i < 4; i++) {
    for (int j = 0; j < 4; j++) {
        matrix[i][j] = temp++;
    }
}
*/
    public static void printMatrix(int[][] matrix) {
        int height = matrix.length, width = matrix[0].length;
        int temp = 0;
        while(height > 2 * temp && width > 2 * temp) {
            printOnce(matrix, temp);
            temp++;
        }
    }

    public static void printOnce(int[][] matrix, int temp) {
        int height = matrix.length, width = matrix[0].length;
        for (int i = temp; i < width - temp; i++) {
            System.out.print(matrix[temp][i]);
            System.out.println();
        }
        if (height - temp > temp) {
            for (int i = temp + 1; i < height - temp; i++) {
                System.out.print(matrix[i][width - temp - 1]);
                System.out.println();
            }
        }
        if (width - temp - 1 > temp && height - temp > temp) {
            for (int i = width - temp - 2; i >= temp ; i--) {
                System.out.print(matrix[height - temp - 1][i]);
                System.out.println();
            }
        }
        if (height - temp - 2 > temp && width - temp - 1 > temp) {
            for (int i = height - temp - 2; i >= temp + 1 ; i--) {
                System.out.print(matrix[i][temp]);
                System.out.println();
            }
        }
    }
```

**后面打印一圈几个循环的判断主要是后两个，必需还要同时满足上面的条件。因为整个的打印一圈的过程严格遵循“右”、“下”、“左”、“上”。如果已经没有“下”了，那么“左”肯定也是不存在的。因此后面的判断条件里面必需考虑进去。**

然后就是总体函数的里面，由于每次都是一整圈，所以打印当前圈的起始点，横纵坐标一定是一样的，然后必定不会超过一半（下标和行、列数，一个从0开始一个从1开始，反正保证有这个规律），然后就可以通过打印当前圈的左上角的坐标点的坐标进入当前的循环了。

本题实现（2022/08/08）：

下面这个是纯模拟。主要是已经知道了转的顺序，一定是从左到右->从上到下->从右到左->从下到上，满足前一个条件才可能走后一个。另外，这种方法还用到了一个性质，就是一圈一圈到里面的过程中，(i,i)这个点不管这个圈长什么样子，只要还在边长一半的范围里面，那就一定是存在的。

```java
class Solution {
    int[] ans;
    int count = 0;

    public int[] spiralOrder(int[][] matrix) {
        if (matrix.length == 0) {
            return new int[0];
        }
        if (matrix[0].length == 0) {
            return new int[0];
        }
        int row = matrix.length;
        int col = matrix[0].length;
        ans = new int[row*col];
        int limit = Math.min(row,col);
        for (int i = 0; i <= (limit-1)/2; i++) {  // 有效(i,i)的范围判断
            printMatrix(i,matrix,row,col);
        }
        return ans;
    }

    public void printMatrix(int index, int[][] matrix, int row, int col) {
        for (int i = index; i <= col - index - 1; i++) {
            ans[count++] = matrix[index][i];
        }
        if (index <= col - index - 1) {
            for (int i = index + 1; i <= row - index - 1; i++) {
                ans[count++] = matrix[i][col - index - 1];
            }
        }
        if (index <= col - index - 1 && index + 1 <= row - index - 1) {
            for (int i = col - index - 2; i >= index; i--) {
                ans[count++] = matrix[row - index - 1][i];
            }
        }
        if (index + 1 <= row - index - 1 && index <= col - index - 2) {
            for (int i = row - index - 2; i >= index + 1; i--) {
                ans[count++] = matrix[i][index];
            }
        }
    }
}
```

另一种思路，就是不用(i,i)作为锚点，直接一圈一圈从里面收。

```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        if(matrix.length == 0) return new int[0];
        int l = 0, r = matrix[0].length - 1, t = 0, b = matrix.length - 1, x = 0;
        int[] res = new int[(r + 1) * (b + 1)];
        while(true) {
            for(int i = l; i <= r; i++) res[x++] = matrix[t][i]; // left to right.
            if(++t > b) break;
            for(int i = t; i <= b; i++) res[x++] = matrix[i][r]; // top to bottom.
            if(l > --r) break;
            for(int i = r; i >= l; i--) res[x++] = matrix[b][i]; // right to left.
            if(t > --b) break;
            for(int i = b; i >= t; i--) res[x++] = matrix[i][l]; // bottom to top.
            if(++l > r) break;
        }
        return res;
    }
}
```

## 4.3 举例让抽象问题具体化

### 面试题30：包含min函数的栈

这道题目其实一开始以为是很简单的，就加一个在进入的时候判断最小的变量就可以了，但是实际上没有考虑到这个栈的出栈问题，如果没有额外的记录当前的最小值一旦出栈就gg了，所以需要一个辅助栈。

这里之所以用的是栈是因为同样的形式去进行入栈和出栈操作。在处理的时候，如果当前入栈的值小于或者等于栈中的最小值（辅助栈的栈顶），那么就进行入栈。之所以对等于也要入栈是因为如果有两个相等的最小值，如果只在辅助栈中入栈一个，那么在另一个元素出栈时可能会出现错误。

如果想要让入栈和出栈的操作对于获取最小值更为方便，那么可以在每次入栈的时候都插入一个值，如果值不比最小值小，那么就再入栈一个最小值，如果比当前最小值更小则入栈新的最小值。这个主要是在出栈的时候不需要再对出栈的内容和辅助栈的栈顶作匹配，多存一些内容，但是操作更为方便。

代码直接参照书上就行，主要还是要**通过模拟这个步骤发现最小数值出栈的这个问题**。

本题实现（2022/08/09）：

```java
class MinStack {

    Stack<Integer> stack;
    Stack<Integer> minStack;

    /** initialize your data structure here. */
    public MinStack() {
        stack = new Stack<>();
        minStack = new Stack<>();
    }
    
    public void push(int x) {
        stack.push(x);
        if (minStack.isEmpty()) {
            minStack.push(x);
        } else {
            minStack.push(Math.min(x,minStack.peek()));
        }
    }
    
    public void pop() {
        stack.pop();
        minStack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int min() {
        return minStack.peek();
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(x);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.min();
 */
```

这个就是最简单的，辅助栈按照正常的栈一样去插入，保证插入的是当前的最小值，那么出栈的时候也出一个就行了。另外一种是题解里面的思路，就是插入的时候要做判断的，只有更小的才会插，这样的坏处就是在出栈的时候要做判断，看什么时候出栈。

官方题解：

```java
class MinStack {
    Stack<Integer> A, B;
    public MinStack() {
        A = new Stack<>();
        B = new Stack<>();
    }
    public void push(int x) {
        A.add(x);
        if(B.empty() || B.peek() >= x)  // 小细节，等号的时候也入栈，不然相等的有两个在后面判断的时候就直接全没了
            B.add(x);
    }
    public void pop() {
        if(A.pop().equals(B.peek()))  // 出栈的时候要判断；必须要用equals，不能==，因为-127~128有Integer Cache，超过之后就是比较对象是否相等了
            B.pop();
    }
    public int top() {
        return A.peek();
    }
    public int min() {
        return B.peek();
    }
}
```

### 面试题31：栈的压入、弹出序列

这道题就是一个栈的模拟。通过用出栈序列不停地匹配栈顶元素，如果到最后出栈序列匹配完不为空，则不是该压栈序列的弹出序列。

这道题目其实也是一个发现规律的题，因为之前碰到过所以知道，但是其实这种题目都自己画图或者写一下流程模拟下应该可以自己找到。

具体写法参照书上的代码，这道题实现起来其实也需要注意下技巧。

代码（leetcode上的版本）：

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Stack<Integer> stack = new Stack<>();
        int i = 0;
        for(int num : pushed) {
            stack.push(num); // num 入栈；因为在上一次已经循环到不能出栈了，所以直接入栈就行
            while(!stack.isEmpty() && stack.peek() == popped[i]) { // 循环判断与出栈
                stack.pop();
                i++;
            }
        }
        return stack.isEmpty();
    }
}
```

下面这段代码更加节省空间，不用新建一个stack，而是直接在pushed上面模拟：

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        int i = 0, j = 0;
        for (int e : pushed) {  // 栈最多和pushed数组同步塞满
            pushed[i] = e;  // 把pushed数组当成stack来用，如果入栈过程中没出栈，那么pushed数组和栈一样，如果有入栈，则栈比pushed数组小，把pushed数组右边的内容填到左边，用i模拟栈顶
            while (i >= 0 && pushed[i] == popped[j]) {  // 如果匹配上了则出栈（i--），然后匹配下一个（j++）
                j++;
                i--;
            }
            i++;
        }
        return i == 0;  // 最终栈为空，全匹配完了
    }
}
```

除了思路知道之外，下面这种不需要额外空间的写法也要理解记忆，因为如果面试的时候有提到关于空间使用更少的要求的话，需要用到本身数组的空间去模拟这个栈。**==栈可以用数组模拟，用一个指针模拟栈头指针即可。==**

本题实现（2022/08/11）：

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Stack<Integer> stack = new Stack<>();
        int j = 0;
        for(int i: pushed) {  // 迭代器
            stack.push(i);
            while(!stack.isEmpty() && stack.peek() == popped[j]) {
                stack.pop();
                j++;
            }
        }
        return j == popped.length;  // 直接判断返回就行
    }
}
```

思路就是模拟，莘开栈辅助空间的话还是很方便写的。当然里面有些地方的判断和写法有些小技巧。

第二种实现，原地处理，直接在pushed上处理，不用额外的辅助栈。因为栈这个东西本身也是可以用数组模拟的。

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        int i = -1, j = 0;
        for(int k = 0; k < pushed.length; k++) {
            pushed[++i] = pushed[k];
            while (i >= 0 && pushed[i] == popped[j]) {
                j++;
                i--;
            }
        }
        return j == popped.length;
    }
}
```

### 面试题32：从上到下打印二叉树

#### 题目一：不分行从上到下打印二叉树

这道题其实就是要求对二叉树的层序遍历，因为知道了所以其实就直接用队列去套一下就行了。这种题目主要就是两点：一个是知道整个操作的规律（这个可以通过自己画几个例子尝试总结）；另外一个就是得知道相关的数据结构。

代码没啥特别的，看书上吧，注意实现里面的异常情况条件判断

**本题扩展**：**如何广度优先遍历一幅有向图**？同样也可以**基于队列实现**。**==树是图的一种特殊退化形式==**，从上到下按层遍历二叉树，从本质上来说，就是广度优先遍历二叉树。

==**举一反三**：不管是广度优先遍历一幅有向图还是一棵树，都要用到队列。==

本题实现（2022/08/11）：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int[] levelOrder(TreeNode root) {
        if (root == null){
            return new int[0];
        }
        Queue<TreeNode> queue = new LinkedList<>();
        ArrayList<Integer> ansList = new ArrayList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            TreeNode temp = queue.poll();
            ansList.add(temp.val);
            if (temp.left != null) {
                queue.offer(temp.left);
            }
            if (temp.right != null) {
                queue.offer(temp.right);
            }
        }
        int[] ans = new int[ansList.size()];
        for (int i = 0; i < ansList.size(); i++) {
            ans[i] = ansList.get(i);
        }
        return ans;
    }
}
```

这里的结果int数组因为一开始并不确定长度，所以只能用ArrayList这种。（如果还是用queue存然后转，可以实现，但是速度会慢很多，所以这种情况以后碰到还是就用ArrayList存了之后再开int空间返回）

#### 题目二：分行从上到下打印二叉树 

如果需要完成分行打印，那就需要在队列中添加额外的计数变量用以存储当前层剩余遍历的点的数量和下一层节点的数量。（一开始我以为下一层节点的数量可以不存，直接在当前层遍历完之后看队列内有多少节点就知道了，但是实际上栈的成员函数中并没有获取栈容量的方法，因此还是需要用一个变量自己存储）

代码里面主要是在入队的时候对nextLevel这个统计下一层节点数量的变量做++，然后在出队时对toBePrinted这个当前层剩余遍历的点的数量的变量做--。当出队到某一时刻，当前层剩余遍历的点的数量为0后，则将nextLevel赋值给toBePrinted，并将nextLevel的计数值重新设置为0。

本题实现（2022/08/11）：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        if (root == null) {
            return new ArrayList<List<Integer>>();
        }
        ArrayList<List<Integer>> ansList = new ArrayList<>();
        int levelNum = 0;
        int currentLevelNum = 1;
        queue.offer(root);
        ArrayList<Integer> tempList = new ArrayList<>();
        while(!queue.isEmpty()) {
            TreeNode temp = queue.poll();
            currentLevelNum--;
            tempList.add(temp.val);
            if (temp.left != null) {
                queue.offer(temp.left);
                levelNum++;
            }
            if (temp.right != null) {
                queue.offer(temp.right);
                levelNum++;
            }
            if (currentLevelNum == 0) {
                ansList.add(tempList);
                // tempList.clear();  // 这里要主要浅拷贝的问题，清空了ansList里面的也就空了
                tempList = new ArrayList<>();
                currentLevelNum = levelNum;
                levelNum = 0;
            }
        }
        return ansList;
    }
}
// 下面这个是模仿题解修改的代码
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        List<List<Integer>> ansList = new ArrayList<>();
        if (root != null) {
            queue.offer(root);
        }
        while(!queue.isEmpty()) {
            ArrayList<Integer> tempList = new ArrayList<>();
            for (int i = queue.size(); i > 0; i--) {  // 用这种方式实现分层写法不错
                TreeNode temp = queue.poll();
                tempList.add(temp.val);
                if (temp.left != null) {
                    queue.offer(temp.left);
                }    
                if (temp.right != null) {
                    queue.offer(temp.right);
                }
            }
            ansList.add(tempList);
        }
        return ansList;
    }
}
```

题解代码：

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        List<List<Integer>> res = new ArrayList<>();  // 注意写法
        if(root != null) queue.add(root);
        while(!queue.isEmpty()) {
            List<Integer> tmp = new ArrayList<>();
            for(int i = queue.size(); i > 0; i--) {  // 用一层遍历结束时的队列长度描述下一层数量
                TreeNode node = queue.poll();
                tmp.add(node.val);
                if(node.left != null) queue.add(node.left);
                if(node.right != null) queue.add(node.right);
            }
            res.add(tmp);
        }
        return res;
    }
}
```

上面的这种写法要理解。当前层遍历完之后的时刻队列内的节点数量就是下一层的数量。

#### 题目三：之字形打印二叉树

这道题目给出的是一种新的打印顺序。根据分析可以大致猜到是依赖于栈实现的遍历方式。但是仔细考虑会发现，**如果只使用一个栈进行操作，在一层的节点还未全部取出的时候，下一层的节点已经需要入栈，这就会导致遍历的顺序出现错误**，因此只使用一个栈是无法解决这个问题的。同时通过模拟可以发现，不同的层入栈的左右节点的顺序有所不同，比如在第三行，为了能从左往右，对于3的子节点的入栈顺序应该是先右后左（这样才能保证在出栈的时候是先左后右），但是对于第一行的节点1，它的子节点的遍历顺序是先左后右，只有先对右子节点入栈再对左子节点入栈才可以保证这个顺序。这个左右子节点的入栈顺序是和树的奇偶行相关的，因此可以用两个栈分别对奇数行和偶数行进行处理。由于已经使用了两个栈，因此不再需要计数变量。具体实现代码看书上。

本题实现（2022/08/11）：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Stack<TreeNode> stack1 = new Stack<>();
        Stack<TreeNode> stack2 = new Stack<>();
        List<List<Integer>> ansList = new ArrayList<>();
        int status = 1;
        if (root != null) {
            stack1.push(root);
        }
        while (!stack1.isEmpty() || !stack2.isEmpty()) {
            ArrayList<Integer> tempList = new ArrayList<>();
            if(status == 1) {
                for(int i = stack1.size(); i > 0; i--) {
                    TreeNode temp = stack1.pop();
                    tempList.add(temp.val);
                    if (temp.left != null) {
                        stack2.push(temp.left);
                    }
                    if (temp.right != null) {
                        stack2.push(temp.right);
                    }
                }
            } else {
                for(int i = stack2.size(); i > 0; i--) {
                    TreeNode temp = stack2.pop();
                    tempList.add(temp.val);
                    if (temp.right != null) {
                        stack1.push(temp.right);
                    }
                    if (temp.left != null) {
                        stack1.push(temp.left);
                    }
                }
            }
            ansList.add(tempList);
            status = (status == 1?2:1);
        }
        return ansList;
    }
}
```

上面这个可以实现，但是其实用别的数据结构可以不用开两个栈。

题解代码（通过双端队列的头插和尾插得到不同顺序的遍历结果）：

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        List<List<Integer>> res = new ArrayList<>();
        if(root != null) queue.add(root);
        while(!queue.isEmpty()) {
            LinkedList<Integer> tmp = new LinkedList<>();
            for(int i = queue.size(); i > 0; i--) {
                TreeNode node = queue.poll();
                if(res.size() % 2 == 0) tmp.addLast(node.val); // 偶数层 -> 队列头部
                else tmp.addFirst(node.val); // 奇数层 -> 队列尾部
                if(node.left != null) queue.add(node.left);
                if(node.right != null) queue.add(node.right);
            }
            res.add(tmp);
        }
        return res;
    }
}
```

还有一种题解的思路也不错，其实就是在特定的行要倒序一下，多一步倒序处理，别的按常规实现也ok。

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        List<List<Integer>> res = new ArrayList<>();
        if(root != null) queue.add(root);
        while(!queue.isEmpty()) {
            List<Integer> tmp = new ArrayList<>();
            for(int i = queue.size(); i > 0; i--) {
                TreeNode node = queue.poll();
                tmp.add(node.val);
                if(node.left != null) queue.add(node.left);
                if(node.right != null) queue.add(node.right);
            }
            if(res.size() % 2 == 1) Collections.reverse(tmp);  // 每层结束按奇偶判断是否进行倒序
            res.add(tmp);
        }
        return res;
    }
}
```

这两种题解实现的思路都很巧妙，只需要在原本实现的基础上做细微的修改就实现了不同的功能。

### 面试题33：二叉搜索树的后序遍历序列

这道题目其实观察之后已经能想到是一个递归了，因为二叉搜索树嘛，子结构也符合这个要求，所以相当于每次拿一个节点，然后分别对左右子节点去判断。通过观察序列可以发现，后序遍历序列的最后一个数即为根节点，而根节点之前的序列可以根据值大于根节点和值小于根节点被分为两段。对于根节点本身来说，如果其左子结构的值都小于自身（题目给定了互不相同），右子结构的值都大于自身，那么从当前节点的角度来说是合理的，至于左右子结构内部是否合理，可以交给递归到下一层时进行判断。这种思路其实可以通过扫描序列的方式完成，首先依次从头向后遍历小于根节点的值，当发现第一个大于根节点的值之后，即认为前面的都是左子结构，继续向后扫描的应该为右子结构。如果对于当前的节点是分配合理的，那么从第一个大于根节点的值向后扫描的过程中不会出现小于根节点的值（如果出现，说明从当前节点进行判断，不是二叉搜索树）。如果扫描到了倒数第二位都是全部满足的，那说明当前节点判断没问题，剩下的交给递归到下一层判断。实现的代码逻辑可以参考书上，大致思路应该没啥难的，实现的时候注意细节就行。

本题实现（2022/08/12）：

```java
class Solution {
    public boolean verifyPostorder(int[] postorder) {
        if (postorder.length == 0) {
            return true;
        }
        return verifyCore(postorder, 0, postorder.length - 1);
    }
    public boolean verifyCore(int[] postorder, int begin, int end) {
        if (end - begin <= 1) {
            return true;
        }
        int root = postorder[end], i = begin;
        for (; i < end; i++) {
            if (postorder[i] > postorder[end]) {
                break;
            }
        }
        for (int j = i; j < end; j++) {
            if (postorder[j] < postorder[end]) {
                return false;
            }
        }
        return verifyCore(postorder, begin, i-1) && verifyCore(postorder, i, end - 1);
    }
}
```

这个因为给定的条件是二叉搜索树了，所以当前节点的左右子树的节点分别满足小于该节点的值和大于该节点的值。然后又根据后序遍历的性质可以发现，最后一个值即为根节点的值。在最后一个值之前的数组可以按照根节点的值分为两部分。这就是一个最基本的单节点是否满足二叉搜索树性质的判断。至于左右子树接着递归下去就行了。这里要注意递归终止的条件。必须要把两种情况考虑进去，一种是子树中至少有一个节点，另一种是压根就没这棵子树。如果子树只有一个节点或者压根没这个子树，就直接返回true即可。

**相关题目**：输入一个整数数组，判断该数组是不是某二叉搜索树的前序遍历结果。

思路一样的，只不过前序遍历是通过序列的第一个值确定根节点，而后序遍历是用序列的最后一个值确定根节点。

**举一反三**：如果面试题要求**处理一棵二叉树的遍历序列**，则可以先**找到二叉树的根节点**，再基于根节点把整棵树的遍历序列**拆分成左子树对应的子序列和右子树对应的子序列**，接下来再**递归地处理**这两个子序列。

### 面试题34：二叉树中和为某一值的路径

这个其实算是一个深度优先搜索吧，在二叉树里面先序遍历就算是符合深度优先搜索。然后整个的数据结构使用的是栈，因为进入了下一层之后返回还是弹出刚进去的节点。判断可以写成一个递归的形式，每一个都将当前的节点加入总和中并把当前节点入栈。如果当前节点没有左子节点且没有右子节点，就说明它是一个叶子节点，也就是说一条路径走完了。这时候比较想要的路径值和目前路径上的值，如果相等则打印路径。之后如果当前节点的左子节点存在或者右子节点存在则继续递归。整个递归过程的最后需要将当前节点出栈，这样才能保证在返回上一层的时候，打印的路径没有问题。（求和的值不需要减回去，这是因为每次传递和的值的时候都是传的值而不是引用，因此相当于是当前递归中的一个局部变量，等到返回上一层的时候，求到的和还是本身的值，不会出错）。

找路径是一个常规的问题，虽然用栈进行操作的思路都知道，但是具体当中的细节，比如递归函数的终止处需要将当前节点出栈这些细节还是需要注意下的。

本题实现（2022/08/12）：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    List<List<Integer>> ansList;
    LinkedList<Integer> route;
    int routeSum = 0;

    public List<List<Integer>> pathSum(TreeNode root, int target) {
        ansList = new ArrayList<>();
        route = new LinkedList<>();
        if (root != null) {
            dfs(root, target);
        }
        return ansList;
    }

    public void dfs(TreeNode root, int target) {
        route.addLast(root.val);
        routeSum += root.val;
        if (root.left == null && root.right == null) {
            if (routeSum == target) {
                ArrayList<Integer> tempList = new ArrayList<>();
                for (int i = 0; i < route.size(); i++) {
                    tempList.add(route.get(i));
                }
                ansList.add(tempList);
            }
        }
        if (root.left != null) {
            dfs(root.left, target);
        }
        if (root.right != null) {
            dfs(root.right, target);
        }
        route.removeLast();
        routeSum -= root.val;
        return ;
    }
}
```

逮着一个坑就往里跳...

逻辑上是很简单，没任何花头的，主要是实现的细节上。

首先是第一个坑，一开始确实是考虑的根节点为空的可能，所以在dfs里面就用的是root是否为空的判断。但是这里的问题在于，不能在到了空节点的时候再去插到结果数组里面。这样的话一个叶子节点会有左右两个null，就会导致结果里面相同的路径出现了两次。要解决这个问题，实际上只需要把插入结果队列的操作还是放在当前节点上判断完成即可。第二个坑是，一开始脑子也没转过来，想到了要避开左右两个子节点为null的情况，就把插入结果队列放到了当前节点上，但是实际上判断是否进入左右子节点并不需要依赖为空，在当前节点进入的时候判断是否为空就会导致根节点为空的情况漏判。

就说起来其实都不是太大的问题，实现总归最后都是能写出来的，只是不能一次A。这个还是整体思路上要理清楚。怎么写可以保证根节点为空的情况在正常的处理里面就能包含，最基本的就是在核心的遍历或者处理部分就考虑好输入为null。

下面是题解的代码：

```java
class Solution {
    LinkedList<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> path = new LinkedList<>(); 
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        recur(root, sum);
        return res;
    }
    void recur(TreeNode root, int tar) {
        if(root == null) return;
        path.add(root.val);
        tar -= root.val;  // 直接拿target记就行，没必要多拖一个变量
        if(tar == 0 && root.left == null && root.right == null)  // 在当前节点的时候就判断，不会出现插入两次的情况
            res.add(new LinkedList(path));  // 这里必须要new，参考32题的2,3里面，应该是个浅复制的问题。反正看到这种两层嵌套的都记得new就行了
        recur(root.left, tar);
        recur(root.right, tar);
        path.removeLast();
    }
}
```

**==结果是两层嵌套形式的，内层每次都需要new新的容器，不然浅复制会影响前面已经插入的结果。==**

**==核心处理的时候就把节点为null考虑进去，尽量避免在当前节点判断子孙是否进入的情况。（可以避免重复结果）==**

## 4.4 分解让复杂问题简单化

通常分治法都可以用递归的代码实现。

### ==面试题35：复杂链表的复制==

这个问题的主要难点在于存在一个随机指向的指针，这个指针的处理肯定是没法和m_pNext指针同时完成的。为了解决随机指针的复制，有三种思路可以完成实现：

1. 先以pNext指针完成新链表的构建，然后对于每个节点的随机指针使用距离头节点的长度作为标识，完成在新链表中对随机指针的复制。这种方法的需要对每个节点的随机指针都从头向后遍历，因此其时间复杂度为$O(n^2)$，肯定难以满足需要。
2. 为了避免对每个指针用遍历搜索，可以使用哈希表存储新节点与旧节点的键值对，通过使用空间换时间的方式完成$O(n)$时间复杂度内的随机指针连接。这种方式的时间复杂度为$O(n)$，空间复杂度为$O(n)$。
3. 进一步的改进在于去除额外空间的开销。由于之前确定新旧节点的关系依赖于哈希表，所以才产生了额外的空间开销。因此尝试考虑直接在链表本身上确定新旧节点的关系。实现方法是将新节点保存在旧节点的后面，即新旧节点之间都是一一连接的关系。这样要想寻找旧节点所对应的新节点，只需要读取其下一个节点即可。完整的实现步骤是现在链表中插入新建的节点，随后为新建的节点构建随机指针关联，最后再将新建节点与旧节点完成拆分。整个过程相当于是三次循环，所以时间复杂度为$O(n)$，空间复杂度为$O(1)$。（**==特别注意第三步对链表的拆分==**）

这道题目从思考的角度来说是肯定会意识到要把pNext指针和随机指针的处理分开的，只是分开处理又要怎么保留关联，因为如果完全没有关联就需要使用额外的空间或者时间。

**==新节点、旧节点前后串一起，可以保留关联。==**

本题实现（2022/08/12）：

```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
class Solution {
    public Node copyRandomList(Node head) {
        copyNode(head);
        linkRandom(head);
        Node anshead = splitList(head);
        return anshead;
    }

    public void copyNode(Node head) {
        while (head != null) {
            Node newNode = new Node(head.val);
            Node nextNode = head.next;  // 可以化简
            head.next = newNode;
            newNode.next = nextNode;
            head = head.next.next;
        }
        return ;
    }

    public void linkRandom(Node head) {
        while (head != null) {
            if (head.random == null) {
                head.next.random = null;  // 新建的节点本来random就指向null，只要处理非null指向 
            } else {
                head.next.random = head.random.next;
            }
            head = head.next.next;
        }
        return ;
    }

    public Node splitList(Node head) {
        Node newHead = null, temp = null;
        while (head != null) {
            if (newHead == null) {
                newHead = head.next;
                temp = head.next;
            } else {
                temp.next = head.next;
                temp = head.next;
            }
            head.next = head.next.next;
            head = head.next;
        }
        return newHead;

    }
}
// 题解代码
class Solution {
    public Node copyRandomList(Node head) {
        if(head == null) return null;
        Node cur = head;
        // 1. 复制各节点，并构建拼接链表
        while(cur != null) {
            Node tmp = new Node(cur.val);
            tmp.next = cur.next;
            cur.next = tmp;
            cur = tmp.next;
        }
        // 2. 构建各新节点的 random 指向
        cur = head;
        while(cur != null) {
            if(cur.random != null)
                cur.next.random = cur.random.next;
            cur = cur.next.next;
        }
        // 3. 拆分两链表
        cur = head.next;
        Node pre = head, res = head.next;  // 最上面head等于null已经特判了，所以这里不用考虑
        while(cur.next != null) {
            pre.next = pre.next.next;
            cur.next = cur.next.next;
            pre = pre.next;
            cur = cur.next;
        }
        pre.next = null; // 单独处理原链表尾节点
        return res;      // 返回新链表头节点
    }
}
```

这题还是靠背的...最难的其实就是思路，如果知道了把新的节点挂在原本的节点后面这个思路，那么问题还是可以拆分成几个子任务去解决的。新增节点、确定随机指针的指向、拆分成两个链表。这道题目最重要的就是从位置上把新的和旧的节点关联了起来。

还有一个最基本的暴力思路要知道，就是用hashmap直接去存节点，然后进行匹配。

代码如下：

```java
class Solution {
    public Node copyRandomList(Node head) {
        if(head == null) return null;
        Node cur = head;
        Map<Node, Node> map = new HashMap<>();
        // 3. 复制各节点，并建立 “原节点 -> 新节点” 的 Map 映射
        while(cur != null) {
            map.put(cur, new Node(cur.val));
            cur = cur.next;
        }
        cur = head;
        // 4. 构建新链表的 next 和 random 指向
        while(cur != null) {
            map.get(cur).next = map.get(cur.next);
            map.get(cur).random = map.get(cur.random);
            cur = cur.next;
        }
        // 5. 返回新链表的头节点
        return map.get(head);
    }
}
```

### ==面试题36：二叉搜索树与双向链表==

这道题目看到二叉搜索树和有序肯定会想到中序遍历，问题在于如何处理这个中序遍历。一开始想得时候是对当前节点连接左右子节点补充指针就行了，但是通过观察可以发现，左子节点并非一定是根节点的上一个最大值。因此需要处理的时候得得到的是当前左子结构转换完毕的最大值的节点用来连接当前节点。

实现依然是用递归完成的，然后由于递归得到的是排序双向链表的最后一个节点，因此在返回时，还需要根据指针找到链表的头节点。这个由于在递归里面定义的pRight是指向下一个，所以要根据排序双向链表的尾节点得到头节点，只需要一直走pLeft就行了。在递归里面，先无限递归往下走到最小的叶子节点，由于初始的pLastNodeInList是null，因此该节点的pLeft是null，然后因为前面没节点所以也不需要把前面节点的pRight连到这个节点上。之后就需要更新链表中的pLastNodeInList是当前节点了，然后到右子结构中进行递归。

**总结一下，每个递归中的节点，只负责解决当前节点和排序双向链表中其上一个节点的双向指针关联，剩下的是在别的递归中完成的，对于排序双向链表的头指针，设置上一个关联为null。**

具体的代码看书上吧，这道题目要好好理解一下，不止是思路没有想到，实现里面的操作其实看了代码之后也没有很理解。

本题实现（2022/08/13）：

这道题目得注意下，因为c++的代码用的是指针，而java没有指针，所以实现上会有区别。

```java
// 题解代码
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val,Node _left,Node _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/
class Solution {
    Node pre, head;
    public Node treeToDoublyList(Node root) {
        if(root == null) return null;
        dfs(root);
        head.left = pre;
        pre.right = head;
        return head;
    }
    void dfs(Node cur) {
        if(cur == null) return;
        dfs(cur.left);
        if(pre != null) pre.right = cur;
        else head = cur;
        cur.left = pre;
        pre = cur;
        dfs(cur.right);
    }
}
// 当场背书又写了一遍
class Solution {
    Node pre = null, head = null;
    public Node treeToDoublyList(Node root) {
        if (root == null) {
            return null;
        }
        dfs(root);
        head.left = pre;
        pre.right = head;
        return head;
    }

    public void dfs(Node root) {
        if (root == null) {
            return ;
        }
        dfs(root.left);
        if (pre == null) {
            head = root;
        } else {
            pre.right = root;
        }
        root.left = pre;
        pre = root;
        dfs(root.right);
    }
}
```

这道题目最基础的就是得知道中序遍历（左根右）二叉搜索树能得到的数字序列是有序的。

然后这种问题肯定是得依赖递归去完成的，纯写逻辑不得写死。所以可以猜测肯定每次只处理一个节点（当前节点）。由于把左子树和右子树看成两部分，一开始肯定没法确定左子树节点的最后一个节点是哪个，只有处理完了整个左子树之后才能把它和当前节点连起来。所以当前节点处理的实际上就是和上一个节点之间的双向连接。

### ==面试题37：序列化二叉树==

什么鬼题目？看了书上的题解和leetcode上的题解都没有太明白这是个啥东西。不过大概知道改进的先序遍历（**缺失子节点的用标志位补齐**）和改进的层序遍历，这两种形式似乎都是对一棵二叉树是唯一的表示。这里以书上的先序遍历为例，序列化函数的递归在遇到空指针时达成递归终止条件返回，其余正常按照先序遍历进行（这个就相当于是打印二叉树嘛，只不过多打了改进的标注位）。在反序列化函数中，同样使用递归完成整个处理。由于序列化参照先序遍历的顺序，因此反序列化依旧使用该顺序处理当前节点并对左右子节点进行递归。对于当前的节点判断是否为标志位，如果不是标志位则创建节点用于构建关联。对于当前节点的左节点指向由第一个递归函数完成，对当前节点的右节点指向由第二个递归函数完成。这里是严格遵循先序遍历的顺序，保证反序列化得到的树结构相同。

**==使用 前序遍历+中序遍历 或 中序遍历+后序遍历 确定一棵二叉树，均要求二叉树中的节点不能重复==**

这道题目就背个思路吧，还有知道**==改进先序遍历和改进层序遍历能对二叉树进行唯一表示==**（改进为含叶节点下面的两个null节点）

本题实现（2022/08/14）：

写个锤子...下面这个就是该死的层序遍历（用先序遍历从逻辑和实现上都会更方便）

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if (root == null) {
            return "[]";
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        StringBuilder stringBuilder = new StringBuilder();
        stringBuilder.append("[");
        while (!queue.isEmpty()) {
            TreeNode tempNode = queue.poll();
            if (tempNode != null) {
                stringBuilder.append(tempNode.val + ",");
                queue.offer(tempNode.left);
                queue.offer(tempNode.right);
            } else {
                stringBuilder.append("null,");
            }
        }
        stringBuilder.deleteCharAt(stringBuilder.length()-1);
        stringBuilder.append("]");
        return stringBuilder.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data.equals("[]")) {
          return null;
        }
        /*
        StringBuilder stringBuilder = new StringBuilder(data);
        stringBuilder.deleteCharAt(stringBuilder.length()-1);
        stringBuilder.deleteCharAt(0);
        String beforeSplit = stringBuilder.toString();
        String[] stringArray = beforeSplit.split(",");
        */
        String[] stringArray = data.subString(1, data.length() - 1).split(",");
        int index = 0;
        Queue<TreeNode> queue = new LinkedList<>();
        TreeNode ansNode = new TreeNode(Integer.parseInt(stringArray[index++]));
        queue.offer(ansNode);
        // while (index < stringArray.length) {
        while (!queue.isEmpty()) {
            TreeNode parent = queue.poll();
            if (!stringArray[index].equals("null")) {
                parent.left = new TreeNode(Integer.parseInt(stringArray[index]));
                queue.offer(parent.left);
            }
            index++;
            /*
            if (index >= stringArray.length()) {
                break;
            }  // 这个可以不用，因为最后一组肯定是两个null，偶数个，不会中间就结束
            */
            if (!stringArray[index].equals("null")) {
                parent.right = new TreeNode(Integer.parseInt(stringArray[index]));
                queue.offer(parent.right);
            }
            index++;
        }
        return ansNode;
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.deserialize(codec.serialize(root));

// 题解代码
public class Codec {
    public String serialize(TreeNode root) {
        if(root == null) return "[]";
        StringBuilder res = new StringBuilder("[");
        Queue<TreeNode> queue = new LinkedList<>() {{ add(root); }};
        while(!queue.isEmpty()) {
            TreeNode node = queue.poll();
            if(node != null) {
                res.append(node.val + ",");
                queue.add(node.left);
                queue.add(node.right);
            }
            else res.append("null,");
        }
        res.deleteCharAt(res.length() - 1);
        res.append("]");
        return res.toString();
    }

    public TreeNode deserialize(String data) {
        if(data.equals("[]")) return null;
        String[] vals = data.substring(1, data.length() - 1).split(",");
        TreeNode root = new TreeNode(Integer.parseInt(vals[0]));
        Queue<TreeNode> queue = new LinkedList<>() {{ add(root); }};
        int i = 1;
        while(!queue.isEmpty()) {
            TreeNode node = queue.poll();
            if(!vals[i].equals("null")) {
                node.left = new TreeNode(Integer.parseInt(vals[i]));
                queue.add(node.left);
            }
            i++;
            if(!vals[i].equals("null")) {
                node.right = new TreeNode(Integer.parseInt(vals[i]));
                queue.add(node.right);
            }
            i++;
        }
        return root;
    }
}
```

你tm的leetcode自己官方题解都用的先序遍历

题解代码：

```java
public class Codec {
    public String serialize(TreeNode root) {
        return rserialize(root, "");
    }
  
    public TreeNode deserialize(String data) {
        String[] dataArray = data.split(",");  // 最后一个符号是逗号，split并不会多一个空字符
        List<String> dataList = new LinkedList<String>(Arrays.asList(dataArray));
        return rdeserialize(dataList);
    }

    public String rserialize(TreeNode root, String str) {
        if (root == null) {
            str += "None,";
        } else {
            str += str.valueOf(root.val) + ",";  // 虽然会造成最后多一个逗号，但是split后并不会多
            str = rserialize(root.left, str);
            str = rserialize(root.right, str);
        }
        return str;
    }
  
    public TreeNode rdeserialize(List<String> dataList) {
        if (dataList.get(0).equals("None")) {
            dataList.remove(0);
            return null;
        }
  
        TreeNode root = new TreeNode(Integer.valueOf(dataList.get(0)));
        // 这个Integer.valueOf内部也是调的Integer.parseInt;parseInt有两个参数，字符串和进制(int)，进制默认为10
        dataList.remove(0);
        root.left = rdeserialize(dataList);
        root.right = rdeserialize(dataList);
        // 上面这几行很关键
        return root;
    }
}
```

只要正常按照根左右的先序遍历顺序区序列化和还原，至少思路上会简单很多。

### ==面试题38：字符串的排列==

书上这个应该也算是回溯法，但是感觉讲得不是很清楚，可以参考这个[题解](https://leetcode.cn/problems/zi-fu-chuan-de-pai-lie-lcof/solution/mian-shi-ti-38-zi-fu-chuan-de-pai-lie-hui-su-fa-by/)。

按照题解的解释，整个过程的思路实际上是一个dfs（即不断往深的走，然后先探索路径到最深处之后返回探索其它路径）。由于字符串的排列肯定要求使用的字符不能重复，因此在越深的地方选择也就越少。题解使用的是交换策略，对自身及之后的字符进行交换从而确定所有可能出现的字符。考虑到给出的字符串中存在多个重复的字符，如果不进行去重，则肯定会在结果中包含重复的字符串。为了解决该问题的策略是保证同样的字符在同一位置只出现一次。通过在当前位置的循环中设置集合来确定当前使用的值是否重复出现过。

代码：

```java
class Solution {
    List<String> res = new LinkedList<>();  // 存放结果
    char[] c;
    public String[] permutation(String s) {  // String s是给出的字符串
        c = s.toCharArray();
        dfs(0);
        return res.toArray(new String[res.size()]);
    }
    void dfs(int x) {
        if(x == c.length - 1) {  // 到了最深层，进行打印（只存在自身交换自身，因此不需要交换直接打印即可）
            res.add(String.valueOf(c));      // 添加排列方案
            return;
        }
        HashSet<Character> set = new HashSet<>();  // 递归函数中的局部变量，集合只用于在当前函数中判断是否重复
        for(int i = x; i < c.length; i++) {  // 在当前层的循环里面判断同样的字符是否已经出现过
            if(set.contains(c[i])) continue; // 重复，因此剪枝
            set.add(c[i]);
            swap(i, x);                      // 交换，将 c[i] 固定在第 x 位
            dfs(x + 1);                      // 开启固定第 x + 1 位字符
            swap(i, x);                      // 恢复交换（重要）
        }
    }
    void swap(int a, int b) {
        char tmp = c[a];
        c[a] = c[b];
        c[b] = tmp;
    }
}
```

本题实现（2022/08/17）：

```java
class Solution {
    char[] charArray;
    LinkedList<String> linkedList = new LinkedList<>();
    StringBuilder stringBuilder = new StringBuilder();
    
    public String[] permutation(String s) {
        charArray = s.toCharArray();
        dfs(0);
        String[] ansStringArray = new String[linkedList.size()];
        int index = 0;
        for (String temp: linkedList) {
            ansStringArray[index++] = temp;
        }
        return ansStringArray;
    }

    public void dfs(int index) {
        if (index == charArray.length) {
            linkedList.add(stringBuilder.toString());
            return ;
        }
        HashSet<Character> hashSet = new HashSet<>();
        for (int i = index; i < charArray.length; i++) {
            if (hashSet.contains(charArray[i])) {
                continue;
            }
            hashSet.add(charArray[i]);
            stringBuilder.append(charArray[i]);
            swap(charArray, index, i);
            dfs(index + 1);
            stringBuilder.deleteCharAt(stringBuilder.length() - 1);
            swap(charArray, index, i);
        }
    }

    public void swap(char[] array, int i, int j) {
        char temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
// 题解
class Solution {
    List<String> res = new LinkedList<>();
    char[] c;  // 处理过程中直接拿这个用来记录某一次的排列结果
    public String[] permutation(String s) {
        c = s.toCharArray();
        dfs(0);
        return res.toArray(new String[res.size()]);  // list转array,LinkedList和ArrayList都有toArray方法,toArray是Collection接口中的方法
    }
    void dfs(int x) {
        if(x == c.length - 1) {
            res.add(String.valueOf(c));      // 添加排列方案
            return;
        }
        HashSet<Character> set = new HashSet<>();
        for(int i = x; i < c.length; i++) {
            if(set.contains(c[i])) continue; // 重复，因此剪枝
            set.add(c[i]);
            swap(i, x);                      // 交换，将 c[i] 固定在第 x 位
            dfs(x + 1);                      // 开启固定第 x + 1 位字符
            swap(i, x);                      // 恢复交换
        }
    }
    void swap(int a, int b) {
        char tmp = c[a];
        c[a] = c[b];
        c[b] = tmp;
    }
}
```

第二种方法，基于“[下一个排列](https://zhuanlan.zhihu.com/p/185058067?utm_id=0)”问题进行实现。

#### [leetcode31. 下一个排列](https://leetcode.cn/problems/next-permutation/)

得到下一个排列的实现思路描述：

首先从后向前找，找到第一次出现前一个比后一个（严格）小的；然后将这个比后一个小的对象与其后方的某个对象进行交换，交换的对象选取为从后向前第一个（严格）大于当前对象的，交换这两个对象；最后令第一步选取对象的右侧对象倒序（即修改为升序，第二步交换操作之后一定可以保证右侧部分是降序的，可以有相邻值相等，但满足降序）。

```java
class Solution {
    public String[] permutation(String s) {
        List<String> ret = new ArrayList<String>();
        char[] arr = s.toCharArray();
        Arrays.sort(arr);  // 升序排列
        do {
            ret.add(new String(arr));
        } while (nextPermutation(arr));
        int size = ret.size();
        String[] retArr = new String[size];
        for (int i = 0; i < size; i++) {
            retArr[i] = ret.get(i);
        }
        return retArr;
    }

    public boolean nextPermutation(char[] arr) {  // 下一个排列的这个算法可以处理有重复的情况
        int i = arr.length - 2;
        while (i >= 0 && arr[i] >= arr[i + 1]) {
            i--;
        }
        if (i < 0) {
            return false;  // 不断寻找下一个排列，直到最后当前排列已经为最大值返回false
        }
        int j = arr.length - 1;
        while (j >= 0 && arr[i] >= arr[j]) {
            j--;
        }
        swap(arr, i, j);
        reverse(arr, i + 1);
        return true;
    }

    public void swap(char[] arr, int i, int j) {
        char temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    public void reverse(char[] arr, int start) {
        int left = start, right = arr.length - 1;
        while (left < right) {
            swap(arr, left, right);
            left++;
            right--;
        }
    }
}
```

另外一种，回溯算法模板，标记访问状态，去重可以用set完成。

```java
class Solution {
    
    /**
        该题类似于 全排列2，本题使用set来去除重复元素
        除了使用set去重外，还可以对数组进行排序，使用visited数组进行剪枝！

    */
    Set<String> res = new HashSet();  // set中没有重复的元素
    public String[] permutation(String s) {

        backtrack(s.toCharArray(),new StringBuilder(), new boolean[s.length()]);
        return res.toArray(new String[0]);   // 泛型方法，里面的new String只是确定类型

    }
    
    // 回溯函数
    public void backtrack(char[] ch,StringBuilder sb, boolean[] visitid){
        // 终止条件
        if(sb.length() == ch.length){
            res.add(sb.toString());
            return;
        }
        // 选择列表
        for(int i = 0; i < ch.length; i++){
            // 剪枝，如果当前位置的元素已经使用过，则跳过进入下一个位置
            if(visitid[i]) continue;
            // 做出选择
            sb.append(ch[i]);
            // 更新标记
            visitid[i] = true;
            // 进入下层回溯
            backtrack(ch,sb,visitid);
            // 撤销选择
            sb.deleteCharAt(sb.length()-1);
            visitid[i] = false;
            
        }
    }
}
// 自己写的
class Solution {
    HashSet<String> hashSet = new HashSet<>();
    public String[] permutation(String s) {
        char[] charArray = s.toCharArray();
        boolean[] visited = new boolean[charArray.length];
        StringBuilder stringBuilder = new StringBuilder();
        dfs(charArray, visited, stringBuilder);
        /*
        String[] ans = new String[hashSet.size()];
        int index = 0;
        for (String temp: hashSet) {
            ans[index++] = temp;
        }
        return ans;
        */
        return hashSet.toArray(new String[0]);
    }

    public void dfs(char[] charArray, boolean[] visited, StringBuilder stringBuilder) {
        if (stringBuilder.length() == charArray.length) {
            hashSet.add(stringBuilder.toString());
            return ;
        }
        for (int i = 0; i < charArray.length; i++) {   
            if (visited[i]) {
                continue;
            }
            visited[i] = true;
            stringBuilder.append(charArray[i]);
            dfs(charArray, visited, stringBuilder);
            visited[i] = false;
            stringBuilder.deleteCharAt(stringBuilder.length() - 1);
        }
    }
}
```

本题扩展：如果不是求字符串的所有排列，而是求字符的所有组合？（ab和ba是不同的排列，但只算一个组合。）

求n个字符的长度为m的组合的时候，可以把这个n个字符分成两部分：第一个字符和其余的所有字符。如果组合里包含第一个字符，则下一步在剩余的字符里选取m-1个字符；如果组合里不包含第一个字符，则下一步在剩余的n-1个字符里选取m个字符。

代码可以参考这个[博客](https://blog.csdn.net/login_sonata/article/details/70992875)。其中递归终止条件的设置是m=0以及字符全用完，一开始自己想得时候是剩下的字符和m的数量相等作为递归终止条件，确实可以早递归终止，但是需要处理剩余字符的入栈，因此从整体来说还是这个博客里面的两个递归终止条件更好。

此外，博客里面有提到：此方法在输入没有重复字符的情况下正常，但是如果输入类似aba的就不对了，因为它会输出重复的。解决办法是输入aba前先去重变为ab，然后把ab作为输入。如果不在一开始就进行去重就会造成之后的组合中出现重复的情况。

相关题目：（都是先分解成基本问题，然后再根据特定的要求完成实现。以下两个问题中所包含的基本问题就是全排列问题）

- 输入一个含有8个数字的数组，判断有没有可能把这8个数字分别放到正方体的8个顶点上，使得正方体上三组相对的面上的4个顶点的和都相等。

  这道题目的要求可以通过为每个顶点起一个变量名称而转化为三个等式，随后这个问题就是在8个数字的全排列中寻找是否存在符合要求的排列情况。因此这个问题最终的核心其实就是得到8个数字的全排列所有情况。

- 在8*8的国际象棋上摆放8个皇后，使其不能相互攻击，即任意两个皇后不得处在同一行、同一列或者同一条对角线上。

  为了确保不在同一行同一列，使用一个长为8的数组，表示行，数组中的值表示列。数组中的值用0~7进行初始化。这样就已经保证了任意两个皇后不处在同一行且不处在同一列。而对角线的条件有两个（见书上），因此为了保证任意两个皇后不得处在同一条对角线上，只需要对0~7进行排列，然后按照下标和值的检查是否存在对角线的情况。

**==举一反三：如果面试题是按照一定要求摆放若干个数字，则可以先求出这些数字的所有排列，然后一一判断每个排列是不是满足题目给定的要求。==**

**==set可以用于去重（不需要手动判断）==**

## 5.2 时间效率

### 面试题39：数组中出现次数超过一半的数字

一道经典的题目，当然这里主要是把几种思路和优劣都分析全。

首先是出现次数超过一半，那对于有序的数组来说，其中间的数一定就是想要的结果。对于奇数，中间的数只有一个，肯定符合；对于偶数，中间的数有两个，但是因为数组中要求出现的次数超过一半，所以中间的两个数字应该是一样的，这样不管是取左边的数还是右边的数就都ok。获取中位数下标的方式是采用右移完成的，右移操作比/=2的操作更快。

基于这种思路就有了第一种解法，基于Partition函数的时间复杂度为$O(n)$的解法

这种方法主要是利用了快速排序中的**Partition函数可以使得前面的数都小于自身，后面的数都大于自身**这个性质。（也就是确定第k大的数，k为过程中比较的基准数的最终下标）在不断循环的过程中，总是对包含middle的一半继续使用Partition函数处理，直到最后选定的基准数正好落在middle位上时即可得到最终的结果。这里书上的海鲜还对于非法输入的情况进行了考虑。非法输入的情况包括两种，第一种是输入的数组为空，第二种则是数组中不存在出现次数超过一半的数字。对于第二种情况只能采用从头到尾扫描计数的方式进行确认。扫描计数的函数是在找到结果之后进行的，这样才能保证直接用当前的值与数组中的值进行比较，可以通过一次扫描以$O(n)$的时间复杂度确定数组中是否存在出现次数超过一半的数字。

**第二种方式，根据数组特点找出时间复杂度为$O(n)$的算法**

如果数组中有数字出现次数超过一半，那么对于数组中的其它数字其实是一样的类别。在处理的时候使用两个局部变量记录状态，一个局部变量用于存储当前可能的结果，另一个变量用于存储其出现次数。对于数组中的数字，如果与当前内容相同则出现次数+1，如果与当前内容不同则出现次数-1。当出现次数为0时，则在下一个数字进入时进行更新可能的结果。这个其实本质上就是在做计数，只不过方式比较巧妙，对于出现不超过一半的数字肯定会在整个扫描的过程中被与其不同的数字消除，导致最终出现次数==0，这样就会要产生更新，只有出现次数超过一半的数字才能坚持到最后，并且最终统计的出现次数>1。

比较第一种第二种算法的优劣，从时间复杂度上来说，两个方法是相同的，都是$O(n)$。第一种算法中的Partition函数其实本身是一个非常常用的函数，可以用它完成很多任务，但是其坏处在于会打乱顺序。相比之下，**第二种算法并不会对数组本身的顺序造成打乱**。因此如果有明确的要求不能打乱原本数组的时候，就只能使用第二种方式了。

本题实现（2022/08/19）：

```java
class Solution {
    public int majorityElement(int[] nums) {
        int rem = nums[0];
        int count = 1;
        for (int i = 1; i < nums.length; i++) {
            if (count == 0) {
                rem = nums[i];
                count = 1;
            } else {
                if (nums[i] == rem) {
                    count ++;
                } else {
                    count --;
                }
            }
        }
        return rem;
    }
}
```

如果题目并没有确定给定的数组总是存在多数元素（即众数一定存在），那么就需要进行多一次数组遍历，验证是否超过半数。

### 面试题40：最小的k个数

这道题目其实有了上一道题目的方法做铺垫，要完成还是很容易联想到Partition函数的，只是与上一道题目相同，Partition函数同样存在打乱顺序的问题。

方法一：时间复杂度为$O(n)$的算法，只有当我们可以修改输入的数组时可用

由于是最小的k个数，如果用Partition函数的基准数最终落到的下标正好为k时，其左侧的数字一定都是小于这个基准数的，因此只需要打印左边的数字就可以得到最小的k个数。（其实这个Partition函数的范围处理与二分查找很相似）

方法二：时间复杂度为$O(nlogk)$的算法，特别适合处理海量数据

因为要得到最小的k个数，因此可以考虑先构建k个空间用于存储。当存储空间不满时直接填入，当存储空间已经填满时，使用当前的数字与存储空间中的数字进行比较，如果当前的数字比存储空间中的最大数字还要大，则直接跳过。如果当前的数字比存储空间中的最大数字要小，则使用当前的数字替换存储空间中的最大数字。由于一直需要进行获取存储空间中最大数字的操作，因此应当联想到最大堆。最大堆的查找时间复杂度为$O(1)$，插入和删除的时间复杂度为$O(logk)$，因此对于全部的k个数字，总的时间效率就是$O(nlogk)$。

另外红黑树也可以达到这个时间复杂度，红黑树的查找、删除和插入操作都只需要$O(logk)$的时间，因此也可以完成。

对第一种解法和第二种解法进行比较，第一种解法的时间复杂度更低，但是其会对原本的数据顺序造成打乱。相比之下，第二种算法则不会打乱输入数据，因为比较都在最小堆的额外空间中，对于本身的数组只需要依次读出即可。此外，第二种算法还存在一个优点就是它可以适用于海量数据。第一种算法要求将所有的数据全部读到内存中才能进行处理，但是对于海量的数据而言，全部读到内存中是无法实现的；第二种方法每次只需要从数组中读取一个数字与最大堆中的数进行比较，并没有受限于数组本身的大小，要求的空间只有k个数据的存储空间。**假设题目是要求从海量的数据中找出最小的k个数字，由于内存的大小是有限的，有可能不能把这些海量的数据一次性全部载入内存。这个时候，我们可以从辅助存储空间（如硬盘）中每次读入一个数字进行处理，这样只需要内存能够容纳leastNumbers就可以了，因此它最适合的情形就是n很大并且k较小的问题。**（见书P212，**==海量数据问题==**）

本题实现（2022/08/19）：

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (arr.length == 0 || k == 0) {
            return new int[0];
        }
        int start = 0, end = arr.length - 1;
        int index = partition(arr, start, end);
        while (index + 1 != k) {
            if (index + 1 > k) {
                end = index - 1;
                index = partition(arr, start, end);
            } else {
                start = index + 1;
                index = partition(arr, start, end);
            }
        }
        int[] ans = new int[k];
        for (int i = 0; i < k; i++) {
            ans[i] = arr[i];
        }
        return ans;
    }
    public int partition(int[] arr, int start, int end) {
        int val = arr[end], index = start - 1;
        for (int i = start; i < end; i++) {
            if (arr[i] <= val) {
                index++;
                if (index != i) {
                    arr[i] = arr[i] + arr[index];
                    arr[index] = arr[i] - arr[index];
                    arr[i] = arr[i] - arr[index];
                    // int temp = arr[i];
                    // arr[i] = arr[index];
                    // arr[index] = temp;
                }
            }
        }
        index++;
        if (index != end) {
            arr[end] = arr[index] + arr[end];
            arr[index] = arr[end] - arr[index];
            arr[end] = arr[end] - arr[index];
        }
        // int temp = arr[end];
        // arr[end] = arr[index];
        // arr[index] = temp;
        return index;
    }
}
// 解法二，官方题解
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        int[] vec = new int[k];
        if (k == 0) { // 排除 0 的情况
            return vec;
        }
        PriorityQueue<Integer> queue = new PriorityQueue<Integer>(Comparator.reverseOrder());
        for (int i = 0; i < k; ++i) {
            queue.offer(arr[i]);
        }
        for (int i = k; i < arr.length; ++i) {
            if (queue.peek() > arr[i]) {
                queue.poll();
                queue.offer(arr[i]);
            }
        }
        for (int i = 0; i < k; ++i) {
            vec[i] = queue.poll();
        }
        return vec;
    }
}
```

这题写的时候脑子抽住了，主函数里面的上下分区没想通。index得到之后可以保证左边的小于等于自己（对应这种partition里的<=），右边的大于自己，所以实际上是只有不断收缩的中间一段顺序未定。

PS：原地交换数值的操作一定要注意，不能是自己和自己进行运算，不然会出错。最好还是老老实实拿个temp存吧，反正不差这一个的空间，给自己挖坑没意义。

解法二主要是针对那种**数据流**或者是**限制内存大小**情况下的解法。

注意优先队列（最大堆、最小堆）的使用，Comparator.naturalOrder()升序，Comparator.reverseOrder()降序。

### 面试题41：数据流中的中位数

这道题目首先就要注意到，数据是以数据流形式到来的，而非本身存储在某个数据结构中一次性到来的。对于数据流而言，数据是一位位到来的，因此需要更多关注于数据的插入和数据结构的维护开销。

书上给出了几种不同的数据结构的插入时间复杂度和得到中位数的时间复杂度：

| 数据结构                                       | 插入的时间复杂度          | 得到中位数的时间复杂度    |
| ---------------------------------------------- | ------------------------- | ------------------------- |
| 没有排序的数组                                 | $O(1)$                    | $O(n)$                    |
| 排序的数组                                     | $O(n)$                    | $O(1)$                    |
| 排序的链表（需要定义两个中间指针）             | $O(n)$                    | $O(1)$                    |
| 二叉搜索树（需添加一个表示子树节点数目的字段） | 平均$O(logn)$，最差$O(n)$ | 平均$O(logn)$，最差$O(n)$ |
| AVL树（不易实现）                              | $O(logn)$                 | $O(1)$                    |
| 最大堆和最小堆                                 | $O(logn)$                 | $O(1)$                    |

其中最合适的方式是采用最后一种最大堆和最小堆进行实现。对于数据可以分为两部分，左半部分用最大堆存储使得容易获得最大值，右半部分用最小堆存储使得容易获得最小值。这样只需要保证好最大堆与最小堆内的元素数量即可很便捷地通过获取最大堆中的最大值或者获取最小堆中的最小值得到数据流中的中位数。

**两个堆中数据的数目之差不能超过1，为了实现平均分配，可以在数据的总数目是偶数时把新数据插入最小堆，否则插入最大堆。为了保证最大堆中的所有数据都要大于最小堆中的数据。当数据的总数目是偶数时，先把新的数据插入最大堆，接着把最大堆中最大的数字拿出来插入最小堆。由于最终插入最小堆的数字是原最大堆中的最大数字，这保证了最小堆中的所有数字都大于最大堆中的数字。**

代码见书上，主要是上面的这个思想，如何为这个由最大堆和最小堆共同构成的复合数据结构，同时需要保证其平衡性。

本题实现（2022/08/19）：

```java
class MedianFinder {
    PriorityQueue<Integer> priorityQueue1;
    PriorityQueue<Integer> priorityQueue2;

    /** initialize your data structure here. */
    public MedianFinder() {
        priorityQueue1 = new PriorityQueue<>(Comparator.reverseOrder()); // 拿最大的
        priorityQueue2 = new PriorityQueue<>(Comparator.naturalOrder()); // 拿最小的
    }
    
    public void addNum(int num) {
        if (priorityQueue1.isEmpty()) {
            priorityQueue1.offer(num);
        } else if (priorityQueue2.isEmpty()){
            if (num >= priorityQueue1.peek()) {
                priorityQueue2.offer(num);
            } else {
                priorityQueue2.offer(priorityQueue1.poll());
                priorityQueue1.offer(num);
            }
        } else {
            int num1 = priorityQueue1.peek(), num2 = priorityQueue2.peek();
            if (priorityQueue1.size() == priorityQueue2.size()) {
                if (num <= num2) {
                    priorityQueue1.offer(num);
                } else {
                    priorityQueue1.offer(priorityQueue2.poll());
                    priorityQueue2.offer(num);
                }
            } else {  // 必定大于
                if (num >= num1) {
                    priorityQueue2.offer(num);
                } else {
                    priorityQueue2.offer(priorityQueue1.poll());
                    priorityQueue1.offer(num);
                }
            }
        }
    }
    
    public double findMedian() {
        if (priorityQueue1.size() > priorityQueue2.size()) {
            return priorityQueue1.peek();
        } else {
            return (priorityQueue1.peek() + priorityQueue2.peek()) / 2.0;
        }
    }
}

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```

这个对思路有一丝印象，加上上一道题目正好看了优先队列，不然估计想不大到。

这里面要想找到中位数应该想到比中位数大的存一部分，比中位数小的存一部分，这样才可能找到中位数。同时这两部分里面对中位数有影响的只有左边的最大值和右边的最小值，因此很容易想到最大堆和最小堆。再之后就是保证数量平衡的判断了，反正就最大堆顶、最小堆顶和插入数比较，然后根据平衡情况看怎么插。

### 面试题42：连续子数组的最大和

这道其实也是经典的老题了。

第一种思路：从前向后扫描，当出现累积为负值时就刷新。**如果当前是负值，减了之后比原本小了，那还是保存之前的最大值。**这样的方式只需要一次扫描就可以确定连续子数组的最大和。

这种思路其实还是蛮简单的，而且实现起来也没有太大难度。书上对于累积负值的判断是在下一个数字进来时进行的，如果是负值，那么就直接给它赋一个新的值，相当于更新了。另外再维护一个整体的最大值进行保存就可以了。

第二种思路：这道题目其实还可以当成一个动态规划的问题来看待，其动态规划的递归方程如下：

$f(i)=\left\{\begin{aligned}&pData[i], & i=0 或者 f(i-1)\leq0 \\ &f(i-1)+pData[i],&i\neq0并且f(i-1)>0\end{aligned}\right.$

本质上来说除了i=0，别的时候就是一个$f(i)=max(pData[i],f(i-1)+pData[i])$的关系。

[leetcode动态规划题解](https://leetcode.cn/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/solution/mian-shi-ti-42-lian-xu-zi-shu-zu-de-zui-da-he-do-2/)

代码：

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int res = nums[0];  // 初始条件
        for(int i = 1; i < nums.length; i++) {
            nums[i] += Math.max(nums[i - 1], 0);  // nums[i]就是dp[i]，缺点是会修改原数组
            res = Math.max(res, nums[i]);
        }
        return res;
    }
}
```

```java
// 解耦dp[i]和nums[i]
class Solution {
    public int maxSubArray(int[] nums) {
        int max = nums[0];
        int former = 0;//用于记录dp[i-1]的值，对于dp[0]而言，其前面的dp[-1]=0
        int cur = nums[0];//用于记录dp[i]的值
        for(int num:nums){
            cur = num;  // f(i)=pData[i]
            if(former>0) cur +=former;  // f(i)=f(i-1)+pData[i]
            if(cur>max) max = cur;
            former=cur;
        }
        return max;
    }
}
```

**这道题目主要是把下面的动态规划的递归方程怎么找到，以及怎么把递归方程转变成最终的代码。**

本题实现（2022/08/19）：

```java
// 自己写的这个太拉了
class Solution {
    public int maxSubArray(int[] nums) {
        int ans = nums[0], max = nums[0];
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] >= 0) {
                if (ans < 0) {
                    ans = nums[i];
                } else {
                    ans += nums[i];
                }
            } else {
                if (ans > 0) {
                    ans += nums[i];
                } else {
                    ans = nums[i];
                }
            }
            max = Math.max(max, ans);
        }
        return max;
    }
}
// 这个是自己写的逻辑简化版本
class Solution {
    public int maxSubArray(int[] nums) {
        int ans = nums[0], max = nums[0];
        for (int i = 1; i < nums.length; i++) {
            if (ans < 0) {  // 前面只会拖后腿，不要了；和当前正负无关
                ans = nums[i];
            } else {
                ans += nums[i];
            }
            max = Math.max(max, ans); 
        }
        return max;
    }
}
// 题解代码
class Solution {
    public int maxSubArray(int[] nums) {
        int ans = nums[0], max = nums[0];
        for (int i = 1; i < nums.length; i++) {
            nums[i] += Math.max(nums[i - 1], 0);
            max = Math.max(max, nums[i]);
        }
        return max;
    }
}
```

怎么说呢，经典题，可能不看出动态规划也知道做法。不过如果分析清楚了动态规划肯定又好写，思路也清晰。

### 面试题43：1~n整数中1出现的次数

看了半天[leetcode题解](https://leetcode.cn/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/solution/mian-shi-ti-43-1n-zheng-shu-zhong-1-chu-xian-de-2/)大概才搞明白意思。（记住下面有个评论老哥提到的拨密码锁的例子，把问题具象化）

这个判断肯定是根据每一位上的值分开算得，因为如果要一起算，像11或者111这种绝对就会出问题，因此每次只管当前的数位。根据n在当前数位上的值的不同，当前位置导致可能出现的1的次数有所区别。首先是n在当前位上是0，这时候只有比它小的数才可能在当前位上出现0，而这个比它小的数有多少是由最高位和数位决定的，数位决定了比例，最高位决定了数量。

![image-20220611224113514](../images/LeetCode的菜鸡实录.assets/image-20220611224113514.png)

如果n在当前位是1，那么可能出现的次数就需要和低位牵扯下了，因为可以当前位上最终是1，再走过低位个数。

![image-20220611224303579](../images/LeetCode的菜鸡实录.assets/image-20220611224303579.png)

如果n在当前位上是2~9，那么就相当于都肯定越过了1了，这时候当前位出现1的情况是填满的，因此直接+1就行。

![image-20220611224359169](../images/LeetCode的菜鸡实录.assets/image-20220611224359169.png)

```java
class Solution {
    public int countDigitOne(int n) {
        int digit = 1, res = 0;
        int high = n / 10, cur = n % 10, low = 0;
        while(high != 0 || cur != 0) {  // 说明当前位还没有从最高位上移出去
            if(cur == 0) res += high * digit;  // 当前位上的值等于0的情况
            else if(cur == 1) res += high * digit + low + 1;  // 当前位上的值等于1的情况
            else res += (high + 1) * digit;  // 当前位上的值大于1的情况
            low += cur * digit;  // 所有低位的值
            cur = high % 10;
            high /= 10;
            digit *= 10;
        }
        return res;
    }
}
```

代码实际上计算就是从最低位开始往上走，根据n在每一位上的数值把所有的情况都统计出来然后得到最终的结果。

**==这道题目感觉上最难的就是怎么拆分成等于0，等于1和大于1三种情况分开讨论，然后就是怎么根据高低位总结出计算通式。==**

本题实现（2022/08/21）：

背出来了要分三种情况，但是循环每一位的构建没有实现出来。

```java
class Solution {
    public int countDigitOne(int n) {
        int high = n / 10, low = 0, cur = n % 10, digit = 1, ans = 0;
        while(high != 0 || cur != 0) {
            if (cur == 0) {
                ans += high * digit;
            } else if (cur == 1) {
                ans += high * digit + low + 1;
            } else {
                ans += (high + 1) * digit; 
            }
            cur = high % 10;
            high /= 10;
            digit *= 10;
            low = n % digit;
        }
        return ans;
    }
}
```

写的时候确实是想到了从低位往高位去循环。不过还是没有把当前位的情况和高低位关联起来。只是背出来了0,1和大于1三种情况还是不够的。

**==关键思路：高位、低位、当前位==**

### 面试题44：数字序列中某一位的数字

这道题目看了之后应该肯定不会用枚举的暴力去做，不然太傻了。还是需要用规律去做。

对于一位数，有10个；对于两位数，10~99，总共有90个；对于n位数，有9*10^(n-1)个。因此可以对于需要的位数逐个删减，然后找到最后所在的范围，并根据范围确定出是多少位数的第几个，然后得到它的数值。

代码的话其它都是正常的流程，就最后求哪一位数字的时候要注意点。一个是先要知道是哪一个数，另外一个是要知道从右往左数第几位，因为正常知道的位数是从左往右数的，而要求到具体是哪个数，就得从右往左才好求。

本题实现（2022/08/21）：

思路是对的，但是边界判断啥的还是有问题

```java
class Solution {
    public int findNthDigit(int n) {
        if (n <= 9) {
            return n;
        }
        n -= 9;
        int flag = 90, digit = 2, start = 10;
        while (n > flag * digit) {
            if (flag > Integer.MAX_VALUE / 10) {
                break;
            }
            n -= flag * digit;
            flag *= 10;
            digit += 1;
            start *= 10;
        }
        int index = n / digit, left = n % digit, right;
        if (left == 0) {
            right = 1;
            index--;
        } else {
            right = digit - left + 1;
        }
        start += index;
        int num = start % 10;
        right--;
        while (right != 0) {
            start /= 10;
            num = start % 10;
            right--;
        }
        return num;
    }
}
// 题解
class Solution {
    public int findNthDigit(int n) {
        int digit = 1;
        long start = 1;  // 2^31次再往上一阶*10会正好int越界
        long count = 9;
        while (n > count) { // 1.
            n -= count;
            digit += 1;
            start *= 10;
            count = digit * start * 9;
        }
        long num = start + (n - 1) / digit; // 2.
        return Long.toString(num).charAt((n - 1) % digit) - '0'; // 3.
    }
}
```

官方题解有两点明显更好，一个是把1位数和更多位数整合在同一个循环逻辑里面了，还有一个就是取数字的处理$(n-1)$%digit。另外用的charAt就可以是从左往右数的第几位，也不需要在左数和右数颠倒了。

### ==面试题45：把数组排成最小的数==

这道题目一开始我想的是先要比较第一位找最小的，然后比第二位，这个比第二位可能有几种情况，要么是全部都在第二位上有数字，那肯定好比，如果没数字相当于又要去比别的数字的第一位...

所以上面这种想法肯定就不行了，那么实际上还是需要去比较数组中的数的，只不过需要以一种新的规则来完成比较。

这个新的规则的比较通过组合前后两个数字顺序的排列来确定大小，因为对于整体的最小相当于是排在最前面。这个比较因为是拼接字符串，所以可以使用字符串的比较操作完成（字典序）。

整体的排序操作可以模仿快速排序完成，只不过对元运算做一下封装，变成了字符串的比较。

[leetcode题解](https://leetcode.cn/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/solution/mian-shi-ti-45-ba-shu-zu-pai-cheng-zui-xiao-de-s-4/)

```java
class Solution {
    public String minNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for(int i = 0; i < nums.length; i++)
            strs[i] = String.valueOf(nums[i]);
        quickSort(strs, 0, strs.length - 1);
        StringBuilder res = new StringBuilder();
        for(String s : strs)
            res.append(s);
        return res.toString();
    }
    void quickSort(String[] strs, int l, int r) {
        if(l >= r) return;
        int i = l, j = r;
        String tmp = strs[i];
        while(i < j) {
            while((strs[j] + strs[l]).compareTo(strs[l] + strs[j]) >= 0 && i < j) j--;  
            while((strs[i] + strs[l]).compareTo(strs[l] + strs[i]) <= 0 && i < j) i++;
            tmp = strs[i];
            strs[i] = strs[j];
            strs[j] = tmp;
        }
        strs[i] = strs[l];
        strs[l] = tmp;
        quickSort(strs, l, i - 1);
        quickSort(strs, i + 1, r);
    }
}
```

java的比较操作可以使用A.compareTo(B);

本题实现（2022/08/21）：

```java
class Solution {
    public String minNumber(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < nums.length; i++) {
            stringBuilder.append(Integer.toString(nums[i]));
        }
        return stringBuilder.toString();
    }
    public void quickSort(int[] nums, int start, int end) {
        if (start == end) {
            return ;
        }
        int index = partition(nums, start, end);
        if (start < index) {
            quickSort(nums, start, index - 1);
        }
        if (end > index) {
            quickSort(nums, index + 1, end);
        }
    }
    public int partition(int[] nums, int start, int end) {
        int val = nums[end], min = start - 1;
        for (int i = start; i < end; i++) {
            if (larger(nums[i], val)) {
                min++;
                if (i > min) {
                    int temp = nums[i];
                    nums[i] = nums[min];
                    nums[min] = temp;
                }
            }
        }
        min++;
        if (min != end) {
            int temp = nums[end];
            nums[end] = nums[min];
            nums[min] = temp;
        }
        return min;
    }
    public boolean larger(int i, int j) {
        String str1 = Integer.toString(i) + Integer.toString(j);  // String.valueOf也能转成String类型
        String str2 = Integer.toString(j) + Integer.toString(i);
        for (int n = 0; n < str1.length(); n++) {
            if (str1.charAt(n) > str2.charAt(n)) {
                return false;
            } else if (str1.charAt(n) < str2.charAt(n)) {
                return true;
            }
        }
        return true;
    }
}
```

这题其实一开始还是没思路，后来思考加回忆了下，肯定是去比较数值。然后回忆起来了是通过拼接前后两个数字的顺序比较的大小。（原理没印象，只记得做法）然后就是写法上面，**两个string的比较直接用compareTo**。

### 面试题46：把数字翻译成字符串

这道题目一开始确实是想到了可以递归走，就是从前面向后每次读一位或者两位，然后把读一位和读两位的数量加一起统计。（这里如果从前往后算的话(其实是i从大到小不行，如果从前往后也是i从小到大那也没事)会有重复子问题的计算，因为f(i-1)和f(i-2)会重）

leetcode题解给的是动态规划的解法，其实思路就是上面的一位和两位合并。

```java
class Solution {
    public int translateNum(int num) {
        String src = String.valueOf(num);
        int p = 0, q = 0, r = 1;
        for (int i = 0; i < src.length(); ++i) {
            p = q; 
            q = r; 
            r = 0;
            r += q;  // 当前位单独读
            if (i == 0) {
                continue;
            }
            String pre = src.substring(i - 1, i + 1);
            if (pre.compareTo("25") <= 0 && pre.compareTo("10") >= 0) {  // 直接用字符串去比较，用数字比较应该是要截出来两位太麻烦了
                r += p;  // 当前位和右边一位一起读
            }
        }
        return r;
    }
}
```

动态规划方程：（用 f(i) 表示以第 i 位结尾的前缀串翻译的方案数）

![image-20220612214241731](../images/LeetCode的菜鸡实录.assets/image-20220612214241731.png)

动态规划边界条件：f(-1)=0；f(0)=1

整个移动的过程用滚动数组的方式完成。

本题实现（2022/08/24）：

Wtf，居然过了道dp

```java
class Solution {
    public int translateNum(int num) {
        char[] array = Integer.toString(num).toCharArray();
        int length = array.length;
        int[] dp = new int[length + 1];
        dp[0] = 1;
        for(int i = 1; i <= length; i++) {
            dp[i] = dp[i-1];
            if (i - 2 >= 0) {
                if (array[i-2] == '1') {
                    dp[i] += dp[i-2];
                } else if (array[i-2] == '2' && array[i-1] >= '0' && array[i-1] <= '5') {
                    dp[i] += dp[i-2];
                }
            }
        }
        return dp[length];
    }
}
```

这个想到的思路是，以当前位为结尾的翻译方式的种数=当前位单独翻译的种数+当前位和前一位一起翻译的种数。其实也就是上面截图的动态转移方程。知道了这个加上初始条件的设置之后就思路上没啥难度了，后面无非是写法上的选择。String的compareTo是个好东西，还是得记得要会用。

题解里面还有一种不需要额外空间的做法，直接在数字上进行处理（上面的方法不管是char数组还是String字符串都是还要$O(n)$的额外空间的）

```java
class Solution {
    public int translateNum(int num) {
        int a = 1, b = 1, x, y = num % 10;
        while(num != 0) {
            num /= 10;
            x = num % 10;
            int tmp = 10 * x + y;
            int c = (tmp >= 10 && tmp <= 25) ? a + b : a;
            b = a;
            a = c;
            y = x;
        }
        return a;
    }
}
```

### 面试题47：礼物的最大价值

第一反应是回溯法（也就是暴力），不过书上给的解法是动态规划。然后按照动态规划的思路想了一下，确实是可以的，因为到某一格的方法就只有两种，要么是左边的格子往右走一格，要么是上边的格子往下走一格。

动态规划方程：$f(i,j)=max(f(i-1,j),f(i,j-1))+gift[i,j]$

按照这个如果直接是用二维数组处理还是蛮简单的，唯一要注意的就是把超过边界（最左边和最上边）的情况都排除掉（就是0）。然后从空间上来说还可以进一步优化，因为每次的关系只依赖于上一行，因此不需要把所有行都保存下来，只需要一行的额外空间用来给动态规划操作就可以了。左边就是直接取j-1，上边就是直接取j，因为这时候还是老的，新的还没有更新进去，在比较完之后更新了才填到当前的j上面。

本题实现（2022/08/24）：

```java
class Solution {
    public int maxValue(int[][] grid) {
        int length = grid[0].length;
        int[] dp = new int[length];
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < length; j++) {
                if (j == 0) {
                    dp[j] += grid[i][0];
                } else {
                    dp[j] = Math.max(grid[i][j] + dp[j-1], grid[i][j] + dp[j]);
                }
            }
        }
        return dp[length - 1];
    }
}
```

也是个dp，然后要么是上面的往下走要么是左边的往右走，没什么花头。就是初始条件注意下，其实一开始全0就行。

### 面试题48：最长不含重复字符的子字符串

这道题目也是动态规划的问题。（我真的会谢，整这些动态规划认么又认不出，只能背模板，吐了）

**定义函数$f(i)$表示以第i个字符为结尾的不包含重复字符的子字符串的最长长度。**这个定义的方式好像是常规套路了，**以第i个xx为结尾的xxx**。

然后如果当前字符之前没出现过就+1，如果出现过需要讨论，一种是出现在当前统计的字符串里面，那么最长就是距离上一个字符的长度，另外一种是上一个字符不在现在统计的字符里面，这时候就是现在统计的字符串的长度。

这里书上为了统计出现是直接见了一个26个字符的数组用来存放上一次出现的下标。

这个咋说呢，说着很唬人是动态规划，实际上就是正常的走一遍扫描的思路，然后要注意位置的更新和是否出现在当前统计的字符串内的判断就是了。

主要的技巧：**新建26个字符串的空间**，**存储字符最后一次出现的下标**，**字符重复后的长度缩减更新**

## 5.3 时间效率与空间效率的平衡

### 面试题49：丑数

这道题目其实一开始想到了一点，因为是原本数再乘2，3，5得到的后面更大的，但是并没有想到是要都存下来。

首先是暴力，不知道为啥，有时候反而最简单的暴力想不到了，其实就是从前往后遍历每个数，然后如果这个数是丑数就计数++，一直到最后找到。

不暴力的方法就是要通过规律去找了，因为是只包含因子2，3，5。那么也就是说，假设已经知道几个丑数了，后面的丑数肯定是前面的丑数去乘2，3，5得到的。然后一开始自己想的时候就变成考虑是前面的哪个数乘了正好是新的最大的，这个估计要用什么规律或者算法判断下。其实书上给的解法就是直接存，全存了就完事了，然后在存的里面比。

因此解法就成了，从1开始（1定义为丑数），然后保存三个变量，分别标注之后乘2的已有的数，之后乘3的已有的数，之后乘5的已有的数。每一次都是从这三个变量里面取了值做乘法然后进行比较，最小的那个就是要新添加的。然后对这三个值进行更新，更新到最小的乘了各自的比例超过当前最大丑数的丑数。（有点拗口，就是新的丑数肯定要比现在最大的丑数还要大，所以先把几个基准数一直增加，知道可以创建出更大的丑数为止）然后最大的丑数是三个都有可能的，那么就在最后插进数组里的时候再比较哪个最小就行了。

知道思想之后应该还不算难实现，核心的就是要**存下来之前的丑数**，然后需要如何**尽量少的做乘法**（三个变量存了每次增，肯定比每次把所有丑数都乘完再找最小的合适）。

### 面试题50：第一个只出现一次的字符

#### 题目一：字符串中第一个只出现一次的字符

这个肯定是要从前往后一遍扫完的嘛，如果用暴力的方式，每一位都往后扫，那时间复杂度$O(n^2)$肯定g。

然后这里是字符，一开始想得是，英文字符就26个，直接开个大小为26的数组就完事了，但是后来看书上的内容，这个字符并不是只有英文字符，还有别的标点啥的，那就变成了ASCII字符了，这玩意是8位的（不过好像最高位不用？是奇偶校验位）。反正就是要注意，如果题目说是字符，没有说是英文字符或者特指数字这种，那么最合理的就是当成ASCII码的范围来解决。

当然了，这里就算是有了辅助的空间，也不能直接一次扫描就出来，本来是想着用-1，1，0这种状态表示的，但是后来看了书上的思路之后发现，这样一次遍历只能找到之前出现过的，没法找到只出现一次的里面哪个是第一次出现。因此还是需要扫两遍，第一遍用来统计出现次数，第二遍再根据出现次数去遍历一次，找到哪个是第一次出现的。（感觉要是这个字符很长，可以考虑扫统计的数组，统计的数组对于第一次出现的当个正，出现一次以上的当个负，没出线的0或者负都ok，然后存的内容就是下标，这样扫一遍，比较哪个正的下标最小就知道是哪个结果了；当然如果字符串的长度不长，可能还是直接遍历字符串会比较省事。这个具体用哪个当成遍历的对象还是要考虑哪个更长，大数问题那种？下面题目二的思路有点像，还可以解决字符流这种的情况）

另外，因为开的是固定的256大小的，所以空间复杂度还是$O(1)$，时间复杂度就算扫了两遍也是$O(n)$。

***本题扩展**：如果不止256个字符还包括汉字（字符数量更多）怎么解决？*

*没搜到...想不到啥合适的，不过应该扩大空间还是能做的？*

**相关题目**：

三道题目都是空间换时间的。比较典型的一点在于，使用空间换时间的方式来处理，似乎大部分的都需要扫描两遍？存储+查找

另外注意一下第三道题目的思想，可以先遍历第一个统计次数++，然后再遍历第二个统计次数--，也是一种哈希表的用法。

**==举一反三：如果需要判断多个字符是不是在某个字符串里出现过或者统计多个字符在某个字符串中出现的次数，那么我们可以考虑基于数组创建一个简单的哈希表，这样可以用很小的空间消耗换来时间效率的提升。==**

#### 题目二：字符流中第一个只出现一次的字符

这个就又和上面不一样了，上面是一次性全给，这个是流，一次给一个。

这个的解法思路和上面题目一里提到的自己的一个想法有点像，就是用正负和数值去标注一下状态，然后对于存储的就直接用下标，这样后面在搜索的时候就直接搜辅助空间的内容就行，不会受到是字符流输入的影响。

别的就没啥问题了，代码看书上吧。

### ==面试题51：数组中的逆序对==

这个题反正是除了暴力自己一开始没想到什么别的做法。

[leetcode题解](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/solution/jian-zhi-offer-51-shu-zu-zhong-de-ni-xu-pvn2h/)，跟书上的其实思路是一样的，可能图画得更多稍微好里节点。

「归并排序」与「逆序对」是息息相关的。归并排序体现了 “分而治之” 的算法思想。

整体的思想应该算是，先把数字打碎成最小的一个一个的情况。然后再从最小的开始慢慢往上排序。（这里要注意下，其实到一个的时候是直接返回的，进行排序处理的是已经从一个弹到上面两个的时候处理的排序）然后先对小的进行排序，再对大的排序，大的排序的时候因为已经是部分有序了，所以比较到一定的临界条件之后，后面的都可以直接塞就ok。（无序的必须遍历，有序的有机会省，比如二分啥的）

代码：

```java
class Solution {
    int[] nums, tmp;
    public int reversePairs(int[] nums) {
        this.nums = nums;
        tmp = new int[nums.length];  // 辅助空间
        return mergeSort(0, nums.length - 1);
    }
    private int mergeSort(int l, int r) {
        // 终止条件
        if (l >= r) return 0;
        // 递归划分
        int m = (l + r) / 2;
        int res = mergeSort(l, m) + mergeSort(m + 1, r);
        // 合并阶段
        int i = l, j = m + 1;
        for (int k = l; k <= r; k++)
            tmp[k] = nums[k];
        for (int k = l; k <= r; k++) {
            if (i == m + 1)
                nums[k] = tmp[j++];
            else if (j == r + 1 || tmp[i] <= tmp[j])
                nums[k] = tmp[i++];
            else {
                nums[k] = tmp[j++];
                res += m - i + 1; // 统计逆序对
            }
        }
        return res;
    }
}
```

怎么说呢，看着代码回过头去想还是能理通的，但是直接想确实想不到。反正记住**==归并排序与逆序对息息相关==**。时间复杂度$O(nlogn)$，空间复杂度$O(n)$。

### 面试题52：两个链表的第一个公共节点

这道也是经典题目了吧

两个链表有公共节点的特点：从这个公共节点往后，两个链表中的节点内容是完全一样的。也就是说，两个链表的尾巴是粘在一起的。

暴力法：遍历第一个链表，在第一个链表的每个节点的访问过程中，对第二个链表进行遍历，查找其中相同的节点。如果找到了相同的节点，那么就是第一个公共节点。如果第一个链表的长度为m，第二个链表的长度为n，那么显然该方法的时间复杂度为$O(mn)$。

如果不用暴力法，那么就要需要利用到两个链表有公共节点的特点。如果是从后先前遍历，那就能很方便的找到第一个不同的节点，那么其后一个节点就是第一个公共节点。由于链表本身是单向链表，要想获得这种倒序的形式，可以利用栈这个数据结构。先将两个链表的所有节点全部入栈，然后一对一对弹出，知道找到最后一个相同的节点。这种思想的解决方案，空间复杂度为$O(m+n)$，时间复杂度为$O(m+n)$，相当于是空间换时间。

如果只想使用$O(1)$的空间复杂度，那么就不能使用栈。可以先遍历两个链表，确定各自的长度。然后对长的链表，其头指针先走两个链表的差值，这样两个指针就会同时走到末尾，也会同时走到第一个公共节点。这种方式的时间复杂度为$O(m+n)$，但是不需要辅助栈，空间复杂度$O(1)$。

**相关题目**：求两个节点的最低公共祖先（7.2节有，这里先省略）

## 5.4 本章小结

降低时间复杂度的第一种方法是改用更加高效的算法。

降低时间复杂度的第二种方法是用空间换取时间。

- 哈希表
- 创建缓存保存中间计算结果

在用递归的思路求解问题的时候，如果有重复的子问题，那么我们也可以通过保存求解子问题的结果来避免重复计算。

## 6.3 知识迁移能力

### 面试题53：在排序数组中查找数字

#### 题目一：数字在排序数组中出现的次数

对于排序数组，应该很自然地联想到二分查找算法。不过一般的二分查找算法是从一个不含重复元素的数组中查找某一个数字，而这里的目标是要确定重复出现数字的次数。

第一种思路是先用二分查找在数组中找到这个目标数字，不管这个数字是出现在哪里，然后再线性地沿着向前和向后两个方向依次找到头和尾。这种方式在最坏的情况下会达到$O(n)$的时间复杂度。

观察第一种思路可以发现，时间的消耗主要是在找到数字的起始和终止位置上，如果可以使用二分算法找到起始和终止位置那就最好了。这个其实是可以实现的，对每次判断是否找到这个数字的条件添加判断条件，当这个数字是起始或者这个数字之前的数字和现在不同，那么就是起始位置。相对的，如果这个数字是最后一个数字或者这个数字之后的数字和现在的数字不同，那么就是终止位置。

这个代码应该没啥问题，主要是注意当找到的不是第一个的时候，还需要end=middleIndex-1，另外找到的不是最后一个的时候，需要start=middleIndex+1。还有就是可能的异常情况，比如要找的数不存在不存在？

第二种实现的思路时间复杂度是$O(logn)$，其中GetFirstK和GetLastK两个函数的时间复杂度都是$O(logn)$。

#### 题目二：0~n-1中缺失的数字

这个看到第一反应就是要找第一个和下标不匹配的数字呗。

那个暴力的思路倒是一开始没想到，先要求所有的和，然后再全遍历一遍加的和，最后可以直接得到少多少。（这种有序的题应该都不会是$O(n)$吧，就算有些是部分有序的题目都要比$O(n)$低了；**但是这个思路可以用来处理给定范围无序数组的情况**）

代码实现里面主要是边界情况的判断。

#### 题目三：数组中数值和下标相等的元素

这个知道是二分的问题的话也蛮简单的。单调递增嘛，所以如果本身的值小于下标，那么就一定在右边，如果本身的值大于下标，那么就一定在左边，然后二分就完事了。

代码看书上吧，很简单的。

### 面试题54：二叉搜索树的第k大节点

什么鬼，本来还在想着怎么在二叉搜索树里面直接找到，那就成了要知道左右子结构里面有多少节点的问题了...这样的话感觉不大好做

最简单的无非就是中序遍历拿到一个有序数组，然后直接取值就行了。

当然了，如果是要用数组存，肯定还得整出来额外的空间，为了把这个空间也省了，完全可以在遍历的时候就拿到。

这里要注意下，书上这个第k大的定义...居然是从小到大，那就左根右吧，如果是从大到下的，那就右根左遍历。然后拿到的数字填在数组的哪个位置上，其实就是遍历打印的顺序，因此完全可以在打印的地方计数，等到了就直接拿了返回就行了。

**别的没啥难的，主要就是理解二叉搜索树升序（左根右），二叉搜索树降序（右根左）。然后==如果只是想知道第几个，那直接在里面计数等到了打出来就行了，没必要存到数组里面。下标就是代表打/读的第几个。==**

### 面试题55：二叉树的深度

#### 题目一：二叉树的深度

这个其实就是深度优先遍历然后找最深的路径。深度优先遍历可以用递归完成。当然这里可以不用栈这个额外的数据结构占空间，直接相当于在回退的时候比较左右子树哪个高就ok了，深度肯定是由左右子树里面更高的那个决定的。递归的终止条件就是递归到了空指针，这时候的高度为0，每个节点取高度的时候就是左子树高度+1和右子树高度+1比哪个更大，取更大的。

#### ==**题目二：平衡二叉树**==

这个问题其实最容易想到的就是用上面的方法，然后把每个节点的判断条件改成左边是不是平衡二叉树&&右边是不是平衡二叉树。

不过使用之前这种思路有个问题，就是会产生重复的计算。（应该是TreeDepth函数里面遍历了，然后IsBalanced里面又做了遍历）

为了要让每个节点只遍历一次，需要使用后序遍历。

看这个[leetcode题解](https://leetcode.cn/problems/ping-heng-er-cha-shu-lcof/solution/mian-shi-ti-55-ii-ping-heng-er-cha-shu-cong-di-zhi/)吧，两种情况说的比较清楚，重复子问题（自顶向下）

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return recur(root) != -1;
    }

    private int recur(TreeNode root) {
        if (root == null) return 0;
        int left = recur(root.left);
        if(left == -1) return -1;
        int right = recur(root.right);
        if(right == -1) return -1;
        return Math.abs(left - right) < 2 ? Math.max(left, right) + 1 : -1;
    }
}
```

### 面试题56：数组中数字出现的次数

#### **==题目一：数组中只出现一次的两个数字==**

按照书上的思路，首先先拆解成数组中只出现一次的一个数字。要解决这个问题，需要知道异或运算的四个规律（交换律、恒等律、归零律、结合律）。由于具有交换律和结合律，所以如果把数组中的所有数字都做异或运算是可以随意进行组合的。又因为异或运算具有归零律，因此相同的两个数字进行运算所获得的结果一定是0。通过这些性质，如果数组中只有一个出现一次的数字，而别的数字都是出现两次的，只需要全部连在一起进行异或运算就可以知道最后留下来的数字了。

然后就是对于有两个数字只出现一次的情况，一开始想得是怎么去二分范围啥的，但是复杂度应该也满高的。书上的思路是这样的，还是对所有的数放一起做异或，然后两个只出现一次的数字的异或结果肯定不是全0，至少在某一位上是不同的。那么就可以对于数组中的数，按照这一位上是1还是0分成两部分，不同的两个数一定会分别在两个部分，并且出现两次的数字一定都在同一部分。这样再分别求两个数组中的数字，就可以得到两个只出现一次的数字。

这个主要还是思路上没想到，另外也不熟悉异或运算，异或运算的几个性质需要记住。

[leetcode题解](https://leetcode.cn/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/solution/jian-zhi-offer-56-i-shu-zu-zhong-shu-zi-tykom/)

leetcode代码：

```java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int x = 0, y = 0, n = 0, m = 1;
        for(int num : nums)               // 1. 遍历异或
            n ^= num;
        while((n & m) == 0)               // 2. 循环左移，计算 m
            m <<= 1;
        for(int num: nums) {              // 3. 遍历 nums 分组
            if((num & m) != 0) x ^= num;  // 4. 当 num & m != 0
            else y ^= num;                // 4. 当 num & m == 0
        }
        return new int[] {x, y};          // 5. 返回出现一次的数字
    }
}
```

书上的代码异常处理啥的做的更好一点，但是那个右移总感觉不是很保险，上面leetcode的这个代码用左移感觉好点，但是一些输入异常没判断啥的。

#### 题目二：数组中唯一只出现一次的数字

除了一个数字只出现一次，别的数字都出现了三次。

这道题目其实思路感觉沾到一点点边了。想到是加起来除3这种，但是其实还是没有完全反应过来，如果想到位运算上面就很接近了。把每一位上面的值（0，1）全部加起来，然后如果这一位上的值最后能被3整除，多出来的数字这一位上就是0，如果没法整除，就是1（应该理论上也只会余1）。

说到底也是位运算，如果知道位运算可能会好想很多。

实现代码书上和[leetcode](https://leetcode.cn/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/solution/mian-shi-ti-56-ii-shu-zu-zhong-shu-zi-chu-xian-d-4/)的都行。主要是要注意在统计的过程中，每一位求和如果是从低到高，那么存放顺序就是先最低位的值，取出来的时候也是先取最低位的值，如果要用左移直接算的话，可以先取最高位，然后每步左移，会方便点。

leetcode代码：

```java
class Solution {
    public int singleNumber(int[] nums) {
        int[] counts = new int[32];
        for(int num : nums) {
            for(int j = 0; j < 32; j++) {
                counts[j] += num & 1;
                num >>>= 1;
            }
        }
        int res = 0, m = 3;
        for(int i = 0; i < 32; i++) {
            res <<= 1;  // 左移后，最低位为0
            res |= counts[31 - i] % m;  // 0和当前位上的余数取或，当前位上位1，则是1，否则是0
        }
        return res;
    }
}
```

**==总结：出现两次用异或，出现两次以上用位运算取余。==**

### 面试题57：和为s的数字

#### 题目一：和为s的两个数字

经典题型，因为是递增排序，所以不需要扫多次或者用什么哈希表去存，直接双指针从头尾开始往中间遍历就完事了。如果头+尾太小，就把头往右动，如果头+尾太大，就把尾往前动。因为本身就是递增有序的，所以很好处理。最后头尾碰到了就终止。时间复杂度$O(n)$

#### **==题目二：和为s的连续正数序列==**

这道题目呢，稍微变了一点。如果还是从头尾开始，那么当找到第一个符合要求的内容之后，下一个不是很好确定，因为不是只要找一个，所以单调的往中间缩肯定会漏。要想找到所有的，其实肯定是要按照某一个规则去单向确定下来的，如果从这个序列起始的角度来说，那就肯定是从前往后遍历。

这个规则其实是一开始用前两个初始化，然后太小了就把右边的右移，太大了就把左边的左移。（这样的话相当于都是不回头的）然后左边的这个肯定不会超过(1+s)/2，因为是连续正数序列，还是递增的。（这里不用s/2应该是怕不整除，5/2=2这种）

**==双指针除了可以从头尾开始往中间走，两个都从头开始走也是一种在有序数组中找子序列的思路。==**

求连续序列的和技巧：通常我们可以用循环求一个连续序列的和，但考虑到每次操作之后的序列和操作之前的序列相比大部分数字都是一样的，只是增加或者减少了一个数字，因此我们可以在前一个序列的和的基础上求操作之后的序列的和。这样可以减少很多不必要的运算，从而提高代码的效率。

*这个规则好像对不连续的递增序列也能用？*

[leetcode题解](https://leetcode.cn/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/solution/mian-shi-ti-57-ii-he-wei-sde-lian-xu-zheng-shu-x-2/)：书上的是它的思路三

### 面试题58：翻转字符串

#### 题目一：翻转单词顺序

这道题目一开始反应是直接按空格把字符串切成几个小的字符串，然后在头尾交换。确实这样也可以做，而且复杂度貌似应该是一样的吧。这道题目真得要考得是**==两次翻转字符串==**这个点。其实这个思路之前好像是见到过的，但是看题的时候没联想起来。首先是对整个序列进行一个翻转，随后再对每个单词进行翻转（找单词的方式也就是通过空格去找）。

实现上注意指针指的是位置，对数组整体倒序是交换的值，确定这点就行了。然后后面移动指针的时候注意情况判断。

**==最重要的还是两次翻转字符串这个点。==**

#### 面试题二：左旋转字符串

这道题目题面和面试题11很像。

如果有前面那道题目做引子，这道题目的思路还是蛮容易想到的。如果没有的话，就得记住，对于字符串的翻转存在两次翻转字符串这种思路。观察题目要求可以发现，这不就是把ab看成一个单词，后面的看成另一个单词嘛，然后就可以用前面的思路。并且，如果是先翻转每个单词，再翻转整体其实结果是一样的。（前面的题也满足，其实就是前后顺序不影响结果，只要是一次整体一次局部就行）。

然后就没啥了，用前面的翻转函数，先翻前两个，然后翻后面几个，最后再把整体一翻就完事了。注意每一部分指针的起讫就ok。还有输入异常的处理。

### 面试题59：队列的最大值

#### 题目一：滑动窗口的最大值

这个好像算法课的时候做过？

一个队列用于维护最大值。从尾部插入新值，从头部取最大值或者出队列。如果新插入的值比之前的大，那就把前面小于的全踢了（从屁股后面出来，因为不一定是都比新进来的小，只踢比当前的小的），如果新的这个没有之前的大，那么先留着，因为前面的出去之后这个有可能会成为最大的。当窗口已经移过当前最大值的时候，将目前的最大值出队列，然后下一个就是新的最大值（**用下标维护**）。

代码看书上的，**进出队列的顺序**、**判断是否窗口已经移过**、**用下标维护**。

#### 题目二：队列的最大值

思路和上面的一样，而且不用维护窗口了。略

## 6.4 抽象建模能力

### ==面试题60：n个骰子的点数==

[leetcode题解](https://leetcode.cn/problems/nge-tou-zi-de-dian-shu-lcof/solution/jian-zhi-offer-60-n-ge-tou-zi-de-dian-sh-z36d/)

这个感觉leetcode那个讲得可能更好一点，方便理解。书上的解法一其实就是暴力，只是这个暴力要怎么做，是用递归去实现。因为总的可能情况数是固定的，所以只需要知道每个和的情况然后去除总数就能知道概率。

使用解法二的改进方法进行处理，那么就只需要$O(n^2)$的时间复杂度。

就是用上一次的结果加上当前次投出1~6的几种不同的可能情况，然后就能得到新的总的和的结果。这样相当于从前向后，从投1个骰子到投n个骰子。

详细的看leetcode题解吧，这个不知道怎么总结比较合适，不过暴力和后面这种方法区别是肯定有的。后面的改进方法相当于是屏蔽了前面的枚举情况在做，不过前面具体哪次投出了什么，只要和算出来都当一起算，所以避免了不必要的一些计算。

### 面试题61：扑克牌中的顺子

一开始以为是统计概率，后来看了leetcode上面的例子才知道就是给一串数字进行判断。

一开始看书上什么0填空缺啥的感觉思路好烦，后来自己想的时候感觉这不就是最大-最小要小于5嘛。中间的所谓补什么也是满足这个要求实现的。

[leetcode题解](https://leetcode.cn/problems/bu-ke-pai-zhong-de-shun-zi-lcof/solution/mian-shi-ti-61-bu-ke-pai-zhong-de-shun-zi-ji-he-se/)给了两种思路，主要差别是在是否需要排序上面。如果不能用额外的空间，那么为了确定没有重复就得排个序，不然直接用个HashSet就能完成去重了。

### **==面试题62：圆圈中最后剩下的数字==**

经典题目，经典的看了就只能模拟。

两种方法，一种是模拟，一种是动态规划。

模拟这里就不细写了，主要是数据结构的使用，还有如何把头尾接起来。

动态规划：先不管后面的删除，只考虑第一次删除写出形式

$(m-1)\%n$

因为下标从0开始，所以是m-1，另外由于m可能大于数字的个数n，所以需要用取余。

在确定了第一轮的形式之后，下一轮从删除的节点的后一个开始。如果删除的节点是k，那么下一次就是把k+1当成初始的0去寻找。然后原来的n-1对应新的n-k-2，原来的0对应新的n-k-1，最后的k-1对应n-2。

观察这个式子可以发现原本的下标和新的下标的映射关系就是

$(x-k-1)\%n$

通过这个映射规则，可以得到如下的递推关系：

![image-20220620130812444](../images/LeetCode的菜鸡实录.assets/image-20220620130812444.png)

然后就可以使用循环实现了，代码还是蛮简单的。

### 面试题63：股票的最大利润

这道题目思路还是比较好想的，只是要最大利润没要考虑持有时间啥的（有持有时间的话感觉就类似与leetcode11盛最多水的容器）。那么要想知道最大利润无非就是要知道在前面的最低价是多少。所以可以用一个变量维护这个最低价。当前的价格如果小于这个最低价就更新，如果大于这个就看看利润是不是比之前的都多，更新最大利润。

书上的这个分i-1更新最小值，i计算最大利润应该是害怕单调递减的情况？如果初始化为0，然后更新最小值和利润都在一位处理，那么利润就会是初始设置的值，分两个就没这种情况。

## 6.5 发散思维能力

### 面试题64：求1+2+...+n

这个不属于常规题了，书上的几种思路都是针对c和c++的，还是参考[leetcode题解](https://leetcode.cn/problems/qiu-12n-lcof/solution/qiu-12n-by-leetcode-solution/)吧。

两种题解的思路可以按照是否使用等差数列求和公式进行划分。

第一种思路使用递归去做，这种就是不依靠等差数列求和公式，直接模拟一个个数加的过程。但是一般的递归方法都是需要if语句去设置递归终止条件的，是否有别的方法去绕过去。

```java
class Solution {
    public int sumNums(int n) {
        boolean flag = n > 0 && (n += sumNums(n - 1)) > 0;
        return n;
    }
}
```

可以通过与运算的性质来实现这一目标。当与运算的前半部分为真时，后半部分的内容才会执行，当前半部分已经为夹，后半部分不会再运行。（同时这个代码里面要保证后半部分一定为真）

**==if的条件判断可以通过与运算的性质部分替代==**

第二种思路是需要利用到求和公式的。这个其实想到一点了，只说不能乘，没说不能位运算啊，左移就是乘2嘛。不过自己想的时候只是到了这一步，还没有完全相通。其实对于乘法就是多个数之和嘛，位运算虽然不能处理*3，但是可以拆成*2和*1相加，主要就是这个相加一开始没想通。然后其实就很简单了，直接解决实现的问题，按公式就ok。

```java
class Solution {
    public int sumNums(int n) {
        int ans = 0, A = n, B = n + 1;
        boolean flag;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        flag = ((B & 1) > 0) && (ans += A) > 0;
        A <<= 1;
        B >>= 1;

        return ans >> 1;
    }
}
```

而且基于位运算来做的话，时间复杂度和空间复杂度都是更低的，$O(logn)$和$O(1)$。

这个14次重复代码是手动展开循环，然后因为给了数值范围确定不超过14位，所以有14个。

### 面试题65：不用加减乘除做加法

这种要实现加法肯定就是要靠位运算。观察二进制加法的规律可以发现，本身数位上的数可以用异或得到，进位用与得到然后左移一位，将进位和本身数位的结果加在一起就可以得到最终的结果了。这样相当于完全把加法用位运算完成了。这里面要注意存在迭代的过程，因为当进位和本身数位上的数进行加法的时候，仍然可能产生进位，所以需要一直迭代，直到最后不存在进位为止。

相关问题：不使用新的变量，交换两个变量的值。

> 基于加减法
>
> ```java
> a = a + b;
> b = a - b;
> a = a - b; 
> ```
>
> 基于异或运算
>
> ```java
> a = a ^ b;
> b = a ^ b;
> a = a ^ b;
> ```

### 面试题66：构建乘积数组

因为不能用除法，所以只能进行乘。

然后为了避免暴力乘，肯定需要联系前后之间的关系。将乘积项按照下标分为左右两部分。对于左侧的部分可以前往后乘，拿上一个的值乘上当前值得到；对于右侧的部分可以从后往前乘，拿后面一个的值乘上当前值得到。然后两部分乘一起就ok了。

按照[leetcode题解](https://leetcode.cn/problems/gou-jian-cheng-ji-shu-zu-lcof/solution/mian-shi-ti-66-gou-jian-cheng-ji-shu-zu-biao-ge-fe/)下面评论的说法，本质上是维护了两个动态规划数组，然后在书上和题解的实现里面都节省了空间就地处理。

```java
class Solution {
    public int[] constructArr(int[] a) {
        int len = a.length;
        if(len == 0) return new int[0];
        int[] b = new int[len];
        b[0] = 1;
        int tmp = 1;
        for(int i = 1; i < len; i++) {
            b[i] = b[i - 1] * a[i - 1];
        }  // 先把左边的每种全部算完，存在最终结果的对应位置，这样算右边的时候直接调就可以
        for(int i = len - 2; i >= 0; i--) {
            tmp *= a[i + 1];
            b[i] *= tmp;  // 这个b[i]里面已经含了左半部分，tmp是右半部分的乘积
        }
        return b;
    }
}
```



# 剑指offer专项突破

## 1.1 整数的基础知识

### 面试题1：整数除法

题目要求不允许使用乘、除、取余运算。要想实现除法只能从最基本的减法入手实现。（**乘法的基础是加法，除法的基础是减法**）当然肯定不会是暴力减然后算格式。这里确实也想到了类似快速幂的处理方式，但是一开始并没有想到是基于除数往上算2的幂次的，一开始还在想怎么拆分成除2或者除4那种，这样相当于是右移嘛。

因为是减法，又要节省时间，所以就是基于除数用类似快速幂的方式去找一个当前允许的最大的数减去，然后把减的次数统计出来。思路上到这边就可以基本确定了。

然后就是实现的细节。首先int的范围是$-2^{31}$~$2^{31}-1$，也就是说正数和负数的范围还不一样，**负数的范围大1**。所以在处理正负的时候，把正数都转换成负数处理不会有问题，但是把负数转换成正数处理有可能溢出。（**有条件的情况下都把正的往负的转**）此外就是正负号的判断，还有是负数的比较以及溢出。

## 1.2 二进制

### 面试题2：二进制加法

这道题目其实一开始看到的时候想到的是以前的做与还有异或啥的去进行加法的问题。不过这边因为输入是字符串，本身就可能超过任何固定的长度范围，所以只能一位一位计算。（从最低位往最高位计算）计算的规则其实还是比较好理解的，需要注意的地方一个是超过某一个字符串的长度之后，其中一位要是0，另外一个就是不能忘记带进位运算。最终的结果因为先计算最低位，所以插入的字符串里面也会是最低位在最前面。这个只需要最终结束输出之前记得reverse()倒序一下就ok。（还有就是StringBuilder的方法使用）

### 面试题3：前n个数字二进制形式中1的个数

这个说实话，最基本的都没想到。最基本的就是**i&(i-1)可以去除i中处于最低一位上的1**。有多少个1就是做了多少次这个运算。

不过如果光是这样还是会有重复计算在里面。因为如果从小往大算，当前数字的1一定比之前的某个数字多一个，而且正好是在最低位上的。所以有这个特殊的关系：$result[i] = result[i\&(i-1)] + 1$。这个思路实际上和费波那契数列是一样的，就是避免重复运算。同样的方式还有另一种思路实现：$result[i] = result[i>>1] + (i\&1)$。这个就是说最后一位上的奇偶决定了比当前数右移一位是多一个1还是相同。（当然这里要注意题目是给了非负数的限制了，如果是负数，这时候java如果做的是带符号右移，即">>"，那么前面补的就是1；如果java做的是无符号右移，即">>>"，那么前面补的就是0）

总之，不管是以上哪一种形式，**究其根本都是为了避免重复运算，所以在解决这些连续的问题之前，有必要先思考一下是不是前后的问题能否直接关联，这样就可以避免重复子运算**。（从低到高的dp转移？）

### 面试题4：只出现一次的数字

这道题目其实就是另外一道的变型。原本最基础的题目是一个数组中，除了一个数字只出现一次，别的数字都出现两次。解决这个基础问题的方式，可以通过运用异或运算的性质实现。

这道题目里面除了一个数字出现一次，别的都是出现三次，所以还是可以用类似位运算的形式，每一位分开来算。只出现一次的数字必定会有至少一位上为1，那么只需要把所有位的结果%3，最后把结果拼起来就是只出现一次的那个数字。

#### 同类型问题：寻找在1~n中没有出现的一个数字

这个其实也是参考位运算的思想，加上还用了异或的性质去解。首先可以在原本的数组后面拼上去1~n这n个数字，这样就能得到一个包含2n-1个数字的数组。在这个数组中，只有原本缺少的那个数字是只出现一次的，别的都是出现了两次的，所以通过异或运算就能够得到原本没有出现的那个数字。

### 面试题5：单次长度的最大乘积






# LeetCode top100

## [1. 两数之和](https://leetcode.cn/problems/two-sum/)

hashmap扫一遍存下来

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer,Integer> hashMap = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (hashMap.containsKey(target - nums[i])) {
                int[] ans = new int[2];
                ans[0] = hashMap.get(target - nums[i]);
                ans[1] = i;
                return ans;
            } else {
                hashMap.put(nums[i], i);
            }
        }
        return new int[2];
    }
}
```

## [2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)

链表逐位从低向高加，唯一要注意的就是进位，在最顶上一位，两个链表本身可能都已经为空了，但是如果此时进位还是存在的话那么就需要多加一位。

下面这个是分成三个阶段：l1和l2同时存在，仅存在l1或l2，l1和l2均空仅判断进位。

~~~java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        int temp = 0;
        ListNode curNode, preNode = null, headNode = null;
        while (l1 != null && l2 != null) {
            int cur = l1.val + l2.val + temp;
            curNode = new ListNode(cur % 10);
            if (preNode != null) {
                preNode.next = curNode;
            } else {
                headNode = curNode;
            }
            temp = cur / 10;
            preNode = curNode;
            l1 = l1.next;
            l2 = l2.next;
        }
        while (l1 != null) {
            int cur = l1.val + temp;
            curNode = new ListNode(cur % 10);
            if (preNode != null) {
                preNode.next = curNode;
            } else {
                headNode = curNode;
            }
            temp = cur / 10;
            preNode = curNode;
            l1 = l1.next;
        }
        while (l2 != null) {
            int cur = l2.val + temp;
            curNode = new ListNode(cur % 10);
            if (preNode != null) {
                preNode.next = curNode;
            } else {
                headNode = curNode;
            }
            temp = cur / 10;
            preNode = curNode;
            l2 = l2.next;
        }
        if (temp != 0) {
            curNode = new ListNode(temp);
            if (preNode != null) {
                preNode.next = curNode;
            } else {
                headNode = curNode;
            }
        }
        return headNode;
    }
}
~~~

下面这个是题解的分成两阶段的方法：l1或l2中只要有节点存在，l1和l2节点均不存在仅处理进位。

~~~java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = null, tail = null;
        int carry = 0;
        while (l1 != null || l2 != null) {
            int n1 = l1 != null ? l1.val : 0;
            int n2 = l2 != null ? l2.val : 0;
            int sum = n1 + n2 + carry;
            if (head == null) {
                head = tail = new ListNode(sum % 10);
            } else {
                tail.next = new ListNode(sum % 10);
                tail = tail.next;
            }
            carry = sum / 10;
            if (l1 != null) {
                l1 = l1.next;
            }
            if (l2 != null) {
                l2 = l2.next;
            }
        }
        if (carry > 0) {
            tail.next = new ListNode(carry);
        }
        return head;
    }
}
~~~

## [==3. 无重复字符的最长子串==](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

思路有问题，确实是想到了用hashmap空间去换时间，但是处理上有问题。（每步都需要刷新最长长度）

**==滑动窗口==**

~~~java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s.length()==0) return 0;
        HashMap<Character, Integer> map = new HashMap<Character, Integer>();
        int max = 0;
        int left = 0;
        for(int i = 0; i < s.length(); i ++){
            if(map.containsKey(s.charAt(i))){
                left = Math.max(left,map.get(s.charAt(i)) + 1);  // 重复出现的字符串完全有可能在现在计算的字符串的左边，也就是说重复不产生影响；最重要的就是这一步的理解
            }
            map.put(s.charAt(i),i);  // 维护最新状态
            max = Math.max(max,i-left+1);
        }
        return max;
        
    }
}
~~~

## [==4. 寻找两个正序数组的中位数==](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

思路1：因为有两个数组，所以将中位数k前元素的数量拆成两份分配到两个数组中，对相应下标的位置处数值大小进行比较，较小的则必定本身及前面的都不会是中位数。去掉这些数之后再递归去找。

下面这个代码里面其实好几个地方也是比较细节的。首先是整体长度奇偶的处理，这里不管奇偶都是用了一个left和right去求，只不过是奇数的时候可以保证left==right。另外就是对两个数组长度的判断，保证nums1一定是更短的那个，方便进行判断。当nums1中所有的元素都不可能是中位数时，直接根据下标到nums2中取相应下标的结果即可。

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int n = nums1.length;
        int m = nums2.length;
        int left = (n + m + 1) / 2;
        int right = (n + m + 2) / 2;
        //将偶数和奇数的情况合并，如果是奇数，会求两次同样的 k 。
        return (getKth(nums1, 0, n - 1, nums2, 0, m - 1, left) + getKth(nums1, 0, n - 1, nums2, 0, m - 1, right)) * 0.5;  
    }
    
    private int getKth(int[] nums1, int start1, int end1, int[] nums2, int start2, int end2, int k) {
        int len1 = end1 - start1 + 1;
        int len2 = end2 - start2 + 1;
        //让 len1 的长度小于 len2，这样就能保证如果有数组空了，一定是 len1 
        if (len1 > len2) return getKth(nums2, start2, end2, nums1, start1, end1, k);
        if (len1 == 0) return nums2[start2 + k - 1];

        if (k == 1) return Math.min(nums1[start1], nums2[start2]);

        int i = start1 + Math.min(len1, k / 2) - 1;
        int j = start2 + Math.min(len2, k / 2) - 1;

        if (nums1[i] > nums2[j]) {
            return getKth(nums1, start1, end1, nums2, j + 1, end2, k - (j - start2 + 1));
        }
        else {
            return getKth(nums1, i + 1, end1, nums2, start2, end2, k - (i - start1 + 1));
        }
    }
}
```

~~思路2：~~

用两个数组左右数量的关系去使用一个变量确定划分方式，比思路1复杂度更低，不过更难理解和实现。

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }

        int m = nums1.length;
        int n = nums2.length;
        int left = 0, right = m;
        // median1：前一部分的最大值
        // median2：后一部分的最小值
        int median1 = 0, median2 = 0;

        while (left <= right) {
            // 前一部分包含 nums1[0 .. i-1] 和 nums2[0 .. j-1]
            // 后一部分包含 nums1[i .. m-1] 和 nums2[j .. n-1]
            int i = (left + right) / 2;
            int j = (m + n + 1) / 2 - i;

            // nums_im1, nums_i, nums_jm1, nums_j 分别表示 nums1[i-1], nums1[i], nums2[j-1], nums2[j]
            int nums_im1 = (i == 0 ? Integer.MIN_VALUE : nums1[i - 1]);
            int nums_i = (i == m ? Integer.MAX_VALUE : nums1[i]);
            int nums_jm1 = (j == 0 ? Integer.MIN_VALUE : nums2[j - 1]);
            int nums_j = (j == n ? Integer.MAX_VALUE : nums2[j]);

            if (nums_im1 <= nums_j) {
                median1 = Math.max(nums_im1, nums_jm1);
                median2 = Math.min(nums_i, nums_j);
                left = i + 1;
            } else {
                right = i - 1;
            }
        }

        return (m + n) % 2 == 0 ? (median1 + median2) / 2.0 : median1;
    }
}
```

## [5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

这题反正这种解法就是中心向外扩展的暴力嘛，然后有两种情况，一种是奇数的回文串，那中间就是一个字符，如果是偶数的回文串，那么中间就是两个字符之间。（因为2022/09/15正好下午刚把联想的一道题写了，是问要在首尾最少加多少个字符才能构成回文串，里面实现的思路反正是很像的，所以就没再写）下面这个题解实际上是把两种回文串的情况都用一个函数封装起来了，思路上是一样的，注意下写法就ok。

```java
class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.length() < 1) {
            return "";
        }
        int start = 0, end = 0;
        for (int i = 0; i < s.length(); i++) {
            int len1 = expandAroundCenter(s, i, i);
            int len2 = expandAroundCenter(s, i, i + 1);
            int len = Math.max(len1, len2);
            if (len > end - start) {
                start = i - (len - 1) / 2;
                end = i + len / 2;
            }
        }
        return s.substring(start, end + 1);
    }

    public int expandAroundCenter(String s, int left, int right) {
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            --left;
            ++right;
        }
        return right - left - 1;
    }
}
```





# 重刷重点题

1. 剑指offer——面试题3——题目二  P41
2. 剑指offer——面试题6  数据结构的使用
3. 剑指offer——面试题7
4. 剑指offer——面试题10 快速幂的实现（矩阵、常数）
5. 剑指offer——P80 快排Partition函数
6. 剑指offer——面试题11 旋转数组的最小数字
7. 剑指offer——面试题15 二进制中1的个数  最优解法（性质）
8. 剑指offer——面试题17 大数情况（**这个不行就战略放弃吧**）
9. 剑指offer——面试题19 正则表达式匹配（至少动态规划的转移方程要能确定）
10. 剑指offer——面试题23 链表中环的入口节点
11. 剑指offer——面试题32 从上到下打印二叉树（主要是第三问写法）
12. 剑指offer——面试题35 复杂链表的复制（思路，新旧链表相邻位）
13. 剑指offer——面试题36 二叉搜索树与双向链表（思路，怎么拆分问题）
14. 剑指offer——面试题37 序列化二叉树 （遍历方式）
15. 剑指offer——面试题38 字符串的排列 （模板写法）
16. 剑指offer——面试题40 最小的k个数 （模板写法）
17. 剑指offer——面试题41 数据流中的中位数 （思路）
18. 剑指offer——面试题43 1~n整数中1出现的次数
19. 剑指offer——面试题44 数字序列中某一位的数字





# [leetcode-master](https://github.com/youngyangyang04/leetcode-master)

## 数组

1. 二分查找：
   - 704-二分查找
2. 快慢指针：
   - 27-移除元素
   - 977-有序数组的平方
3. 滑动窗口：
   - 209-长度最小的子数组
4. 
