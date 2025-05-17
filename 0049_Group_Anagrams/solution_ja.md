## Problem

https://leetcode.com/problems/group-anagrams/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach

- 途中で断念した方法
  - `Map<String, List<String>>` を作成し、strs の文字列を一つずつチェック
  - Map の key に当該文字列のアナグラムが存在しなければ key として追加する
  - Map の key に当該文字列のアナグラムが存在すれば value の配列に追加する
  - アナグラムの存在有無のチェック方法が分からず断念
- 結局正解を調べ、後述の Approach 1 の解法で Step1 を終えた

## Step 2

他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### Approach 1. 文字列を sort して Map の key にする方法

- 時間計算量: O(n \* k log k)
  -n は文字列の数、 k log k は各文字列の sort
- 空間計計算量: O(n \* k)

- 各文字列を char でソートし、HashMap の key として利用する方法
- Step1 で断念したアナグラムの有無チェックのやり方の答えがこれだった。

- 返り値の ArrayList 生成時に Map の values をそのまま放り込めばよい
  - https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> wordToAnagrams = new HashMap<>();

        for (String word : strs) {
            char[] chars = word.toCharArray();
            Arrays.sort(chars);
            String sortedWord = new String(chars);

            if (!wordToAnagrams.containsKey(sortedWord)) {
                wordToAnagrams.put(sortedWord, new ArrayList<>());
            }
            wordToAnagrams.get(sortedWord).add(word);
        }
        return new ArrayList<>(wordToAnagrams.values());
    }
}
```

```java
if (!wordToAnagrams.containsKey(sortedWord)) {
    wordToAnagrams.put(sortedWord, new ArrayList<>());
}
wordToAnagrams.get(sortedWord).add(word);
```

この部分は以下のように一行で書くこともできる（Java 8 以降）

```java
wordToAnagrams.computeIfAbsent(sortedWord, k -> new ArrayList<>()).add(word);
```

- https://github.com/HitoshiKoba/Arai60-public/pull/5/files#r2020661663
  - > 問題文の制約にはないでしょうが、Unicode のサロゲートペアや結合文字などがあると大変です。
    > 書くことを要求されないとは思いますが、何が大変なのかは理解しておきたいです。
    > https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.dofnod9ahqqt
- このコメントを見て軽く調べたら思った以上に大変そうっぽかった。自分なりに理解した内容は以下
  - サロゲートペアで不正な並びになる。絵文字や一部の漢字など BMP 外 (U+10000 以上) の文字は 2 code unit で 1 文字を構成する。これを code unit 単位でソートするとペアがバラバラになり不正な並びになる
  - 同じ見た目でも NFC (合成済み) と NFD (分解済み) が存在する結合文字列はバイト列が異なる。ソートキーも異なるため互いをアナグラムとみなせない

### Approach 2. 文字出現回数をキーにする方法

- 時間計算量: O(n \* k)
- 空間計計算量: O(n)

- https://github.com/tokuhirat/LeetCode/pull/12/files#diff-b3ce983a1ce9b7a4aeebe28733031ea99f7ebeef325f9b193a9ccab8d0bdc752R4-R5
  - アナグラムかどうかを ソートではなく、アルファベット 26 文字の出現カウントの一致で判定する方法
  - ソートを使う方法よりも、O(n)で済むため高速（n = 単語の長さ）
  - 文字列系はアルファベット 26 文字や ASCII256 文字を利用する解法が出てくるので引き出しに入れておいた方が良さそう
    - 過去解いたのだと "387. First Unique Character in a String" とか、"3. Longest Substring Without Repeating Characters" あたりもそう。

```java
public class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> wordToAnagrams = new HashMap<>();

        for (String word : strs) {
            int[] count = new int[26];
            for (char c : word.toCharArray()) {
                count[c - 'a']++;
            }
            String key = Arrays.toString(count);
            wordToAnagrams.computeIfAbsent(key, k -> new ArrayList<>()).add(word);
        }

        return new ArrayList<>(wordToAnagrams.values());
    }
}
```

### その他

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> wordToAnagrams = new HashMap<>();

        for (String word : strs) {
            char[] chars = word.toCharArray();
            Arrays.sort(chars);
            String sortedWord = new String(chars);

            if (!wordToAnagrams.containsKey(sortedWord)) {
                wordToAnagrams.put(sortedWord, new ArrayList<>());
            }
            wordToAnagrams.get(sortedWord).add(word);
        }
        return new ArrayList<>(wordToAnagrams.values());
    }
}
```
