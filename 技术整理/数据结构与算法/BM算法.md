### 字符串匹配的Boyer-Moore算法

参考：[字符串匹配的Boyer-Moore算法](http://www.ruanyifeng.com/blog/2013/05/boyer-moore_string_search_algorithm.html)

BM算法的效率是KMP算法的3-5倍，是一种效率高，构思巧妙的字符串匹配算法。


与基于前缀比较的暴力匹配算法以及KMP算法不同，BM算法采用基于后缀的比较方法，在BM算法中，包含了两个并行的比较方法：1.坏字符算法；2.好后缀算法。算法的核心在于，通过并行的坏字符与好后缀算法，计算出每次后移的最大位移（不超过模式串本身大小）。

算法的开始将待匹配字符串（textString）与模式串（patString）从头部进行对齐。自尾部开始进行字符比较，若尾部比较失败，则可通过一次比较确定匹配失败，进行位移操作





```java
/**
 * 算法匹配
 */
public static int pattern(String pattern, String target) {
    int tLen = target.length();
    int pLen = pattern.length();

    if (pLen > tLen) {
        return -1;
    }

    int[] bad_table = build_bad_table(pattern);// 1,3,5,6,2,
    int[] good_table = build_good_table(pattern);// 1,8,5,10,11,12,13

    for (int i = pLen - 1, j; i < tLen;) {
        System.out.println("跳跃位置：" + i);
        for (j = pLen - 1; target.charAt(i) == pattern.charAt(j); i--, j--) {
            if (j == 0) {
                System.out.println("匹配成功，位置：" + i);
//					i++;   // 多次匹配
//					break;
                return i;
            }
        }
        i += Math.max(good_table[pLen - j - 1], bad_table[target.charAt(i)]);
    }
    return -1;
}

/**
 * 字符信息表
 */
public static int[] build_bad_table(String pattern) {
    final int table_size = 256;
    int[] bad_table = new int[table_size];
    int pLen = pattern.length();

    for (int i = 0; i < bad_table.length; i++) {
        bad_table[i] = pLen;  //默认初始化全部为匹配字符串长度
    }
    for (int i = 0; i < pLen - 1; i++) {
        int k = pattern.charAt(i);
        bad_table[k] = pLen - 1 - i;
    }
//		for (int i : bad_table) {
//			if (i != 7) {
//				System.out.print(i + ",");
//			}
//		}
    return bad_table;
}

/**
 * 匹配偏移表。
 *
 * @param pattern
 *            模式串
 * @return
 */
public static int[] build_good_table(String pattern) {
    int pLen = pattern.length();
    int[] good_table = new int[pLen];
    int lastPrefixPosition = pLen;

    for (int i = pLen - 1; i >= 0; --i) {
        if (isPrefix(pattern, i + 1)) {
            lastPrefixPosition = i + 1;
        }
        good_table[pLen - 1 - i] = lastPrefixPosition - i + pLen - 1;
    }

    for (int i = 0; i < pLen - 1; ++i) {
        int slen = suffixLength(pattern, i);
        good_table[slen] = pLen - 1 - i + slen;
    }
    return good_table;
}

/**
 * 前缀匹配
 */
private static boolean isPrefix(String pattern, int p) {
    int patternLength = pattern.length();
    for (int i = p, j = 0; i < patternLength; ++i, ++j) {
        if (pattern.charAt(i) != pattern.charAt(j)) {
            return false;
        }
    }
    return true;
}

/**
 * 后缀匹配
 */
private static int suffixLength(String pattern, int p) {
    int pLen = pattern.length();
    int len = 0;
    for (int i = p, j = pLen - 1; i >= 0 && pattern.charAt(i) == pattern.charAt(j); i--, j--) {
        len += 1;
    }
    return len;
}
```
