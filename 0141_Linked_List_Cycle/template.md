## Problem
https://leetcode.com/problems/linked-list-cycle/

## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### Approach
* 素直に手でやるならどうかと考えたやり方
* ListNodeを1つずつ走査しながらHashSetに訪問済のListNodeを順次格納していき、新たに訪問したListNodeがSetの中にあったらtrue
* 空間計算量がO(n)と高いので、もっといい方法ありそうと思いながら書いた

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode current = head;
        HashSet<ListNode> visitedSet = new HashSet<>();

        while (current != null) {
            if (visitedSet.contains(current)) {
                return true;
            }
            visitedSet.add(current);
            current = current.next;
        }
        return false;
    }
}
```

## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### 他の方のPR
* https://github.com/onyx0831/leetcode/pull/1
* https://github.com/t0hsumi/leetcode/pull/1
* https://github.com/ryuryu5121/Arai60/pull/1
* https://github.com/hajimeito1108/arai60/pull/1
* https://github.com/lilnoahhh/leetcode/pull/1


### 解法1. フロイドの循環検出法
* 1つ飛ばしで進むウサギ（fast）と1つずつ進む亀（slow）の2つのポインタを同時に同じリスト上を走らせると、サイクルがある場合必ずどこかのノードで合流するという考え方
    * https://en.wikipedia.org/wiki/Cycle_detection#Floyd's_tortoise_and_hare
* 感想: 2つ走らせるという発想はなかった。時間をかけても自分でゼロから思いつくのは厳しいと思う。考え方のパターンとしてストックしたい


```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode fast = head
        ListNode slow = head;
    
        while (fast != null && fast.next != null) { // Check the lead runner fast.
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {
                return true;
            }
        }
        return false;
    }
}
```

## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode fast = head
        ListNode slow = head;
    
        while (fast != null && fast.next != null) { // Check the lead runner fast.
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {
                return true;
            }
        }
        return false;
    }
}
```
