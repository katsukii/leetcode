## Problem

https://leetcode.com/problems/find-k-pairs-with-smallest-sums/description/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach

- 簡単に解けるかと思ったらかなり苦戦した。体感として普段解いてる Medium よりも難しかった気がする
- 最初、それぞれの配列を担当する 2 つのポインタを用意し、ポインタをインクリメントしつつ配列要素のペア(i, j+1) or (i+1, j) いずれかの和が小さい方の組合せを結果用配列に格納していく方法の実装を試みた。しかし、これだとポインタが後戻りできないため多くの組合せを見過ごしてしまうことが原因でうまくいかなかった

  - [i, j]を[0, 0], [0, 1], [1, 1] と進めた後に[1, 0]に戻れない

- その後、以下のようにブルートフォースで実装。書きながらだめそうと思ったがやはり Memory Limit Exceeded で動かなかった。ブルートフォースはだいたいのケースで筋が悪いっぽい
  - 時間計算量は列挙に O(m·n)、ヒープへの挿入に O(m·n·log(m·n))
- ブルートフォースで全ペア分の配列を用意し、各ペアの合計値の heap に格納する
- result 用の配列に k 回分 heap から poll()して格納

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> pairs = new ArrayList<>();
        for (int u : nums1) {
            for (int v : nums2) {
                pairs.add(Arrays.asList(u, v));
            }
        }
        PriorityQueue<List<Integer>> sumMinHeap = new PriorityQueue<>(
            (a, b) -> (a.get(0) + a.get(1)) - (b.get(0) + b.get(1))
        );
        sumMinHeap.addAll(pairs);

        List<List<Integer>> result = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            result.add(sumMinHeap.poll());
        }
        return result;
    }
}
```

### Approach 1. k-way マージ（最小ヒープ）

時間計算量 O(k log k): ヒープは常に最大 k 個だけ要素を持つ。poll, offer 操作は O(log k)。これを最大 k 回繰り返す
空間計算量 O(k)

- 答えを探して書いた方法。「全組み合わせを生成してからソートする」のではなく、ソート済みという所与の配列の性質を利用して「必要な分だけ（最大 k 個）」のみを順に構築するため効率的
- 参考: https://yamase-note.com/study/leetcode/leetcode373/
- k-way merge アルゴリズムというらしい

  - k 本のソート済みリストを単一のソート済みリストにマージすることに特化したアルゴリズム
  - k-way merge は今回の最小ヒープ以外にも分割統治法、トーナメント木など他の種類もあるらしい
  - https://en.wikipedia.org/wiki/K-way_merge_algorithm

- 1. 最小ヒープを使用して、ペアの和とそのインデックスを管理する
- 2. 最初に (0, 0) のペアをヒープに入れる（最初の要素同士の組み合わせ）
- 3. ヒープから最小の和を持つペアを取り出し、結果配列に追加する
- 4. 取り出したペアに基づいて、次に候補となるペアをヒープに追加する
- 5. k 個のペアを取得するまで 3 と 4 を繰り返す

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) {
            return result;
        }

        // A heap having the pair of (i, j), which is sorted by nums' sum
        PriorityQueue<int[]> sumMinHeap = new PriorityQueue<>(
            (a, b) -> (nums1[a[0]] + nums2[a[1]]) - (nums1[b[0]] + nums2[b[1]])
        );

        // Initialize: The pair of each elem of nums1 and nums2[0]
        for (int i = 0; i < Math.min(k, nums1.length); i++) {
            sumMinHeap.offer(new int[]{i, 0});
        }
        // build k pairs
        while (!sumMinHeap.isEmpty() && result.size() < k) {
            int[] indexPair = sumMinHeap.poll();
            int i = indexPair[0];
            int j = indexPair[1];
            result.add(Arrays.asList(nums1[i], nums2[j]));

            if (++j < nums2.length) {
                sumMinHeap.offer(new int[]{i, j});
            }
        }
        return result;
    }
}
```

## Step 2

他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

- https://discord.com/channels/1084280443945353267/1183683738635346001/1187326805015810089

  - > 私だったら紙と鉛筆だけでやれといわれたら、10 問目は、エクセルみたいな表を作ります。
  - > 左に nums1、上に nums2 を書きます。
  - > で、左上が、絶対に一番小さいじゃないですか。
  - > まさか、全部の表を手作業で埋めて、全部比較してから一番小さいやつは、これ、ってやらないと思うんですよ。
  - > そうして、小さい順に s 個出力したとして、その次に小さいやつは、もうすでに使ったやつの右か下にあるやつの中にあるはずです。
  - > それを素直にコーディングするとどうなりますか。ということです。
  - 紙に表を作って考えるところまではいったものの、そこからアルゴリズムに落とすところで思考が止まってしまっていた。粘りが足りない

### Approach 2. 全探索+枝刈り+最大ヒープ

時間計算量（最悪ケース） O(n \* m \* log k) ※ ただし break が頻発すれば大幅に減る
空間計算量 O(k)

- https://github.com/shintaroyoshida20/leetcode/pull/15/files#r2073136397

  - > 二重ループで全部 priority queue に足していくんだけれども、溢れたら捨てていき、また、ループの中でそれ以降があふれると確定したら、枝刈りするということですね。
  - なるほど。全探索の過程であふれることが確定した時点でそれ以降は枝刈りすればいいのか。minHeap ではなく maxHeap を使えば常に最大が分かる

- Step1 の改良版。全探索しながら最大ヒープにペアを格納する。
- ヒープの要素数が k に達した段階で、ヒープの頂点（最大値）と現在のペアの sum を比較。現在のペアの方が大きければそれ以降の要素はスキップ（枝刈り）して次のイテレーションに進む

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        PriorityQueue<List<Integer>> sumMaxHeap = new PriorityQueue<>(
            (a, b) -> (b.get(0) + b.get(1)) - (a.get(0) + a.get(1))
        );
        for (int u : nums1) {
            for (int v : nums2) {
                List<Integer> pair = Arrays.asList(u, v);
                // Add elems until the numb of elems reaches k.
                if (sumMaxHeap.size() < k) {
                    sumMaxHeap.offer(pair);
                    continue;
                }

                List<Integer> maxPair = sumMaxHeap.peek();
                int maxSum = maxPair.get(0) + maxPair.get(1);
                if (maxSum <= u + v) {
                    break;
                }
                sumMaxHeap.offer(pair);
                sumMaxHeap.poll();
            }
        }

        List<List<Integer>> result = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            result.add(sumMinHeap.poll());
        }
        return result;
    }
}
```

### Approach 3. Set+最小ヒープを用いた隣接グリッド探索

時間計算量 O(k log k)
空間計算量 O(k)

- https://github.com/shintaro1993/arai60/pull/14/files#r2060221928

  - > 基本的に(i+1,j)と(i,j+1)のペアを入れ、同じペアを二度入れないようにすでに追加したペアを set で管理する方法もあります

- 0,0 からスタートし、右隣と下隣を探索候補としてし最小ヒープに格納していく。その際、すでに訪問済のマスはスキップする
- ヒープの頂点を取り出し結果配列に追加する
- ポイントは、常に候補の中から最小のマスが探索対象となること。このため合理的な順で探索が進められる

- コメント: Python の tuple みたいな Pair というクラスが Java にあるのを初めて知った

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) {
            return result;
        }

        PriorityQueue<int[]> sumMinHeap = new PriorityQueue<>(
            (a, b) -> (nums1[a[0]] + nums2[a[1]]) - (nums1[b[0]] + nums2[b[1]])
        );
        for (int i = 0; i < Math.min(k, nums1.length); i++) {
            sumMinHeap.offer(new int[]{i, 0});
        }
        while (!sumMinHeap.isEmpty() && result.size() < k) {
            int[] indexPair = sumMinHeap.poll();
            int i = indexPair[0];
            int j = indexPair[1];
            result.add(Arrays.asList(nums1[i], nums2[j]));

            if (++j < nums2.length) {
                sumMinHeap.offer(new int[]{i, j});
            }
        }
        return result;
    }
}
```

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        PriorityQueue<List<Integer>> sumMaxHeap = new PriorityQueue<>(
            (a, b) -> (b.get(0) + b.get(1)) - (a.get(0) + a.get(1))
        );
        for (int u : nums1) {
            for (int v : nums2) {
                List<Integer> pair = Arrays.asList(u, v);
                if (sumMaxHeap.size() < k) {
                    sumMaxHeap.offer(pair);
                    continue;
                }

                List<Integer> maxPair = sumMaxHeap.peek();
                int maxSum = maxPair.get(0) + maxPair.get(1);
                if (maxSum <= u + v) {
                    break;
                }
                sumMaxHeap.offer(pair);
                sumMaxHeap.poll();
            }
        }

        List<List<Integer>> result = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            result.add(sumMinHeap.poll());
        }
        return result;
    }
}
```
