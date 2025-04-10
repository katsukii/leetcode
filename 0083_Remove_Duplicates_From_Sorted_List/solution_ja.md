## Problem

https://leetcode.com/problems/remove-duplicates-from-sorted-list/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach

時間計算量: O(n)
空間計算量: O(1)

- head からノードを一つずつ走査。現在の対象ノードの値が次のノードと同一であればリンクをさらにその次につなぎなおす。そうでない場合に対象ノードを次に移動
- 主役のノードは一人しかいないので、シンプルに node と命名した

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode node = head;

        while (node != null && node.next != null) {
            if (node.val == node.next.val) {
                node.next = node.next.next;
            } else {
                node = node.next;
            }
        }

        return head;
    }
}
```

## Step 2

他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

- continue で 処理をスキップする方法。現在ノードを次に進める処理が else の中にある事に違和感があったのでこちらの方が見やすい
  - https://github.com/shintaro1993/arai60/pull/6/files

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode node = head;

        while (node != null && node.next != null) {
            if (node.val == node.next.val) {
                node.next = node.next.next;
                continue;
            }
            node = node.next;
        }

        return head;
    }
}
```

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

```java

```
