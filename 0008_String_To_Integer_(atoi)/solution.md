## Problem
https://leetcode.com/problems/string-to-integer-atoi/description/

## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### Approach
* 直感的に文字を一つずつ走査しながら文字のタイプによって適切な処理を行うという方法をとった
* この方法で簡単にいけるかなと思ったが、意外にハマってしまい時間がかかった
* 特に、符号や数値が出現済かどうかなどの状態管理、最大値および最小値になるパターンの判定に少しハマった
* 所感として、全体的にフラグ管理が多くとても読みづらいなと思いながらなんとか完成させたという感じだった

```java
class Solution {
    public int myAtoi(String s) {
        int result = 0;
        boolean appearedSign = false;
        boolean appearedDigit = false;
        boolean isLeading = true;
        int sign = 1;
        int length = s.length();
        for (int i = 0; i < length; i++) {
            char c = s.charAt(i);
            if (isLeading && Character.isWhitespace(c)) {
                continue;
            }
            isLeading = false;
            if (!appearedSign && !appearedDigit 
                && (c == '-' || c == '+')) {
                if (c == '-') sign = -1;
                    appearedSign = true;
                    continue;
            }
            if (Character.isDigit(c)) {
                appearedDigit = true;
                if (result > (Integer.MAX_VALUE - (c-'0')) / 10) {
                    return (sign == 1) ? Integer.MAX_VALUE : Integer.MIN_VALUE;
                }
                result = result * 10 + (c-'0');
            } else {
                break;
            }
        }
        return result * sign;
    }
}
```

## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### 解き方のパターン
#### 1-1. 文字列を1つずつ解析する方法
* Step1で実施した方法だが、一回のループで全文字を走査しようとすると状態管理フラグが多くなり煩雑になってしまう。そのためループを複数に分け、走査中の文字位置（index）を前のループから引き継ぐという方法をとることで状態管理を減らすことができる
* これによりネストが深くならず、フラグも減らせるため認知負荷が高くなることを避けられる

```java
public class Solution {
    public int myAtoi(String s) {
        if (s == null || s.isEmpty()) return 0;

        int length = s.length();
        int index = 0;
        int sign = 1;
        int result = 0;

        // Step 1: Skip leading spaces
        while (index < length && s.charAt(index) == ' ') index++;

        // Step 2: Check for sign
        if (index < length && (s.charAt(index) == '-' || s.charAt(index) == '+')) {
            sign = (s.charAt(index) == '-') ? -1 : 1;
            index++;
        }

        // Step 3: Parse digits and build result
        while (index < length && Character.isDigit(s.charAt(index))) {
            int digit = s.charAt(index) - '0';

            // Check for overflow before adding the digit
            if (result > (Integer.MAX_VALUE - digit) / 10) {
                return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }
            result = result * 10 + digit;
            index++;
        }

        return result * sign;
    }
}
```

#### 1-2. multiplyExact, addExactを用いるパターン
* レビューコメントにて、multiplyExact, addExactというメソッドを教えてもらった
    * https://github.com/katsukii/leetcode/pull/9/files#r1919816731
* 2つのint型数値の積、和を求め、int型からオーバーフローした際に例外を投げるというもの
* 計算の際にオーバーフローする条件を自分で求める必要がなくなる
* ただし、例外処理はif文よりも処理が重いためパフォーマンスはよくない

```java
public class Solution {
    public int myAtoi(String s) {
        if (s == null || s.isEmpty()) return 0;

        int length = s.length();
        int index = 0;
        int sign = 1;
        int result = 0;

        // Step 1: Skip leading spaces
        while (index < length && s.charAt(index) == ' ') index++;

        // Step 2: Check for sign
        if (index < length && (s.charAt(index) == '-' || s.charAt(index) == '+')) {
            sign = (s.charAt(index) == '-') ? -1 : 1;
            index++;
        }

        // Step 3: Parse digits and build result
        while (index < length && Character.isDigit(s.charAt(index))) {
            int digit = s.charAt(index) - '0';
            
            // calculate the digit
            try {
                result = Math.addExact(Math.multiplyExact(result, 10), digit);
            } catch (ArithmeticException e) {
                if (sign == -1) return Integer.MIN_VALUE;
                return Integer.MAX_VALUE;
            }
            index++;
        }

        return result * sign;
    }
}
```

#### 2. 正規表現を使う方法
* 正規表現を使用して文字列から有効な部分だけを抽出する方法
* 時間計算量、空間計算量ともにパフォーマンスは低かった。調べたところ、Javaの正規表現エンジンではバックトラッキングを使用しているため、非効率になる可能性がある

```java
import java.util.regex.*;
class Solution {
    public int myAtoi(String s) {
        if (s == null || s.isEmpty()) return 0;

        Pattern pattern = Pattern.compile("^[+-]?\\d+");
        Matcher matcher = pattern.matcher(s.trim());

        if (!matcher.find()) return 0;

        String numStr = matcher.group(); // extracted numbers
        try {
            return Integer.parseInt(numStr);
        } catch (NumberFormatException e) {
            // Overflow
            return numStr.charAt(0) == '-' ? Integer.MIN_VALUE : Integer.MAX_VALUE;
        }
    }
}
```

### 参考にしたPR
* https://github.com/Mike0121/LeetCode/pull/23/files
* https://github.com/rihib/leetcode/pull/10/files
* https://github.com/fhiyo/leetcode/pull/57/files
* https://github.com/Yoshiki-Iwasa/Arai60/pull/64/files

## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。

* 方法1を選択

```java
public class Solution {
    public int myAtoi(String s) {
        if (s == null || s.isEmpty()) return 0;

        int length = s.length();
        int index = 0;
        int sign = 1;
        int result = 0;

        // Step 1: Skip leading spaces
        while (index < length && s.charAt(index) == ' ') index++;

        // Step 2: Check for sign
        if (index < length && (s.charAt(index) == '-' || s.charAt(index) == '+')) {
            sign = (s.charAt(index) == '-') ? -1 : 1;
            index++;
        }

        // Step 3: Parse digits and build result
        while (index < length && Character.isDigit(s.charAt(index))) {
            int digit = s.charAt(index) - '0';

            // Check for overflow before adding the digit
            if (result > (Integer.MAX_VALUE - digit) / 10) {
                return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }
            result = result * 10 + digit;
            index++;
        }

        return result * sign;
    }
}
```
