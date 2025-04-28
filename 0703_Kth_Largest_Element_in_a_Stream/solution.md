## Problem

https://leetcode.com/problems/kth-largest-element-in-a-stream/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach

- わからなかったので回答を見て作成。ヒープはこれまで慣れてなかったのでこの問題でしっかり目にインプットした
- k 番目に大きい値だけを求めるために、サイズ k の最小ヒープを作り「常に上位 k 個の中で最小の値（= k 番目に大きい値）を根に持つ」よう管理するという方法。

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

```java

```

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

```java

```
