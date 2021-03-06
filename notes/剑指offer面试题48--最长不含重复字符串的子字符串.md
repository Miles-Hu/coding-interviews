# 剑指offer面试题48--最长不含重复字符串的子字符串

>   ```
>   请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。假设字符串中只包含'a'~'z'之间的字符，例如在字符串"arabcacfr"中，最长的不含重复字符的子字符串是"acfr"，长度为4
>   ```

动态规划，**定义$f(i)$表示以第i个字符为结尾的不含重复字符的子字符串长度。**

如果第i个字符之前没有出现过，则$f(i) = f(i -1) +1$，比如‘abc',$f(0) = 1$是必然的，然后字符’b‘之前没有出现过，则$f(1) = f(0)+1$, 字符’c'之前没有出现过，那么$f(2) = f(1) +1$,每次计算都会用到上一次计算的结果。

如果第i个字符之前出现过呢？找到该字符上次出现的位置preIndex，当前位置i - preIndex就得到这两个重复字符之间的距离，设为d。此时有两种情况

-   如果`d <= f(i-1)`,说明当前重复字符必然在f(i-1)所对应的字符串中，比如’bdcefgc‘，当前字符c前面出现过了，preIndex为2，此时`d = 6 -2 = 4` ,小于` f(i -1) = 7 (bdcefg)`我们只好丢弃前一次出现的字符c及其前面的所有字符，得到当前最长不含重复字符的子字符串为’efgc‘，即`f(i) = 4`, 多举几个例子就知道，应该让`f(i) = d`；

-   如果`d > f(i-1)`, 这说明当前重复字符必然在f(i-1)所对应的字符串**之前**，比如`erabcdabr`当前字符r和索引1处的r重复，`preIndex =1, i = 8,d = 7`。而`f(i -1) = 4 (cdab)`,此时直接加1即可，即`f(i) = f(i-1) +1`


根据这两种情况可写出代码如下：

```java
package Chap5;

public class LongestSubstring {
    public int findLongestSubstring(String str) {
        int curLen = 0;
        int maxLen = 0;
        // 0~25表示a~z，position[0] = index,表明a上次出现在index处
        int[] position = new int[26];
        for (int i = 0; i < 26; i++) {
            position[i] = -1;
        }

        for (int i = 0; i < str.length(); i++) {
            int preIndex = position[str.charAt(i) - 'a'];
            // 字符第一次出现，或者d > f(i -1)
            if (preIndex == -1 || i - preIndex > curLen) curLen++;
            // d <= f(i -1) 
            else curLen = i - preIndex;
            // 记录当前字符出现的位置
            position[str.charAt(i) - 'a'] = i;
            if (curLen > maxLen) maxLen = curLen;
        }
        return maxLen;
    }
}

```

用了一个数组position代替哈希表，记录'a'~'z'每个字符上次出现的位置，能以O(1)的时间完成，先要将position中的值全初始化为-1，因为上次出现的位置可能含有索引0。 curLen就是上面说到的$f(i -1)$。

类似的解法。

用一个全局preIndex表示重复字符的最远位置，或者说上一次出现重复字符的位置。

还是上面的例子。

- 当`d <= f(i-1)`，如bdcefgc，在当前c未出现时，preIndex是-1，因为还没有出现过重复字符，当前c出现后，有重复字符了，preIndex是索引2处的c。i - preIndex == 4就表示了以当前c结尾的最长不重复子串长度。
- 当`d > f(i-1)`，如erabcdabr，当前r未出现时，上一个重复字符是b，其preIndex是3。当前r出现后，r上一次出现的位置是1，比较两者，显然b的上次出现位置靠右些，所以应该以b的preIndex为准（如果以r的preIndex为准，会将重复的b包含进去）

综和以上两种情况，需要计算1）不计入当前字符时最近一个重复字符的上次出现位置，2）算上当前字符时，当前字符的上次出现位置。取这两者的最大值，即取两个位置中更靠后的位置。`preIndex = Math.max(preIndex, position[ch - 'a'])`，然后`i - preIndex`表示以位置i处字符结尾的最长不重复子串的长度。

```java
public class LongestSubstring {
    public int findLongestSubstring(String str) {
        int curLen = 0;
        int maxLen = 0;
        int preIndex = -1;
        // 0~25表示a~z，position[0] = index,表明a上次出现在index处
        int[] position = new int[26];
        for (int i = 0; i < 26; i++) {
            position[i] = -1;
        }

        for (int i = 0; i < str.length(); i++) {
            preIndex = Math.max(preIndex, position[str.charAt(i) - 'a']);
			curLen = i - preIndex;
            // 记录当前字符出现的位置
            position[str.charAt(i) - 'a'] = i;
            maxLen = Math.max(curLen, maxLen);
        }
        return maxLen;
    }
}
```

---

by @sunhaiyu

2018.1.24