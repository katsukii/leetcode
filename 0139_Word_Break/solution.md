## Problem
https://leetcode.com/problems/word-break/

## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### Approach
* まず手作業でやるとしたらどうするかを考え、スライディングウィンドウ法を適用しようとした。
* 文字列sの左端から1文字ずつwordDictと突合し、合わなければウィンドウの終端を1文字進める。合った場合はウィンドウの開始位置を終端の横までジャンプさせる。これを繰り返しウィンドウの始端が文字列の末尾以降にたどり着けばtrue、逆に終端が先にたどり着いた場合はfalse
* 結果的に一部のケースで失敗
    * 成功: `s = "leetcode", wordDict = ["leet","code"]` -> 期待 true, 結果: true
    * 成功: `s = "applepenapple", wordDict = ["apple","pen"]` -> 期待 true, 結果: true
    * 成功: `s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]` -> 期待 false, 結果: false
    * **失敗**: `s = "aaaaaaa", wordDict = ["aaaa","aaa"]` -> 期待: true, 結果: false
* 以下が失敗コード

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        return canSegment(s, wordDict, s.length(), 0, 1);
    }

    public boolean canSegment(String s, List<String> wordDict, int length, int start, int end) {
        if (start == s.length()) return true;
        if (end == s.length() + 1) return false;       
        String target = s.substring(start, end);
        for (String word : wordDict) {
            if (word.equals(target)) start = end;
        }
        return canSegment(s, wordDict, length, start, end + 1);
    }
}
```

* 上記のコードでは、後戻り確認ができない仕様のため、あるstart位置の文字列が異なるend位置によってwordDict内の複数の単語とマッチするケースで先にマッチした方（ウィンドウが短い方）のみを進めてしまうため考慮漏れが発生してしまう
* 例) s = "aaaaaaa", wordDict = ["aaaa","aaa"]
    * aaa aaa と判定処理してしまい、aが一文字余ってしまうため期待trueに反して結果falseとなり失敗する
* 今回の問題がスライディングウィンドウ法だけで解けないのは、すべての可能な区切り位置が探索できない（単方向スライドでは不十分）から
* よって、すべての区切り位置を探索可能な動的計画法が適している


## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### 参考にしたPR
* https://github.com/hroc135/leetcode/pull/37
* https://github.com/Ryotaro25/leetcode_first60/pull/43
* https://github.com/goto-untrapped/Arai60/pull/20


* いくつか実装方法がある模様。今回はDP（トップダウン）、DP（ボトムアップ）、トライ木（Trie）、BFS、を実装

### 解法1. 動的計画法（トップダウン、再帰 + メモ化）
* 部分問題の結果を後から再利用する前提で再帰的に計算し、その結果をメモ化していく
1. 辞書の中から  wordを1つずつ取り出し、現在のindex（start） から始まる部分文字列と比較
2. 一致する場合、確認対象の末尾index `end = start + word.length()` を次の開始位置として再帰的に分割可能性を判定
3. 再帰結果が成功（1）なら、現在の位置までの再帰も成功としてreturn（記録はなし）
    * ここでいう成功 = 文字列 s が末尾まで完全に分割可能であることを示す。そのため1を返せる時点で再利用の可能性はない
4. すべての単語を試しても分割可能でない場合、そのstart位置は失敗として 0 を記録

* 変数にdpと命名したくなるが、他の方のレビューを見て何が格納されているかを示す名前に修正した

```java
class Solution {
    final int UNVISITED = -1;
    final int UNBREAKABLE = 0;
    final int BREAKABLE = 1;

    public int checkBreakability(String s, List<String> wordDict, int start, int[] dpBreakability) {
        if (start == s.length()) return BREAKABLE; // Base case
        if (dpBreakability[start] != UNVISITED) return dpBreakability[start]; // Memorized case
        
        for (String word : wordDict) {
            int end = start + word.length();
            if ((end <= s.length()) && s.regionMatches(start, word, 0, word.length())) {
                if (checkBreakability(s, wordDict, end, dpBreakability) == BREAKABLE) {
                    return BREAKABLE; // Breakable
                }
            }
        }

        return dpBreakability[start] = UNBREAKABLE;
    }

    public boolean wordBreak(String s, List<String> wordDict) {
        int[] dpBreakability = new int[s.length() + 1];
        for (int i = 0; i <= s.length(); i++) dpBreakability[i] = UNVISITED;
        return checkBreakability(s, wordDict, 0, dpBreakability) == BREAKABLE;
    }
}
```

以下は `dpBreakability` をBool値で管理できるようにHashMap型に変更した方法

```java
class Solution {
    public boolean checkBreakability(String s, List<String> wordDict, int start, HashMap breakabilityMap) {
        if (start == s.length()) return true; // Base case
        if (breakabilityMap.containsKey(start)) return false; // Memorized case
        
        for (String word : wordDict) {
            int end = start + word.length();
            if ((end <= s.length()) && s.regionMatches(start, word, 0, word.length())) {
                if (checkBreakability(s, wordDict, end, breakabilityMap)) return true;
            }
        }

        breakabilityMap.put(start, false);
        return false; // Not breakable
    }

    public boolean wordBreak(String s, List<String> wordDict) {
        HashMap breakabilityMap = new HashMap<Integer, Boolean>();
        return checkBreakability(s, wordDict, 0, breakabilityMap);
    }
}
```

### 解法2. 動的計画法（ボトムアップ）
* dbBreakabiliity[i]は文字列sの1文字目からi文字目までのwordDictの単語による分割可能性を指す
    * dbBreakabiliity[0]は空文字列のため常に分割可能
* 部分文字列の終端、始端をindexとする二重ループにより文字列sの部分文字列を網羅的に走査する
* 以下のAND条件を満たすとき`dbBreakability[end] = true` となる
    1. 確認済のインデックスstartまでの文字列が既に分割可能である
    2. 新たに確認するstartからendまでの部分文字列がwordDictに含まれる

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> wordSet = new HashSet<>(wordDict);
        boolean[] dpBreakability = new boolean[s.length() + 1];
        dpBreakability[0] = true;

        for (int end = 1; end <= s.length(); end++) {
            for (int start = 0; start < end; start++) {
                if (dpBreakability[start] && wordSet.contains(s.substring(start, end))) {
                    dpBreakability[end] = true;
                    break;
                }
            }
        }

        return dpBreakability[s.length()];
    }
}
```

* startsWith()というメソッドを使う方法も教わった
* こちらは認知不可が低いことに加え、パフォーマンスが上記コードよりも高い
    * 上記コードの計算量がO(len(s)^2)であるのに対し、以下はO(len(s) * len(wordDict))

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        boolean[] dpBreakability = new boolean[s.length() + 1];
        dpBreakability[0] = true;

        for (int start = 0; start < s.length(); start++) {
            if (!dpBreakability[start]) {
                continue;
            }

            for (String word : wordDict) {
                if (s.startsWith(word, start)) {
                    dpBreakability[start + word.length()] = true;
                }
            }
        }

        return dpBreakability[s.length()];
    }
}
```

### 解法3. Trie（トライ木）+ DP
* トライ木とは文字列検索用のデータ構造。文字列の先頭部分(prefix)の共通部分を木構造の形で共有&保存することでO(N)での検索を可能にする（Nは検索文字列の長さ）というコンセプト
* 各ノードは1文字を表し、ノード間のエッジが文字列の構成を示す
    * ※ 複数文字を表すパターンも存在する
    * 例えば "cat" と "cats" を保存する場合、接頭部分 "cat" を共有し余分なメモリを節約する

```
c - a - t (isWordEnd=true)
        \
        s (isWordEnd=true)
```

* 基本的な機能としては挿入と検索の2つ。挿入時はノードを1文字ずつたどり存在しなければ新しく作成する。検索時も同様に一文字ずつ文字列の末尾までノードをたどり、最後のノードが終了フラグを持っているか確認する
* 以下、putWordsToTrieメソッドが挿入に、checkメソッドが検索に該当。checkメソッドでは、現在のindexから始まる部分文字列を探索しながら、すでに計算した結果をdpマップで再利用することで再帰の深さを抑えている
* 処理の流れとしては、putWordsToTrieでwordDictのトライ木を構築し、そのあとcheckで文字列sをトライ木のrootから順に探索。分割可能ならtrueを、そうでなければfalseをdpBreakabilityに格納する。最終的にcheck(s, 0)の結果をreturnする
* ちなみにTrie + DFSの解法もあるようだが、時間を使いすぎてしまうため今回は割愛

```java
class Trie{
    public Trie[] next = new Trie[26];
    public boolean isWordEnd = false;
    
    public void addWord(String word) {
        Trie current = this;
        for(int i= 0 ; i < word.length(); i++){
            int index = word.charAt(i) - 'a';
            if(current.next[index] == null) {
                current.next[index] = new Trie();
            }
            current = current.next[index] ;
        }
        current.isWordEnd = true;
    }
    
    public boolean match(String s, int currentIndex, HashMap<Integer, Boolean> dpBreakability) {
        if (currentIndex == s.length()) return true;
        if (dpBreakability.containsKey(currentIndex)) {
            return dpBreakability.get(currentIndex);
        }

        Trie current = this;
        boolean result = false;
        for (int i = currentIndex; i < s.length(); ++i) {
            int index = s.charAt(i) - 'a';
            if (current.next[index] == null) break;
            current = current.next[index];
            if (current.isWordEnd && match(s, i + 1, dpBreakability)) {
                result = true;
                break;
            }
        }
        dpBreakability.put(currentIndex, result);
        return result;
    }
}

class Solution {
    private Trie trie = new Trie();

    public boolean wordBreak(String s, List<String> wordDict) {
        for (String word : wordDict) {
            trie.addWord(word);
        }
        return trie.match(s, 0, new HashMap<>());
    }
}
```

### 解法4. BFS（幅優先探索）
* 文字列のある位置startから次の位置endまでのs.substring(start, end) が wordDict に含まれる単語であるかを確認するというコンセプト
* 各indexをグラフのノードとし、startからend（start < end ≤ s.length()）への移動はその部分文字列がwordDictに含まれている場合にのみ可能なエッジと考える
    * 例：s = "leetcode", wordDict = ["leet", "code"]
	* index 0 → 4（“leet”）が可能
	* index 4 → 8（“code”）が可能
* 最後のノードに到達できれば文字列全体を分割できたことになる
* 処理の流れとしては、今いる位置（start、最初はindex 0）からみて、s.substring(start, end)をチェック。分割可能なすべての単語の終端index（end）を次の探索start位置としてキューに追加していく

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> wordSet = new HashSet<>(wordDict);
        Queue<Integer> queue = new ArrayDeque<>();
        boolean[] visited = new boolean[s.length()];

        queue.add(0);

        while (!queue.isEmpty()) {
            int start = queue.poll();
            if (visited[start]) continue;
            visited[start] = true;

            for (int end = start + 1; end <= s.length(); end++) {
                if (wordSet.contains(s.substring(start, end))) {
                    if (end == s.length()) return true;
                    queue.add(end);
                }
            }
        }

        return false;
    }
}
```



## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。

LeetCode上でパフォーマンスの高かった動的計画法（トップダウン、再帰 + メモ化）を選択して練習した

```java
class Solution {
    public int checkBreakability(String s, List<String> wordDict, int start, int[] dpBreakability) {

        if (start == s.length()) return 1; // Base case
        if (dpBreakability[start] != -1) return dpBreakability[start]; // Memorized case
        
        for (String word : wordDict) {
            int end = start + word.length();
            if ((end <= s.length()) && s.regionMatches(start, word, 0, word.length())) {
                if (checkBreakability(s, wordDict, end, dpBreakability) == 1) {
                    return 1; // Breakable
                }
            }
        }

        return dpBreakability[start] = 0; // Not breakable
    }

    public boolean wordBreak(String s, List<String> wordDict) {
        int[] dpBreakability = new int[s.length() + 1];
        for (int i = 0; i <= s.length(); i++) dpBreakability[i] = -1; // -1 means unchecked
        return checkBreakability(s, wordDict, 0, dpBreakability) == 1;
    }
}
```

レビューを受けて、ボトムアップDPを再選択

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        boolean[] dpBreakability = new boolean[s.length() + 1];
        dpBreakability[0] = true;

        for (int start = 0; start < s.length(); start++) {
            if (!dpBreakability[start]) {
                continue;
            }

            for (String word : wordDict) {
                if (s.startsWith(word, start)) {
                    dpBreakability[start + word.length()] = true;
                }
            }
        }

        return dpBreakability[s.length()];
    }
}
```