## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### 思考の流れ
* 自分が手作業でやるとしたらどのようなやり方で行うかを考えてみる
* 文字列から文字を一つずつ取り出すと同時に、出現した文字列を箱に格納していく。毎回箱の中身をチェックし、箱に格納済の文字が出現したらそのタイミングで格納済の文字の数を記録するとともに箱の中身をリセットする。これを終端にたどり着くまで繰り返し最大のカウント数を返り値とする
* この方法でHashSetを使って実装着手したものの、ある程度進めたところで欠陥に気づく。これだとカウント漏れが生じる
    * `abcabcbb`という文字列のうち、`abc`はカウントされるが`bca`はカウントされない
* この方法で進めるとなると文字の開始位置ごとに箱を用意して各文字それぞれに対して上記を実施する必要がある。この実装結果が以下

### 試行1 HashSetに格納する方法

時間計算量: O(n^2)
動作はするものの、LeetCode上でのパフォーマンスはかなり低い

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        
        int length = s.length();
        int longest = 0;
        for (int startInd = 0; startInd < length; startInd++) {
            
            HashSet<Character> appeared = new HashSet<>();
            
            char start = s.charAt(startInd);
            int endInd = startInd;
            while (endInd < length && !appeared.contains(s.charAt(endInd))) {
                appeared.add(s.charAt(endInd));
                endInd++;
            }
            longest = Math.max(longest, endInd - startInd);
        }
        return longest;
    }
}
```

他の方法を思いつかなかったため、コードを参照することに。

## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### スライディングウィンドウ法

時間計算量: O(n)

* 文字の位置を示すleftとrightをウィンドウとして定義し、先行するrightにより文字を1つずつ走査しながら各文字の最後の出現位置(index)を記録していく
* 対象文字が記録済（重複）の場合、ウィンドウの始端を最後に記録した位置の1つ先まで進める（対象位置が現在の始端より前であれば動かさない）
    * ポイントは始端を単に1つ進めるのではなく、記録済の文字の1つ先まで飛ばすこと
    * `abcabcbb`であれば、ウィンドウの終端が2番目のa(i=3)に達したタイミングで開始位置をb(i=1)に進める。終端がb(i=6)なら始端はc(i=5)に進める
    * 始端をジャンプさせる理由は、今見ている終端文字が最後に登場した位置以前は見ても意味がない（必ず重複する）ため
* これを、ウィンドウの最長の長さを更新しながら、終端が最後の文字に到達するまで繰り返す

#### HashMap version

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        HashMap<Character, Integer> lastIndex = new HashMap<>();
        int length = s.length(), left = 0, longest = 0;

        for (int right = 0; right < length; right++) {
            char rightChar = s.charAt(right); 

            if (lastIndex.containsKey(rightChar)) {
                left = Math.max(left, lastIndex.get(rightChar) + 1);
            }

            longest = Math.max(longest, right - left + 1);

            lastIndex.put(rightChar, right);
        }
        
        return longest;
    }
}
```

#### int[] version
入力が純粋なASCII文字列（a-z, A-Z, 0-9）であることが確定しているためこちらでも可。こちらの方がパフォーマンスが高い

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int[] lastIndex = new int[256];
        for (int i = 0; i < 256; i++) lastIndex[i] = -1;

        int length = s.length(), left = 0, longest = 0;

        for (int right = 0; right < length; right++) {
            char currentChar = s.charAt(right); 

            left = Math.max(left, lastIndex[currentChar] + 1);

            longest = Math.max(longest, right - left + 1);

            lastIndex[currentChar] = right;
        }
        
        return longest;
    }
}
```

#### 感想
* Step1で実施した、二重ループによりウィンドウを各文字ごとにリセットし直すやり方よりも遥かに効率が良い
* リアルな人間が手作業で実施することをイメージすると、以下のイメージになった
    * 最初の方法: ウィンドウの始端と終端それぞれに2人の人間が立ち、終端担当がウィンドウの距離を何往復もしながらカウントしていく
    * 二番目の方法: ウィンドウの終端担当1人のみが後ろ向きでカウントをスタートし、重複のないようにウィンドウの始端の位置を調整していきながら数える。この方法であれば一度も往復する必要がない
* 最初から二番目の方法を思いつけなかった理由として、このような走査をする場合、終端から始端を向いて（後ろ向きで）カウントするのではなく、無意識に始端に立って考え始めてしまっていると感じた
* 上記をふまえ、考え方のストックとして引き出しに入れたい

#### その他
* 他の方のコードではleft, rightとしているものが多く、そちらに変更した

#### 参考PR
* https://github.com/philip82148/leetcode-arai60/pull/3
* https://github.com/Yoshiki-Iwasa/Arai60/pull/42
* https://github.com/rossy0213/leetcode/pull/23
* https://github.com/fhiyo/leetcode/pull/48
* https://github.com/sakzk/leetcode/pull/3

## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int[] lastIndex = new int[256];
        for (int i = 0; i < 256; i++) lastIndex[i] = -1;

        int length = s.length(), left = 0, longest = 0;

        for (int right = 0; right < length; right++) {
            char currentChar = s.charAt(right); 

            left = Math.max(left, lastIndex[currentChar] + 1);

            longest = Math.max(longest, right - left + 1);

            lastIndex[currentChar] = right;
        }
        
        return longest;
    }
}
```
