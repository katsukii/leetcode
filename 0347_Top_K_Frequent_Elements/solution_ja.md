## Problem

// The URL of the problem

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach 1. Map と max-heap を使った解法

- 解法自体はすぐ思いついたが、Map での for 文、Max-heaap の書き方が分からず検索した

- Map に番号と出現回数のペアを保存
- 空の max-heap を用意し、Map に保存されているペアを出現回数を軸として挿入
- Max-heap から k 回 poll()する

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        HashMap<Integer, Integer> numCounts = new HashMap<>();
        for (int num : nums) {
            numCounts.put(num, numCounts.getOrDefault(num, 0) + 1);
        }
        PriorityQueue<int[]> countHeap = new PriorityQueue<>(
            (a, b) -> b[1] - a[1] // Max-heap
        );

        numCounts.forEach((num, count) -> {
            countHeap.offer(new int[] {num, count});
        });

        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = countHeap.poll()[0];
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

```java

```

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

```java

```
