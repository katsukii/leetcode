## Problem

https://leetcode.com/problems/reverse-linked-list/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach 1. 配列に一旦格納

時間計算量: O(n) - 2 回走査
空間計算量: O(n)

- 最初に思いついたやり方
- 各ノードを配列に一度保存して逆順に再度リンクを張っていく

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) {
            return null;
        }

        ListNode node = head;
        List<ListNode> nodes = new ArrayList<>();
        while (node != null) {
            nodes.add(node);
            node = node.next;
        }

        ListNode reverseHead = nodes.get(nodes.size() - 1);
        for (int i = nodes.size() - 1; i > 0; i--) {
            node = nodes.get(i);
            node.next = nodes.get(i - 1);
        }
        nodes.get(0).next = null;
        return reverseHead;
    }
}
```

## Step 2

他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

- https://github.com/shintaroyoshida20/leetcode/pull/12/files#diff-62f050819b0cae018db7450bb0f0341942d799ece31ce3d0d1d4a64d45578363R24
  - 再帰で解く方法があるようなのでチャレンジしたい
- https://github.com/irohafternoon/LeetCode/pull/9/files#r2020110286
  - 解き方 3 種類あるらしい
  - > 「リンク」を中心に見ていて、範囲内のすべてのリンクを順番にひっくり返すという方法
  - > 先頭の前にダミーをつけて、先頭の次のノードをダミーの後ろに挿入していく方法
  - > ひっくりかえす前の鎖と後の鎖を用意して、前のやつの先頭を後のやつの先頭につけていく方法

### Approach 2. リンクにフォーカスし順番に走査しながら逆接続する方法

時間計算量: O(n)
空間計算量: O(1)

- リンクを順番にひっくり返していく方法
- 単に next だと再接続前か後かがわかりづらかったので、old とつけた。この命名がレビュアーにどう思われるかは気になるところ

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode prev = null;
        ListNode node = head;

        while (node != null) {
            // Save node and reverse
            ListNode oldNext = node.next;
            node.next = prev;
            // Proceed nodes
            prev = node;
            node = oldNext;
        }

        return prev;  // new head
    }
}
```

### Approach 3. 番兵（Sentinel Node）を使用する方法

時間計算量: O(n)
空間計算量: O(1)

- 一番手前となる番兵を用意し、所与のリンクリストを走査しながら番兵の直後に割り込ませていく方法

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode dummyHead = new ListNode(0, null);
        ListNode node = head;

        while (node != null) {
            ListNode oldNext = node.next;
            node.next = dummyHead.next;
            dummyHead.next = node;
            node = oldNext;
        }
        return dummyHead.next;
    }
}
```

### Approach 4. 2 つのチェーンを用意する方法

時間計算量: O(n)
空間計算量: O(1)

- 空の逆順用リストの先頭用ノード（reversedHead）を用意する
- 元のリストの先頭ノードを別の変数に退避し、ノードを進める
- 先ほど退避した先頭ノードを元のリストから切り離し逆順用リストの先頭（rversedHead の手前）につなぎ直す
- reversedHead を先頭に後退させる
- これをを元のリストが null になるまで繰り返す

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode reversedHead = null;
        ListNode node = head;

        while (node != null) {
            // temporarily store current head
            ListNode oldHead = node;
            node = node.next;
            oldHead.next = reversedHead;
            reversedHead = oldHead;
        }

        return reversedHead;
    }
}
```

### Approach 5. バックトラッキングで再帰的に解く方法

時間計算量: O(n)
空間計算量: O(n) 再帰呼び出しのためスタックがリストの長さに比例

- 最後のノードに到達するまで再帰的に進み、そのノードを head として返す
- 再帰から戻りながら、各ノードのリンクを逆向きにしていく

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode newHead = reverseList(head.next);

        head.next.next = head;
        head.next = null;

        return newHead;
    }
}
```

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

- Approach 2 の方法で解いた。慣れたら oldNext とか書くより普通に next でもいい気がしてきた
- でも初見だとやはりわかりづらい気もする。悩ましい

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode prev = null;
        ListNode node = head;

        while (node != null) {
            ListNode oldNext = node.next;
            node.next = prev;
            prev = node;
            node = oldNext;
        }

        return prev;
    }
}
```
