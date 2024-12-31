## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。


* 文字列を走査し、各文字の出現回数をHashMapに格納
* もう一度走査し、出現回数が1回だった文字列をreturnする。該当がなければ-1をreturn

```java
class Solution {
    public int firstUniqChar(String s) {
        HashMap<Character, Integer> charCounts = new HashMap<>();
        
        for (char c : s.toCharArray()) {
            if (charCounts.containsKey(c)) {
                charCounts.put(c, charCounts.get(c) + 1);
            } else {
                charCounts.put(c, 1);
            }
        }

        int length = s.length();
        for (int i = 0; i < length; i++) {
            if (charCounts.get(s.charAt(i)) == 1) return i;
        }

        return -1;
    }
}
```

### レビュー後の修正

* `getOrDefault` というMapメソッドを教えていただいたのでそれを用いて修正
    * https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html#getOrDefault-java.lang.Object-V-

```java
class Solution {
    public int firstUniqChar(String s) {
        HashMap<Character, Integer> charCounts = new HashMap<>();
        
        for (char c : s.toCharArray()) {
            charCounts.put(c, charCounts.getOrDefault(c, 0) + 1);
        }

        int length = s.length();
        for (int i = 0; i < length; i++) {
            if (charCounts.get(s.charAt(i)) == 1) return i;
        }

        return -1;
    }
}
```


## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。


### Fixed Size Array version

時間計算量: O(n)
空間計算量: O(1)

* 処理の流れはStep 1と同様。ただし、与えられる文字列がアルファベットのみであることを考慮しHashMapではなく固定サイズの配列を使用。結果として空間効率が良い。
* 個人的な感想としては、こちらの方が条件分岐も少なく済むため認知負荷が低いように感じる

```java
class Solution {
    public int firstUniqChar(String s) {
        int[] charCounts = new int[26];

        int length = s.length();
        for (int i = 0; i < length; i++) {
            charCounts[s.charAt(i) - 'a']++;
        }

        for (int i = 0; i < length; i++) {
            if (charCounts[s.charAt(i) - 'a'] == 1) {
                return i;
            }
        }

        return -1;
    }
}
```

### 他のPRを見て知った解法
#### LRU Data Management version

> LRU っぽいデータを使うのは、わりと聞きやすいので、考えておくといいでしょう。
> 1回だけ出てきた字を入れる Java の LinkedHashMap と2回以上出てきた字を入れる set を用意して操作するイメージです。
> https://github.com/Hurukawa2121/leetcode/pull/15#discussion_r1894986957

* こちらのコメントで知った解法。

時間計算量: O(n)
空間計算量: O(n)

* LRUのようなデータ管理が必要な場合はこちらのアルゴリズムが一般的のようなので覚えておく

```java
class Solution {
    public int firstUniqChar(String s) {
        LinkedHashMap<Character, Integer> onceUsedChars = new LinkedHashMap<>();
        HashSet<Character> multiUsedChars = new HashSet<>();
        int length = s.length();

        for (int i = 0; i < length; i++) {
            char c = s.charAt(i);
            if (!onceUsedChars.containsKey(c) && !multiUsedChars.contains(c)) {
                onceUsedChars.put(c, i);
            } else if (onceUsedChars.containsKey(c)) {
                multiUsedChars.add(c);
                onceUsedChars.remove(c);
            }            
        }
        return onceUsedChars.isEmpty() ? -1 : onceUsedChars.entrySet().iterator().next().getValue();
    }
}
```

### 参考PR
* https://github.com/Hurukawa2121/leetcode/pull/15
* https://github.com/colorbox/leetcode/pull/29
* https://github.com/philip82148/leetcode-arai60/pull/4
* https://github.com/tarinaihitori/leetcode/pull/15
* https://github.com/ryoooooory/LeetCode/pull/20

### レビュー後の修正点
* マジックナンバーのため定数を定義
* 不要なスペースを除去

```java
class Solution {
    private static final int NUM_LOWERCASE = 'z' - 'a' + 1;
    public int firstUniqChar(String s) {
        int[] charCounts = new int[NUM_LOWERCASE];

        int length = s.length();
        for (int i = 0; i < length; i++) {
            charCounts[s.charAt(i) - 'a']++;
        }

        for (int i = 0; i < length; i++) {
            if (charCounts[s.charAt(i) - 'a'] == 1) {
                return i;
            }
        }
        return -1;
    }
}
```


## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。

コードはStep2のFixed Size Array versionと同一

```java
class Solution {
    public int firstUniqChar(String s) {
        int[] charCounts = new int[26];

        int length = s.length();
        for (int i = 0; i < length; i++) {
            charCounts[s.charAt(i) - 'a']++;
        }

        for (int i = 0; i < length; i++) {
            if (charCounts[s.charAt(i) - 'a'] == 1) {
                return i;
            }
        }
        return -1;
    }
}
```
