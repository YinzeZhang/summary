```
w70P83eUXeKqTlKbQIP
9Cmi7i2QkUa5TUKFfCIq
Ans8nx2VAmEo
I6CsFTVqT3HpNIcpC3
```

算法思路：先找到长度为6的字串出现的最大次数。再二分找最长的且出现次数等于最大次数的子串。

首先要对原串进行遍历找出所有长度为6的字符串，用kmp求长度为6的子串出现的最大次数。

找到最大次数之后，二分子串长度，用kmp对每一个子串长度找次数为最大次数的子串，如果当前长度的子串出现的次数等于最大次数，二分区间取右边。如果当前长度的子串出现的次数小于最大次数，二分区间取左边。

时间、空间复杂度

空间复杂度：kmp的next数组O(n)

时间复杂度：kmp算法的时间复杂度为O(m + n)，n和m分别为模式串和待查找串的长度。二分区间时间复杂度为O(logn)。枚举长度为m的字串时间复杂度为O(n)，总时间复杂度为（n^2*logn)

```
package com.zyz;

import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        String str = scanner.nextLine();
        int len = str.length();
        String ans = "";

        int maxCount = 0;
        for (int i = 0; i < len; i++) {
            if (i + 6 > len) continue;
            String pattern = str.substring(i, i + 6);
            int[] next = kmpNext(pattern);
            int count = kmp(str, pattern, next);
            maxCount = Math.max(count, maxCount);
        }

        int l = 6, r = len;
        while (l < r) {
            int L = (l + r + 1) / 2;
            int count = 0;
            int start = 0;
            for (int i = 0; i < len; i++) {
                if (i + L > len) continue;
                String pattern = str.substring(i, i + L);
                int[] next = kmpNext(pattern);
                int tmp = kmp(str, pattern, next);
                if (tmp > count) {
                    count = tmp;
                    start = i;
                }
            }
            if (count == maxCount) {
                ans = str.substring(start, start + L);
                l = L;
            } else {
                r = L - 1;
            }
        }
        System.out.println(ans);
    }

    private static int[] kmpNext(String dest) {
        int[] next = new int[dest.length()];
        next[0] = 0;
        for (int i = 1, j = 0; i < dest.length(); i++) {
            while (j > 0 && dest.charAt(i) != dest.charAt(j)) {
                j = next[j - 1];
            }
            if (dest.charAt(i) == dest.charAt(j)) {
                j++;
            }
            next[i] = j;
        }
        return next;
    }

    public static int kmp(String source, String dest, int[] next) {
        int count = 0;
        for (int i = 0, j = 0; i < source.length(); i++) {
            while (j > 0 && source.charAt(i) != dest.charAt(j)) {
                j = next[j - 1];
            }
            if (source.charAt(i) == dest.charAt(j)) {
                j++;
            }
            if (j == dest.length()) {
                count++;
                j = 0;
            }
        }
        return count;
    }
}

```

