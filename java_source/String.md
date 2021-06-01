## String

> 

#### new String()

   String 提供了一个保护类型的构造方法。目前不支持使用false，只使用true。那么可以断定，加入这个share的只是为了区分于String(char[] value)方法，不加这个参数就没办法定义这个函数，只有参数不同才能进行重载。那么，第二个区别就是具体的方法实现不同。这里直接将value的引用赋值给String的value。那么也就是说，这个方法构造出来的String和参数传过来的char[] value共享同一个数组。作用的话，肯定是性能好一点。假如把该方法改为public，而不是protected的话，对外开放访问，就可以通过修改数组的引用来破坏String的不可变性。

```java
String(char[] value, boolean share) {
    // assert share : "unshared not supported";
    this.value = value;// 没有真正复制，只是赋值引用
}
//    对比一下，下面是实际复制了数组元素的：
public String(char value[]) {
    this.value = Arrays.copyOf(value, value.length);//
     	//用到Arrays的copyOf方法将value中的内容逐一复制到String当中
}
```

#### charAt

```java
/**
 * Returns the {@code char} value at the
 * specified index. An index ranges from {@code 0} to
 * {@code length() - 1}. The first {@code char} value of the sequence
 * is at index {@code 0}, the next at index {@code 1},
 * and so on, as for array indexing.
 *
 * <p>If the {@code char} value specified by the index is a
 * <a href="Character.html#unicode">surrogate</a>, the surrogate
 * value is returned.
 *
 * @param      index   the index of the {@code char} value.
 * @return     the {@code char} value at the specified index of this string.
 *             The first {@code char} value is at index {@code 0}.
 * @exception  IndexOutOfBoundsException  if the {@code index}
 *             argument is negative or not less than the length of this
 *             string.
 */
public char charAt(int index) {
    if ((index < 0) || (index >= value.length)) {
        throw new StringIndexOutOfBoundsException(index);
    }
    return value[index];
}
```

#### codePointAt

// 返回 当前下标对应字符的 ascii 码

```java
/**
 * Returns the character (Unicode code point) at the specified
 * index. The index refers to {@code char} values
 * (Unicode code units) and ranges from {@code 0} to
 * {@link #length()}{@code  - 1}.
 *
 * <p> If the {@code char} value specified at the given index
 * is in the high-surrogate range, the following index is less
 * than the length of this {@code String}, and the
 * {@code char} value at the following index is in the
 * low-surrogate range, then the supplementary code point
 * corresponding to this surrogate pair is returned. Otherwise,
 * the {@code char} value at the given index is returned.
 *
 * @param      index the index to the {@code char} values
 * @return     the code point value of the character at the
 *             {@code index}
 * @exception  IndexOutOfBoundsException  if the {@code index}
 *             argument is negative or not less than the length of this
 *             string.
 * @since      1.5
 */
// 下标从 0 开始， s.length  - 1 结束
public int codePointAt(int index) {
    if ((index < 0) || (index >= value.length)) {
        throw new StringIndexOutOfBoundsException(index);
    }
    return Character.codePointAtImpl(value, index, value.length);
}
```

#### codePointBefore

```java
/**
 * Returns the character (Unicode code point) before the specified
 * index. The index refers to {@code char} values
 * (Unicode code units) and ranges from {@code 1} to {@link
 * CharSequence#length() length}.
 *
 * <p> If the {@code char} value at {@code (index - 1)}
 * is in the low-surrogate range, {@code (index - 2)} is not
 * negative, and the {@code char} value at {@code (index -
 * 2)} is in the high-surrogate range, then the
 * supplementary code point value of the surrogate pair is
 * returned. If the {@code char} value at {@code index -
 * 1} is an unpaired low-surrogate or a high-surrogate, the
 * surrogate value is returned.
 *
 * @param     index the index following the code point that should be returned
 * @return    the Unicode code point value before the given index.
 * @exception IndexOutOfBoundsException if the {@code index}
 *            argument is less than 1 or greater than the length
 *            of this string.
 * @since     1.5
 */
//==这个下标是从 1 开始， s.length 结束==
public int codePointBefore(int index) {
    int i = index - 1;
    if ((i < 0) || (i >= value.length)) {
        throw new StringIndexOutOfBoundsException(index);
    }
    return Character.codePointBeforeImpl(value, index, 0);
}
```

#### public String(StringBuffer buffer)

```java
/**
 * Allocates a new string that contains the sequence of characters
 * currently contained in the string buffer argument. The contents of the
 * string buffer are copied; subsequent modification of the string buffer
 * does not affect the newly created string.
 *
 * @param  buffer
 *         A {@code StringBuffer}
 */
// 修改缓冲区的内容， 并不会影响到 新创建的 String 对象
public String(StringBuffer buffer) {
    synchronized(buffer) {
        this.value = Arrays.copyOf(buffer.getValue(), buffer.length());
    }
}
```

#### public String(StringBuilder builder)

```java
/**
 * Allocates a new string that contains the sequence of characters
 * currently contained in the string builder argument. The contents of the
 * string builder are copied; subsequent modification of the string builder
 * does not affect the newly created string.
 *
 * <p> This constructor is provided to ease migration to {@code
 * StringBuilder}. Obtaining a string from a string builder via the {@code
 * toString} method is likely to run faster and is generally preferred.
 *
 * @param   builder
 *          A {@code StringBuilder}
 *
 * @since  1.5
 */
// 修改缓冲区的内容， 并不会影响到 新创建的 String 对象
public String(StringBuilder builder) {
    this.value = Arrays.copyOf(builder.getValue(), builder.length());
}
```

#### void getChars(char dst[], int dstBegin)

==包内私有方法，把一个字符串的一部分 复制到 dst 数组中， 而且从下标 = dstBegin 出开始, 把value 全部复制到 新的数组中==

```java
/**
 * Copy characters from this string into dst starting at dstBegin.
 * This method doesn't perform any range checking.
 */
void getChars(char dst[], int dstBegin) {
    System.arraycopy(value, 0, dst, dstBegin, value.length);
}
getChars(dst, 10); 也就是 下标从 10 开始， 才被中心赋值
```

#### ==compareTo==

==

- 如果相等 返回 0
- 选择 最小下标不同的字符比较， 如果  return currStr.charAt(k) < anotherStr.charAt(k)
- 如果比较完最短字符串的所有字符都相等，则返回 return currStr.length - anotherStr.length

==

```java
/**
     * Compares two strings lexicographically.
     * The comparison is based on the Unicode value of each character in
     * the strings. The character sequence represented by this
     * {@code String} object is compared lexicographically to the
     * character sequence represented by the argument string. The result is
     * a negative integer if this {@code String} object
     * lexicographically precedes the argument string. The result is a
     * positive integer if this {@code String} object lexicographically
     * follows the argument string. The result is zero if the strings
     * are equal; {@code compareTo} returns {@code 0} exactly when
     * the {@link #equals(Object)} method would return {@code true}.
     * <p>
     * This is the definition of lexicographic ordering. If two strings are
     * different, then either they have different characters at some index
     * that is a valid index for both strings, or their lengths are different,
     * or both. If they have different characters at one or more index
     * positions, let <i>k</i> be the smallest such index; then the string
     * whose character at position <i>k</i> has the smaller value, as
     * determined by using the &lt; operator, lexicographically precedes the
     * other string. In this case, {@code compareTo} returns the
     * difference of the two character values at position {@code k} in
     * the two string -- that is, the value:
     * <blockquote><pre>
     * this.charAt(k)-anotherString.charAt(k)
     * </pre></blockquote>
     * If there is no index position at which they differ, then the shorter
     * string lexicographically precedes the longer string. In this case,
     * {@code compareTo} returns the difference of the lengths of the
     * strings -- that is, the value:
     * <blockquote><pre>
     * this.length()-anotherString.length()
     * </pre></blockquote>
     *
     * @param   anotherString   the {@code String} to be compared.
     * @return  the value {@code 0} if the argument string is equal to
     *          this string; a value less than {@code 0} if this string
     *          is lexicographically less than the string argument; and a
     *          value greater than {@code 0} if this string is
     *          lexicographically greater than the string argument.
     */

public int compareTo(String anotherString) {
     int len1 = value.length;
     int len2 = anotherString.value.length;
     int lim = Math.min(len1, len2);
     char v1[] = value;
     char v2[] = anotherString.value;

     int k = 0;
     while (k < lim) {
          char c1 = v1[k];
          char c2 = v2[k];
          if (c1 != c2) {
               return c1 - c2;
          }
          k++;
     }
     return len1 - len2;
}
```

#### compare

==这个非常有意思， 大写相等的小写一定相等， 为什么下面出现了及比较小写 有比较大写的情况==

>public boolean equalsIgnoreCase (String anotherString)
>
>​        在 Java 的字符串忽略大小写比较中有个比较奇怪的做法，将 字符 转换成大写比较之后，还会将 字符 转换成小写在比较一次。
>
>源代码中的解释是：
>
>​    Unfortunately, conversion to uppercase does not work properly for the Georgian alphabet, which has strange rules about case conversion.  So we need to make one last check before exiting.
>
>翻译如下：
>
>​    不幸的是，对于格鲁吉亚字母表来说，转换为大写字母并不能正常工作，因为格鲁吉亚字母表在大小写转换方面有着奇怪的规则。所以我们需要在退出前进行最后一次检查。

```java
public int compare(String s1, String s2) {
    int n1 = s1.length();
    int n2 = s2.length();
    int min = Math.min(n1, n2);
    for (int i = 0; i < min; i++) {
        char c1 = s1.charAt(i);
        char c2 = s2.charAt(i);
        if (c1 != c2) {
            c1 = Character.toUpperCase(c1);
            c2 = Character.toUpperCase(c2);
            if (c1 != c2) {
                c1 = Character.toLowerCase(c1);
                c2 = Character.toLowerCase(c2);
                if (c1 != c2) {
                    // No overflow because of numeric promotion
                    return c1 - c2;
                }
            }
        }
    }
    return n1 - n2;
}
```

#### regionMatches

==忽略大小写，  只是比较两个字符串的一部分内容, 返回的是true or false==

```java
/**
     * Tests if two string regions are equal.
     * <p>
     * A substring of this {@code String} object is compared to a substring
     * of the argument {@code other}. The result is {@code true} if these
     * substrings represent character sequences that are the same, ignoring
     * case if and only if {@code ignoreCase} is true. The substring of
     * this {@code String} object to be compared begins at index
     * {@code toffset} and has length {@code len}. The substring of
     * {@code other} to be compared begins at index {@code ooffset} and
     * has length {@code len}. The result is {@code false} if and only if
     * at least one of the following is true:
     * <ul><li>{@code toffset} is negative.
     * <li>{@code ooffset} is negative.
     * <li>{@code toffset+len} is greater than the length of this
     * {@code String} object.
     * <li>{@code ooffset+len} is greater than the length of the other
     * argument.
     * <li>{@code ignoreCase} is {@code false} and there is some nonnegative
     * integer <i>k</i> less than {@code len} such that:
     * <blockquote><pre>
     * this.charAt(toffset+k) != other.charAt(ooffset+k)
     * </pre></blockquote>
     * <li>{@code ignoreCase} is {@code true} and there is some nonnegative
     * integer <i>k</i> less than {@code len} such that:
     * <blockquote><pre>
     * Character.toLowerCase(this.charAt(toffset+k)) !=
     Character.toLowerCase(other.charAt(ooffset+k))
     * </pre></blockquote>
     * and:
     * <blockquote><pre>
     * Character.toUpperCase(this.charAt(toffset+k)) !=
     *         Character.toUpperCase(other.charAt(ooffset+k))
     * </pre></blockquote>
     * </ul>
     *
     * @param   ignoreCase   if {@code true}, ignore case when comparing
     *                       characters.
     * @param   toffset      the starting offset of the subregion in this
     *                       string.
     * @param   other        the string argument.
     * @param   ooffset      the starting offset of the subregion in the string
     *                       argument.
     * @param   len          the number of characters to compare.
     * @return  {@code true} if the specified subregion of this string
     *          matches the specified subregion of the string argument;
     *          {@code false} otherwise. Whether the matching is exact
     *          or case insensitive depends on the {@code ignoreCase}
     *          argument.
     */
public boolean regionMatches(boolean ignoreCase, int toffset,
                             String other, int ooffset, int len) {
     char ta[] = value;
     int to = toffset;
     char pa[] = other.value;
     int po = ooffset;
     // Note: toffset, ooffset, or len might be near -1>>>1.
     if ((ooffset < 0) || (toffset < 0)
         || (toffset > (long)value.length - len)
         || (ooffset > (long)other.value.length - len)) {
          return false;
     }
     while (len-- > 0) {
          char c1 = ta[to++];
          char c2 = pa[po++];
          if (c1 == c2) {
               continue;
          }
          if (ignoreCase) {
               // If characters don't match but case may be ignored,
               // try converting both characters to uppercase.
               // If the results match, then the comparison scan should
               // continue.
               char u1 = Character.toUpperCase(c1);
               char u2 = Character.toUpperCase(c2);
               if (u1 == u2) {
                    continue;
               }
               // Unfortunately, conversion to uppercase does not work properly
               // for the Georgian alphabet, which has strange rules about case
               // conversion.  So we need to make one last check before
               // exiting.
               if (Character.toLowerCase(u1) == Character.toLowerCase(u2)) {
                    continue;
               }
          }
          return false;
     }
     return true;
}
```

#### indexOf

// 这里面只有第一个 和 第二个 在外部被调用， 而另外两个多参的 indexOf 都只能 String内部调用， 所以他们的参数也都是固定的。

char[] source： 字符串

int sourceOffset	：字符串偏移  默认 为 0

int sourceCount：字符串的长度

char[] target：查找字符串

int targetOffset：查找字符串的偏移  默认为 0

int targetCount：查找字符串的长度

int fromIndex：从源字符串开始找的下标

```java
public int indexOf(String str) { // 
     return indexOf(str, 0);
}

/**
     * Returns the index within this string of the first occurrence of the
     * specified substring, starting at the specified index.
     *
     * <p>The returned index is the smallest value <i>k</i> for which:
     * <blockquote><pre>
     * <i>k</i> &gt;= fromIndex {@code &&} this.startsWith(str, <i>k</i>)
     * </pre></blockquote>
     * If no such value of <i>k</i> exists, then {@code -1} is returned.
     *
     * @param   str         the substring to search for.
     * @param   fromIndex   the index from which to start the search.
     * @return  the index of the first occurrence of the specified substring,
     *          starting at the specified index,
     *          or {@code -1} if there is no such occurrence.
     */
public int indexOf(String str, int fromIndex) {
     return indexOf(value, 0, value.length,
                    str.value, 0, str.value.length, fromIndex);
}

/**
     * Code shared by String and AbstractStringBuilder to do searches. The
     * source is the character array being searched, and the target
     * is the string being searched for.
     *
     * @param   source       the characters being searched.
     * @param   sourceOffset offset of the source string.
     * @param   sourceCount  count of the source string.
     * @param   target       the characters being searched for.
     * @param   fromIndex    the index to begin searching from.
     */
static int indexOf(char[] source, int sourceOffset, int sourceCount,
                   String target, int fromIndex) {
     return indexOf(source, sourceOffset, sourceCount,
                    target.value, 0, target.value.length,
                    fromIndex);
}
```

#### indexOf

> 这个其实就是一种实现，还是挺难的
>
> ![](java_source_imgs/indexOf_max.jpg)

```java
/**
 * Code shared by String and StringBuffer to do searches. The
 * source is the character array being searched, and the target
 * is the string being searched for.
 *
 * @param   source       the characters being searched.
 * @param   sourceOffset offset of the source string.
 * @param   sourceCount  count of the source string.
 * @param   target       the characters being searched for.
 * @param   targetOffset offset of the target string.
 * @param   targetCount  count of the target string.
 * @param   fromIndex    the index to begin searching from.
 */
static int indexOf(char[] source, int sourceOffset, int sourceCount,
                   char[] target, int targetOffset, int targetCount,
                   int fromIndex) {
     // 如果targetCount = 0; 有两种返回结果， 要么 为 fromIndex, 要么为最大值
     // 如果 fromIndex >= sourceCount  返回sourceCount
     // 否则， 返回formIndex
     if (fromIndex >= sourceCount) {
          return (targetCount == 0 ? sourceCount : -1);
     }
     if (fromIndex < 0) {
          fromIndex = 0;
     }
     if (targetCount == 0) {
          return fromIndex;
     }

     char first = target[targetOffset]; // 找出查找字符串的第一个字符
     int max = sourceOffset + (sourceCount - targetCount);	// 最后一次比较，第一个字符所能比较的下标。  因为 i + target.length() < max + target.length，详情见上图

     for (int i = sourceOffset + fromIndex; i <= max; i++) {
          /* Look for first character. */
          /*
          	这儿非常有意思，例如， 【b, b, o, u] 查找字符串b o u, 找到第一个字符 b 相等，但是后面不相等， 如果当前字符串的后一个字符 又与第一个字符 相等， 重新开始一次比较
          	b = first, 但是后面的不等， 开始新一轮比较， 这是 第一个出现 b 的后面可能再次出现 b          	
          */
          if (source[i] != first) {
               while (++i <= max && source[i] != first);
          }
		  // 1. 其实，执行到这一步，要么第一个字符都没有找到，并且，i > max
         // 2. 第一个字符刚好找到了
          /* Found first character, now look at the rest of v2 */
          if (i <= max) {
               int j = i + 1; // 执行到这一步， 第一个字符一定匹配， 所以从 匹配后的字符串开始
               int end = j + targetCount - 1;	// 所查找的次数就是 j + targetCount - 1(只比较 targetCount - 1次就够了)  -- 因为，第一个字符已经匹配，之后最多比较的下标
               // 
               for (int k = targetOffset + 1; j < end && source[j]
                    == target[k]; j++, k++);
			// 这儿就是source[j] == target[k]， 如果比较之后， j == end, 也就是匹配到 该字符串
              // 其实，如果，sourceOffset == 0, 这个时候直接返回 i 就可以，也可以返回
              // j - targetCount
               if (j == end) {
                    /* Found whole string. */
                    return i - sourceOffset;
               }
               // 
          }
     }
     return -1;
}
```

#### lastIndexOf

```java
/**
 * Returns the index within this string of the last occurrence of the
 * specified substring.  The last occurrence of the empty string ""
 * is considered to occur at the index value {@code this.length()}.
 *
 * <p>The returned index is the largest value <i>k</i> for which:
 * <blockquote><pre>
 * this.startsWith(str, <i>k</i>)
 * </pre></blockquote>
 * If no such value of <i>k</i> exists, then {@code -1} is returned.
 *
 * @param   str   the substring to search for.
 * @return  the index of the last occurrence of the specified substring,
 *          or {@code -1} if there is no such occurrence.
 */
public int lastIndexOf(String str) {
    return lastIndexOf(str, value.length);
}

/**
 * Returns the index within this string of the last occurrence of the
 * specified substring, searching backward starting at the specified index.
 *
 * <p>The returned index is the largest value <i>k</i> for which:
 * <blockquote><pre>
 * <i>k</i> {@code <=} fromIndex {@code &&} this.startsWith(str, <i>k</i>)
 * </pre></blockquote>
 * If no such value of <i>k</i> exists, then {@code -1} is returned.
 *
 * @param   str         the substring to search for.
 * @param   fromIndex   the index to start the search from.
 * @return  the index of the last occurrence of the specified substring,
 *          searching backward from the specified index,
 *          or {@code -1} if there is no such occurrence.
 */
public int lastIndexOf(String str, int fromIndex) {
    return lastIndexOf(value, 0, value.length,
            str.value, 0, str.value.length, fromIndex);
}

/**
 * Code shared by String and AbstractStringBuilder to do searches. The
 * source is the character array being searched, and the target
 * is the string being searched for.
 *
 * @param   source       the characters being searched.
 * @param   sourceOffset offset of the source string.
 * @param   sourceCount  count of the source string.
 * @param   target       the characters being searched for.
 * @param   fromIndex    the index to begin searching from.
 */
static int lastIndexOf(char[] source, int sourceOffset, int sourceCount,
        String target, int fromIndex) {
    return lastIndexOf(source, sourceOffset, sourceCount,
                   target.value, 0, target.value.length,
                   fromIndex);
}

/**
 * Code shared by String and StringBuffer to do searches. The
 * source is the character array being searched, and the target
 * is the string being searched for.
 *
 * @param   source       the characters being searched.
 * @param   sourceOffset offset of the source string.
 * @param   sourceCount  count of the source string.
 * @param   target       the characters being searched for.
 * @param   targetOffset offset of the target string.
 * @param   targetCount  count of the target string.
 * @param   fromIndex    the index to begin searching from.
 */
static int lastIndexOf(char[] source, int sourceOffset, int sourceCount,
        char[] target, int targetOffset, int targetCount,
        int fromIndex) {
    /*
     * Check arguments; return immediately where possible. For
     * consistency, don't check for null str.
     */
     // 查找字符串长度为 3， 开始查找位置必须 为 10 - 3， 也就是最右边开始查找的下标， 有点类似于indexOf 中 最后允许查找的下标
    int rightIndex = sourceCount - targetCount;
     // 
    if (fromIndex < 0) {
        return -1;
    }
     // 从 rightIndex 开始查找
    if (fromIndex > rightIndex) {
        fromIndex = rightIndex;
    }
    /* Empty string always matches. */
     // 如果 
    if (targetCount == 0) {
        return fromIndex;
    }

    int strLastIndex = targetOffset + targetCount - 1;
    char strLastChar = target[strLastIndex];	// 最后一个字符
    int min = sourceOffset + targetCount - 1;
    int i = min + fromIndex;	// 因为首先获取的最后一个字符， 所以从 sourceCount - 1

startSearchForLastChar:
    while (true) {
        while (i >= min && source[i] != strLastChar) {// 首先匹配第一个字符
            i--;
        }
        if (i < min) {	// 剩余字符数量小于 查找字符长度， 自然查找不成功
            return -1;
        }
        int j = i - 1;
        int start = j - (targetCount - 1);
        int k = strLastIndex - 1;

        while (j > start) {		
            if (source[j--] != target[k--]) {
                i--;
                continue startSearchForLastChar;
            }
        }
        return start - sourceOffset + 1;
    }
}
```

#### trim

- 字符串为空	返回源字符串
- 字符串第一个字符和最后一个字符都  > 空格， 返回源字符串
- 如果字符串每个字符都 < 空格， 返回空字符串
- 如果开头或结尾 有空格， 就会取字串， k 为 开始不为 空格字符的下标， m 为结尾 不为空格字符的下标， 然后 subString(k, m + 1);

```java
/**
     * Returns a string whose value is this string, with any leading and trailing
     * whitespace removed.
     * <p>
     * If this {@code String} object represents an empty character
     * sequence, or the first and last characters of character sequence
     * represented by this {@code String} object both have codes
     * greater than {@code '\u005Cu0020'} (the space character), then a
     * reference to this {@code String} object is returned.
     * <p>
     * Otherwise, if there is no character with a code greater than
     * {@code '\u005Cu0020'} in the string, then a
     * {@code String} object representing an empty string is
     * returned.
     * <p>
     * Otherwise, let <i>k</i> be the index of the first character in the
     * string whose code is greater than {@code '\u005Cu0020'}, and let
     * <i>m</i> be the index of the last character in the string whose code
     * is greater than {@code '\u005Cu0020'}. A {@code String}
     * object is returned, representing the substring of this string that
     * begins with the character at index <i>k</i> and ends with the
     * character at index <i>m</i>-that is, the result of
     * {@code this.substring(k, m + 1)}.
     * <p>
     * This method may be used to trim whitespace (as defined above) from
     * the beginning and end of a string.
     *
     * @return  A string whose value is this string, with any leading and trailing white
     *          space removed, or this string if it has no leading or
     *          trailing white space.
     */
public String trim() {
     int len = value.length;
     int st = 0;
     char[] val = value;    /* avoid getfield opcode */

     while ((st < len) && (val[st] <= ' ')) {
          st++;
     }
     while ((st < len) && (val[len - 1] <= ' ')) {
          len--;
     }
     return ((st > 0) || (len < value.length)) ? substring(st, len) : this;
}
```

#### substring

- 取值 subString(a, b) 实际上取得是 a 到 (b-1)的字符组成的字符串

  ==有意思的是==

  - 如果 subString(0, value.length) , 表示返回原来的字符串， 而不是创建新的字符串（同一个引用）
  - 如果subString(a, b) ， a != 0 || b != value.length , 就会取字串， 然后 返回一个新的字符串（堆上）

```java
/**
     * Returns a string that is a substring of this string. The
     * substring begins at the specified {@code beginIndex} and
     * extends to the character at index {@code endIndex - 1}.
     * Thus the length of the substring is {@code endIndex-beginIndex}.
     * <p>
     * Examples:
     * <blockquote><pre>
     * "hamburger".substring(4, 8) returns "urge"
     * "smiles".substring(1, 5) returns "mile"
     * </pre></blockquote>
     *
     * @param      beginIndex   the beginning index, inclusive.
     * @param      endIndex     the ending index, exclusive.
     * @return     the specified substring.
     * @exception  IndexOutOfBoundsException  if the
     *             {@code beginIndex} is negative, or
     *             {@code endIndex} is larger than the length of
     *             this {@code String} object, or
     *             {@code beginIndex} is larger than
     *             {@code endIndex}.
     */
public String substring(int beginIndex, int endIndex) {
     if (beginIndex < 0) {
          throw new StringIndexOutOfBoundsException(beginIndex);
     }
     if (endIndex > value.length) {
          throw new StringIndexOutOfBoundsException(endIndex);
     }
     int subLen = endIndex - beginIndex;
     if (subLen < 0) {
          throw new StringIndexOutOfBoundsException(subLen);
     }
     return ((beginIndex == 0) && (endIndex == value.length)) ? this
          : new String(value, beginIndex, subLen);
}
```

### `getChar`

> 这个函数还是比较难的，要重复记录

==包内私有方法，把一个字符串的一部分 复制到 dst 数组中， 而且从下标 = dstBegin 出开始复制数组, 复制的整个长度为 srcEnd - srcBegin==

```java
public void getChars(int srcBegin, int srcEnd, char dst[], int dstBegin) {
    if (srcBegin < 0) {
        throw new StringIndexOutOfBoundsException(srcBegin);
    }
    if (srcEnd > value.length) {
        throw new StringIndexOutOfBoundsException(srcEnd);
    }
    if (srcBegin > srcEnd) {
        throw new StringIndexOutOfBoundsException(srcEnd - srcBegin);
    }
    System.arraycopy(value, srcBegin, dst, dstBegin, srcEnd - srcBegin);
}
```

#### getChars

> 将调用者 复制到 dst， 从 dstBegin 开始，长度为 value.length
>
> 从dstBegin开始将字符串中的字符复制到dst中。此方法不执行任何范围检查。

```java
void getChars(char dst[], int dstBegin) {
    System.arraycopy(value, 0, dst, dstBegin, value.length);
}
```

#### concat

```java
public String concat(String str) {
    // 假设调用者为 src
    // 要拼接的字符串，如果为空，直接返回
    int otherLen = str.length();
    if (otherLen == 0) {
        return this;
    }
    // 
    int len = value.length;
    // 首先创建新的数组
    char buf[] = Arrays.copyOf(value, len + otherLen);
    // getChars 其实就是将 str 拼接到 buf 从 下标 len 开始的位置
    str.getChars(buf, len);
	// 这个时候 buf 下标 len 之前存储的调用者src, 下标 len 之后存储的是 str
    // 下面的构造方法表示 不会创建新的数组。一定要和 new String(String name) 区分开，因为这个会创建新的数组存储 name
    return new String(buf, true);
}
```

#### '+' 和 concat 的区别

> 1. 同样都是连接字符串，但是 '+' 不受参数类型的限制，可以接受 数字、null、对象类型等各种类型的拼接
>
>    ```java
>    name = "CaoXiangbiao";
>    System.out.println(name + 4); // 
>    System.out.println(5 + name + 4);
>    System.out.println(name + null);
>    System.out.println(3 + 2 + name); // 这个很有意思，就是字符串之前的 '+' 会进行计算，然后再连接
>    System.out.println(name + 3 + 2); // CaoXiangbiao32,字符串之后出现的被视为字符串的连接
>    /*
>    CaoXiangbiao4
>    5CaoXiangbiao4
>    CaoXiangbiaonull
>    5CaoXiangbiao
>    */
>    ```
>
> 2. concat 只接受字符串类型的参数，并且 str 不能为空

#### '+' 字节码层面

> 我们会发现，使用 StringBuilder 会创建，然后调用两次 append 方法，最后调用 toString 方法，将该对象转化为 String 类型返回，要记住，这种拼接只会在队中创建字符串对象，而不会在字符串常量池中创建对象。

```java
       String surName = "Cao", firstName = "Xiangbiao";
	   String name = surName + firstName;       
	   0: ldc           #2                  // String Cao
       2: astore_1
       3: ldc           #3                  // String Xiangbiao
       5: astore_2
       6: new           #4                  // class java/lang/StringBuilder
       9: dup
      10: invokespecial #5                  // Method java/lang/StringBuilder."<init>":()V
      13: aload_1
      14: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljav
a/lang/StringBuilder;
      17: aload_2
      18: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljav
a/lang/StringBuilder;
      21: invokevirtual #7                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      24: astore_3
      25: return
```
#### contentEquals 和 equals 的区别

> 该方法接受 StringBuffer 和 CharSequence 类型的参数，其实比较的也是两个字符串的内容，`尽管 equals 接收 Object 类型的参数`，但是后续的比较只能为 字符串类型

```java
/*
将此字符串与指定的StringBuffer进行比较。当且仅当此String表示与指定StringBuffer相同的字符序列时，结果为true。这个方法在StringBuffer上进行同步。
*/
public boolean contentEquals(StringBuffer sb) {
    return contentEquals((CharSequence)sb);
}
private boolean nonSyncContentEquals(AbstractStringBuilder sb) {
    char v1[] = value;
    char v2[] = sb.getValue(); // 获取 StringBuilder 中的字符数组 
    int n = v1.length;
    if (n != sb.length()) {
        return false;
    }
    for (int i = 0; i < n; i++) {
        if (v1[i] != v2[i]) {
            return false;
        }
    }
    return true;
}

/*
将此字符串与指定的CharSequence进行比较。当且仅当此String表示与指定序列相同的char值序列时，结果为true。注意，如果CharSequence是一个StringBuffer，那么该方法将对它进行同步。
*/
public boolean contentEquals(CharSequence cs) {
    // Argument is a StringBuffer, StringBuilder
    if (cs instanceof AbstractStringBuilder) {
        if (cs instanceof StringBuffer) {
            synchronized(cs) { // 还是要同步的
                return nonSyncContentEquals((AbstractStringBuilder)cs);
            }
        } else {
            return nonSyncContentEquals((AbstractStringBuilder)cs);
        }
    }
    // Argument is a String 
    if (cs instanceof String) {
        return equals(cs);
    }
    // Argument is a generic CharSequence -- 其他类型
    // {
    char v1[] = value;
    int n = v1.length;
    if (n != cs.length()) {
        return false;
    }
    for (int i = 0; i < n; i++) {
        if (v1[i] != cs.charAt(i)) {
            return false;
        }
    }
    // } -- 其实，这儿的操作和 nonSyncContentEquals 还是有些区别的，因为charSequence 有些不是由 字符数组构成
    return true;
}
```

#### regionMatches

> 该方法被重载，一个忽略大小写，一个不忽略大小写

```java
/*
将此String对象的子字符串与参数other的子字符串进行比较。如果这些子字符串表示相同的字符序列，则结果为真。要比较的String对象的子字符串从索引tooffset开始，长度为len。要比较的other子字符串从下标offset开始，长度为len。当且仅当下列条件中至少有一个为真时，结果为假
*/
public boolean regionMatches(int toffset, String other, int ooffset,
                             int len) {
    char ta[] = value;
    int to = toffset;
    char pa[] = other.value;
    int po = ooffset;
    // Note: toffset, ooffset, or len might be near -1>>>1.
    if ((ooffset < 0) || (toffset < 0)
        || (toffset > (long)value.length - len)
        || (ooffset > (long)other.value.length - len)) {
        return false;
    }
    while (len-- > 0) {
        if (ta[to++] != pa[po++]) {
            return false;
        }
    }
    return true;
}

public boolean regionMatches(boolean ignoreCase, int toffset,
                             String other, int ooffset, int len) {
    char ta[] = value;
    int to = toffset;
    char pa[] = other.value;
    int po = ooffset;
    // Note: toffset, ooffset, or len might be near -1>>>1.
    if ((ooffset < 0) || (toffset < 0)
        || (toffset > (long)value.length - len)
        || (ooffset > (long)other.value.length - len)) {
        return false;
    }
    while (len-- > 0) {
        char c1 = ta[to++];
        char c2 = pa[po++];
        if (c1 == c2) {
            continue;
        }
        if (ignoreCase) {
            // If characters don't match but case may be ignored,
            // try converting both characters to uppercase.
            // If the results match, then the comparison scan should
            // continue.
            char u1 = Character.toUpperCase(c1);
            char u2 = Character.toUpperCase(c2);
            if (u1 == u2) {
                continue;
            }
            // Unfortunately, conversion to uppercase does not work properly
            // for the Georgian alphabet, which has strange rules about case
            // conversion.  So we need to make one last check before
            // exiting.
            if (Character.toLowerCase(u1) == Character.toLowerCase(u2)) {
                continue;
            }
        }
        return false;
    }
    return true;
}
```

#### regionMatches 和 equals 的区别

> **相同点：**
>
> 1. 首先两个的参数都必须是 String 类型。（equals 虽然是 Object, 但是，只有为 String 类型时，才满足）
>
> **区别：**
>
> 1. regionMatches 可以只匹配两个字符串的一部分，而 equals 只能完整的比较字符串
>
> 2. regionMatches 可以不区分大小写的前提下，比较字符串的一部分。
>
>    ```java
>    String surName = "CaoXiangbiao";
>    String firstName = "Biao";
>    System.out.println(surName.regionMatches(true, 8, firstName, 0, 4));
>    ```

#### equalsIgnoreCase 和 equals 的区别

> 区别类似于 regionMatches 和 equals 的区别

#### startsWith 和 endWith

> startWith 当前字符串以什么开始
>
> endWith 当前字符串以什么结束