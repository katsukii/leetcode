## Problem

https://leetcode.com/problems/kth-largest-element-in-a-stream/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach 1. Heap を使った方法

- わからなかったので回答を見て作成。ヒープはこれまで慣れてなかったのでこの問題でしっかりめにインプットした。時間があれば自分で実装したいところだが一旦後回し
- PriorityQueue
  - https://docs.oracle.com/javase/8/docs/api/java/util/PriorityQueue.html
- k 番目に大きい値だけを求めるために、サイズ k の最小ヒープを作り「常に上位 k 個の中で最小の値（= k 番目に大きい値）を根に持つ」よう管理するという方法。

時間計算量: 各 add が O(log k) → 合計 O(n log k)
空間計算量: O(k)

```java
class KthLargest {
    private final PriorityQueue<Integer> minHeap;
    private final int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        minHeap = new PriorityQueue<>(k);

        for (int num : nums) {
            add(num);
        }
    }

    public int add(int val) {
        if (minHeap.size() < k) {
            minHeap.offer(val); // add
        } else if (val > minHeap.peek()) {
            minHeap.poll(); // remove root
            minHeap.offer(val);
        }

        return minHeap.peek();
    }
}
```

## Step 2

他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### Approach 2. TreeMap を使った方法

- https://github.com/Ryotaro25/leetcode_first60/pull/9/files#r1619710596
  - > この問題で priority_queue にいきなりいくのは、私は実は違和感があります。
  - > 平衡二分木が C++ だったら map があり、これは順番に並んでいます。
- 平衡二分探索木
  - https://ja.wikipedia.org/wiki/%E5%B9%B3%E8%A1%A1%E4%BA%8C%E5%88%86%E6%8E%A2%E7%B4%A2%E6%9C%A8
- Java だと TreeMap がそれに当たるっぽい（赤黒木）

- TreeMap にスコアを Key、当該スコア個数を Value として保存

```java
class KthLargest {
    private final int k;
    private TreeMap<Integer, Integer> scores;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        this.scores = new TreeMap<>();

        for (int num : nums) {
            scores.put(num, scores.getOrDefault(num, 0) + 1);
        }
    }

    public int add(int val) {

        scores.put(val, scores.getOrDefault(val, 0) + 1);
        int rank = 0;
        for (int key : scores.descendingKeySet()) {
            rank += scores.get(key);
            if (rank >= k) {
                return key;
            }
        }
        return -1; // dummy
    }
}
```

- 上記の方法で試したところ、大量の add が走るテストケースで Time Limit Exceeded エラーとなった。要素削除がないため add のたびに scors が増え続けること原因
- 以下は 常に Top k に相当する要素だけを残すようにサイズを k に保つ
- k 番目に大きい要素は TreeMap の最小 Key に該当する

時間計算量: コンストラクタが O(n log k)、add 単発が O(log k)なので O(n log k)
空間計算量: O(k)

```java
class KthLargest {
    private final int k;
    private TreeMap<Integer, Integer> scores;
    private int scoreCount;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        this.scores = new TreeMap<>();
        this.scoreCount = 0;
        for (int num : nums) {
            add(num);
        }
    }

    public int add(int val) {
        if (scoreCount < k) {
            scores.put(val, scores.getOrDefault(val, 0) + 1);
            scoreCount++;
        } else {
            int kthScore = scores.firstKey();
            if (val > kthScore) {
                scores.put(val, scores.getOrDefault(val, 0) + 1);
                scoreCount++;
                updateScoreCount(kthScore);
                scoreCount--;
            }
        }
        return scores.firstKey();
    }

    private void updateScoreCount(int key) {
        int count = scores.get(key);
        if (count == 1) {
            scores.remove(key);
        } else {
            scores.put(key, count - 1);
        }
    }
}
```

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

```java

```
