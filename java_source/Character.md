# Character

> ** <p><a name="BMP">The set of characters from U+0000 to U+FFFF</a> is* sometimes referred to as the <em>Basic Multilingual Plane (BMP)</em>.* <a name="supplementary">Characters</a> whose code points are greater* than U+FFFF are called <em>supplementary character</em>s.  The Java* platform uses the UTF-16 representation in {@code char} arrays and* in the {@code String} and {@code StringBuffer} classes. In* this representation, supplementary characters are represented as a pair* of {@code char} values, the first from the <em>high-surrogates</em>* range, (&#92;uD800-&#92;uDBFF), the second from the* <em>low-surrogates</em> range (&#92;uDC00-&#92;uDFFF).*
>
> ==BMP 和 SMP的区别==
>
> char 数组、String、StringBuffer 都是用的utf-16

> ```
> * <ul>* <li>The methods that only accept a {@code char} value cannot support* supplementary characters. They treat {@code char} values from the* surrogate ranges as undefined characters. For example,* {@code Character.isLetter('\u005CuD840')} returns {@code false}, even though* this specific value if followed by any low-surrogate value in a string* would represent a letter.** <li>The methods that accept an {@code int} value support all* Unicode characters, including supplementary characters. For* example, {@code Character.isLetter(0x2F81A)} returns* {@code true} because the code point value represents a letter* (a CJK ideograph).* </ul>*
> ```

#### CharacterCache

```java
private static class CharacterCache {
     private CharacterCache(){}

     static final Character cache[] = new Character[127 + 1];

     static {
          for (int i = 0; i < cache.length; i++)
               cache[i] = new Character((char)i);
     }
}
```

#### valueOf

> 静态函数。如果 char 的值小于 127， 就从 cache中取出相同的值。

```java
public static Character valueOf(char c) {
     if (c <= 127) { // must cache
          return CharacterCache.cache[(int)c];
     }
     return new Character(c);
}
```

#### charValue

> 获取char 的值， 返回的是基本类型（intValue）

```java
/**
     * Returns the value of this {@code Character} object.
     * @return  the primitive {@code char} value represented by
     *          this object.
     */
public char charValue() {
     return value;
}
```

#### equals

> 这里比较的是内容， 而不是进行  obj == this. 而是进行  value = obj.charValue

```java
/**
     * Compares this object against the specified object.
     * The result is {@code true} if and only if the argument is not
     * {@code null} and is a {@code Character} object that
     * represents the same {@code char} value as this object.
     *
     * @param   obj   the object to compare with.
     * @return  {@code true} if the objects are the same;
     *          {@code false} otherwise.
     */
public boolean equals(Object obj) {
     if (obj instanceof Character) {
          return value == ((Character)obj).charValue();
     }
     return false;
}
```

#### isValidCodePoint

> 0x0000 - 0x10FFFF

```java
/**
     * Determines whether the specified code point is a valid
     * <a href="http://www.unicode.org/glossary/#code_point">
     * Unicode code point value</a>.
     *
     * @param  codePoint the Unicode code point to be tested
     * @return {@code true} if the specified code point value is between
     *         {@link #MIN_CODE_POINT} and
     *         {@link #MAX_CODE_POINT} inclusive;
     *         {@code false} otherwise.
     * @since  1.5
     */
public static boolean isValidCodePoint(int codePoint) {
     // Optimized form of:
     //     codePoint >= MIN_CODE_POINT && codePoint <= MAX_CODE_POINT
     int plane = codePoint >>> 16;
     return plane < ((MAX_CODE_POINT + 1) >>> 16);
}
System.out.println(Character.isValidCodePoint(0X13FFFF)); // false
```

#### isBmpCodePoint

> 是不是低位  0x0000-0xFFFF1

```java
/**
     * Determines whether the specified character (Unicode code point)
     * is in the <a href="#BMP">Basic Multilingual Plane (BMP)</a>.
     * Such code points can be represented using a single {@code char}.
     *
     * @param  codePoint the character (Unicode code point) to be tested
     * @return {@code true} if the specified code point is between
     *         {@link #MIN_VALUE} and {@link #MAX_VALUE} inclusive;
     *         {@code false} otherwise.
     * @since  1.7
     */
public static boolean isBmpCodePoint(int codePoint) {
     return codePoint >>> 16 == 0;
     // Optimized form of:
     //     codePoint >= MIN_VALUE && codePoint <= MAX_VALUE
     // We consistently use logical shift (>>>) to facilitate
     // additional runtime optimizations.
}
System.out.println(Character.isBmpCodePoint(0X13FFFF)); // false
System.out.println(Character.isBmpCodePoint(57343));	// true
```

#### isSupplementaryCodePoint

> 不是高位，而是 判断当前字符是不是 超过了 0x010000

```java
/**
     * Determines whether the specified character (Unicode code point)
     * is in the <a href="#supplementary">supplementary character</a> range.
     *
     * @param  codePoint the character (Unicode code point) to be tested
     * @return {@code true} if the specified code point is between
     *         {@link #MIN_SUPPLEMENTARY_CODE_POINT} and
     *         {@link #MAX_CODE_POINT} inclusive;
     *         {@code false} otherwise.
     * @since  1.5
     */
public static boolean isSupplementaryCodePoint(int codePoint) {
     return codePoint >= MIN_SUPPLEMENTARY_CODE_POINT
          && codePoint <  MAX_CODE_POINT + 1;
}
```

#### isHighSurrogate 和 isLowSurrogate

> 这个和上面不同的是，只是判断当前字符是不是高位或低位，是针对 isSupplementaryCodePoint, 只有这个函数为 true 的情况下，

```java
/**
     * Determines if the given {@code char} value is a
     * <a href="http://www.unicode.org/glossary/#high_surrogate_code_unit">
     * Unicode high-surrogate code unit</a>
     * (also known as <i>leading-surrogate code unit</i>).
     *
     * <p>Such values do not represent characters by themselves,
     * but are used in the representation of
     * <a href="#supplementary">supplementary characters</a>
     * in the UTF-16 encoding.
     *
     * @param  ch the {@code char} value to be tested.
     * @return {@code true} if the {@code char} value is between
     *         {@link #MIN_HIGH_SURROGATE} and
     *         {@link #MAX_HIGH_SURROGATE} inclusive;
     *         {@code false} otherwise.
     * @see    Character#isLowSurrogate(char)
     * @see    Character.UnicodeBlock#of(int)
     * @since  1.5
     */
public static boolean isHighSurrogate(char ch) {
     // Help VM constant-fold; MAX_HIGH_SURROGATE + 1 == MIN_LOW_SURROGATE
     return ch >= MIN_HIGH_SURROGATE && ch < (MAX_HIGH_SURROGATE + 1);
}

/**
     * Determines if the given {@code char} value is a
     * <a href="http://www.unicode.org/glossary/#low_surrogate_code_unit">
     * Unicode low-surrogate code unit</a>
     * (also known as <i>trailing-surrogate code unit</i>).
     *
     * <p>Such values do not represent characters by themselves,
     * but are used in the representation of
     * <a href="#supplementary">supplementary characters</a>
     * in the UTF-16 encoding.
     *
     * @param  ch the {@code char} value to be tested.
     * @return {@code true} if the {@code char} value is between
     *         {@link #MIN_LOW_SURROGATE} and
     *         {@link #MAX_LOW_SURROGATE} inclusive;
     *         {@code false} otherwise.
     * @see    Character#isHighSurrogate(char)
     * @since  1.5
     */
public static boolean isLowSurrogate(char ch) {
     return ch >= MIN_LOW_SURROGATE && ch < (MAX_LOW_SURROGATE + 1);
}
System.out.println(Character.isLowSurrogate('c'));false	// 就不是 SMP
System.out.println(Character.isLowSurrogate('\uDC56'));true
System.out.println(Character.isLowSurrogate('\uD856'));false
System.out.println(Character.isHighSurrogate('\uDC56'));false
System.out.println(Character.isHighSurrogate('\uD856'));true
```

#### charCount

> 就是判断当前codePoint是一个SMP还是一个BMP

```java
/**
     * Determines the number of {@code char} values needed to
     * represent the specified character (Unicode code point). If the
     * specified character is equal to or greater than 0x10000, then
     * the method returns 2. Otherwise, the method returns 1.
     *
     * <p>This method doesn't validate the specified character to be a
     * valid Unicode code point. The caller must validate the
     * character value using {@link #isValidCodePoint(int) isValidCodePoint}
     * if necessary.
     *
     * @param   codePoint the character (Unicode code point) to be tested.
     * @return  2 if the character is a valid supplementary character; 1 otherwise.
     * @see     Character#isSupplementaryCodePoint(int)
     * @since   1.5
     */
public static int charCount(int codePoint) {
     return codePoint >= MIN_SUPPLEMENTARY_CODE_POINT ? 2 : 1;
}
```

#### toCodePoint

>其实这个就是计算 SMP所能代表的 10进制整数。
>
>MIN_SUPPLEMENTARY_CODE_POINT + 
>
>(high << 10)  - （MIN_HIGH_SURROGATE << 10）+（low - MIN_LOW_SURROGATE）

```java
/**
     * Converts the specified surrogate pair to its supplementary code
     * point value. This method does not validate the specified
     * surrogate pair. The caller must validate it using {@link
     * #isSurrogatePair(char, char) isSurrogatePair} if necessary.
     *
     * @param  high the high-surrogate code unit
     * @param  low the low-surrogate code unit
     * @return the supplementary code point composed from the
     *         specified surrogate pair.
     * @since  1.5
     */
public static int toCodePoint(char high, char low) {
     // Optimized form of:
     // return ((high - MIN_HIGH_SURROGATE) << 10)
     //         + (low - MIN_LOW_SURROGATE)
     //         + MIN_SUPPLEMENTARY_CODE_POINT;
     return ((high << 10) + low) + (MIN_SUPPLEMENTARY_CODE_POINT
                                    - (MIN_HIGH_SURROGATE << 10)
                                    - MIN_LOW_SURROGATE);
}
```

#### codePointAt

> 返回 字符的codePoint  ，首先获取 当前字符， 如果是 高位， 再判断 是否有字符，然后 再判断是否为低位， 如果是， 返回两个字符表示的codePoint , 否则返回当前位 的 codePoint

```java
/**
     * Returns the code point at the given index of the
     * {@code CharSequence}. If the {@code char} value at
     * the given index in the {@code CharSequence} is in the
     * high-surrogate range, the following index is less than the
     * length of the {@code CharSequence}, and the
     * {@code char} value at the following index is in the
     * low-surrogate range, then the supplementary code point
     * corresponding to this surrogate pair is returned. Otherwise,
     * the {@code char} value at the given index is returned.
     *
     * @param seq a sequence of {@code char} values (Unicode code
     * units)
     * @param index the index to the {@code char} values (Unicode
     * code units) in {@code seq} to be converted
     * @return the Unicode code point at the given index
     * @exception NullPointerException if {@code seq} is null.
     * @exception IndexOutOfBoundsException if the value
     * {@code index} is negative or not less than
     * {@link CharSequence#length() seq.length()}.
     * @since  1.5
     */
public static int codePointAt(CharSequence seq, int index) {
     char c1 = seq.charAt(index);
     if (isHighSurrogate(c1) && ++index < seq.length()) {
          char c2 = seq.charAt(index);
          if (isLowSurrogate(c2)) {
               return toCodePoint(c1, c2);
          }
     }
     return c1;
}
```

