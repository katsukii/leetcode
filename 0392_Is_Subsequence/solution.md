## Problem
https://leetcode.com/problems/is-subsequence/

## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### Approach
* 文字列sとtの二重ループ。sを一文字ずつ走査し、その中で文字列tも一文字ずつ走査する。文字列tの走査では、対象文字がsと一致しない限りイテレータを進め、一致した場合のみsを進める
* 文字列sの終端文字とtの文字が一致した場合はtrue、一致せずにループを終えた場合はfalseとする

上記の考え方で作ってみようとしたが結局断念。以下は動かないコード

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        int sLength = s.length();
        int tLength = t.length();
        int tIndex = 0;

        if (sLength > tLength) return false;

        for (int sIndex = 0; sIndex < sLength; sIndex++) { // traversal of s
            if (s.charAt(sIndex) == t.charAt(tIndex) && sIndex == sLength - 1) return true;
            while (tIndex < tLength && s.charAt(sIndex) != t.charAt(tIndex)) { // traversal of t
                if (tIndex == tLength) return false;
                tIndex++;
            }
        }
        return true;
    }
}
```

## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。


### 思考メモ
* 他の方のPRを見て、ループ一つにつき一文字列を走査するという固定観念に囚われていたことに気づいた
* あまり型にとらわれずに自由な発想で解き方を工夫することを学んだ

### 解法1. 
* 2つの文字列を一つのループ内で同時に走査し、tとsが一致したらsの方のindexを進める
* ループが終わった段階でsのindexが終端文字の1つ先の値になっていればtrue


```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        int sIndex = 0;
        int tIndex = 0;
        int sLength = s.length();

        while (sIndex < sLength && tIndex < t.length()) {
            if (s.charAt(sIndex) == t.charAt(tIndex)) sIndex++;
            tIndex++;
        }
        return sIndex == sLength;
    }
}
```

### 解法2. 無限ループ
* whileをtrueにするパターン

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        int sIndex = 0;
        int tIndex = 0;
        while (true) {
            if (sIndex == s.length()) return true;
            if (tIndex == t.length()) return false;
            if (s.charAt(sIndex) == t.charAt(tIndex)) sIndex++;
            tIndex++;
        }
    }
}
```

### レビュー後に学んだこと
* 状況によるが、オーバーヘッドよりも認知負荷軽減を優先した方がよい場合がある。今回のケースでは、 `s.length()` をわざわざ変数格納しても節約できるオーバーヘッドは一回あたり数クロック、ナノ秒のオーダー。それよりも、変数を負い続けるコストの方が高いという判断
    * https://github.com/katsukii/leetcode/pull/10#discussion_r1921489698

## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        int sIndex = 0;
        int tIndex = 0;
        while (sIndex < s.length() && tIndex < t.length()) {
            if (s.charAt(sIndex) == t.charAt(tIndex)) sIndex++;
            tIndex++;
        }
        return sIndex == s.length();
    }
}
```
