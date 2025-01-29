## Problem
https://leetcode.com/problems/linked-list-cycle-ii/

## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### Approach
* フロイドの循環検出法の延長で何かありそうだと思ったが、頭をひねっても思いつきそうになかったため、Linked List Cycle 1 (以下LLC1)と同様のSetを使った解法で実行

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode node = head;
        HashSet<ListNode> visited = new HashSet<>();

        while (node != null) {
            if (visited.contains(node)) {
                return node;
            }
            visited.add(node);
            node = node.next;
        }
        return null;
    }
}
```

## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### 参考コード
* https://github.com/lilnoahhh/leetcode/pull/2
* https://github.com/ryuryu5121/Arai60/pull/2
* https://github.com/t0hsumi/leetcode/pull/2

### 解法1. フロイドの循環検出法
* LLC1の延長の解き方。フロイドの循環検出法を用いて以下の2ステップで解く
* 1. 2つのポインタの合流点を検出
    * LLC1と同様。returnではなくbreakでループを終わらせて後続の処理へ進む
    * breakに入らずにループが終了した場合はfastが終端にたどり着いたことを意味するため nullをreturnする
* 2. サイクルの開始点の検出
    * slowの位置のみheadに戻し、fastとslowを1つずつ進める。すると、サイクルの開始点で合流するため検出できる

#### 上記の解法の根拠
* 以下と仮定する
    * a: リストのheadからサイクル開始点までの距離
    * b: サイクル開始点から2つのポインタの合流点までの距離
    * c: サイクルの長さ
* fastはslowの2倍の距離を進むため `a + b + k * c = 2(a + b)` といえる
    * kはサイクルの周回数（整数）
* 上記より `k * c = a + b` が導ける。つまり、サイクルを k 回周回する距離は、リストのheadから合流点までの距離 a + b と等しいといえる
* 合流後、fastをリストの開始点 head に戻し、slow と同じ速度（1stepずつ）で動かすとすると、a分の距離を進めばサイクルの開始点までたどり着ける。
* 一方、その間 slow も同様にサイクル合流点から a 分の距離を進むことになる。slowはもともとサイクル開始点からb分の距離を進んだ位置でfastと合流しているため、サイクル開始点から進んだ距離は b + a となる
* `a + b = k * c` より、ちょうどサイクルをk周したことになり、fastが開始点に着くと同時にslowも開始点にたどりつく。よってfastとslowが合流した地点がサイクル開始点といえる

* 感想: 科学手品として割り切りつつも、ロジックとして理解はしておきたいと思い腹落ちさせるのに2時間くらいかけてしまった。とても効率の悪いことをしている気がするのでもう少し時間の使い方を考えたい

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;

        // Step1: Move pointers to the meeting point.
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {
                break;
            }
        }

        if (fast == null || fast.next == null) return null;

        // Step2: Find the start of the cycle
        slow = head;
        while (slow != fast) {
            fast = fast.next;
            slow = slow.next;
        }

        return fast;
    }
}
```

## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。


```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;

        // Step1: Move pointers to the meeting point.
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {
                break;
            }
        }

        if (fast == null || fast.next == null) return null;

        // Step2: Find the start of the cycle
        slow = head;
        while (slow != fast) {
            fast = fast.next;
            slow = slow.next;
        }

        return fast;
    }
}
```
