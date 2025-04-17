## Problem

https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach

- 3 回の独立したループを実行
  - 1 回目: リストを走査して、対象ノードの値と次のノードの値とが重複している場合は、値を HashSet に格納
  - 2 回目: 再度頭から走査。先頭が重複ノードだった場合に備え、重複していないノードまで進める
  - 3 回目: 改めて新しい先頭から走査。HashSets を確認しながら値重複ノードはスキップしつつ、重複がないノード同士をつなげ直す
- 感想
  - なんとなくもっと見やすくて効率の良い方法がありそうなモヤモヤがありながらも、思いつかなかったので上記の発想で走りきったという感じ

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode node = head;
        HashSet<Integer> duplicateVals = new HashSet<>();

        while (node != null && node.next != null) {
            if (node.val == node.next.val) {
                duplicateVals.add(node.val);
            }
            node = node.next;
        }

        node = head;
        while (node != null && duplicateVals.contains(node.val)) {
            if (node.next == null) { // All nodes are duplicated.
                node = null;
                break;
            }
            node = node.next;
        }

        head = node;
        ListNode lastUnique = head;
        if (node != null) {
            node = node.next;
        }
        while (node != null) {
            if (duplicateVals.contains(node.val)) {
                lastUnique.next = null;
            } else {
                lastUnique.next = node;
                lastUnique = node;
            }
            node = node.next;
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

### Approach 1

- 3 つのポインタを使用
  - node: メインでリストを一つずつ走査するノード。head からスタート
  - dummyHead: 番兵として元のリストの head の手前に配置する
    - 番兵 を先頭に置くことで、本来の head 自体が削除対象となるケース（1->1->2 など）を考慮したループ処理が不要になる
  - lastUnique: 重複のない最後のノードを追跡。最初は dummyHead
- リストを走査し一つ先のノードの値をチェックしていく。重複ノードはスキップ、重複なしノードには lastUnique と lastUnique.next をリンクさせる
- 上記を繰り返すことで dummyHead.next が必ず重複のないリストの先頭になる

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode node = head;
        ListNode dummyHead = new ListNode(0, node);
        ListNode lastUnique = dummyHead;

        while (node != null) {
            if (node.next != null && node.val == node.next.val) { // Node is duplicate
                while (node.next != null && node.val == node.next.val) { // Skip to last duplicated node
                    node = node.next;
                }
                lastUnique.next = node.next; // Next of last duplicated node
            } else { // Node is not Duplicate
                lastUnique = lastUnique.next;
            }
            node = node.next;
        }
        return dummyHead.next;
    }
}
```

### Approach 2

- こちらは dummyHead と node の 2 人の登場人物だけで完了できる方法
- Approach 1 とは逆の発想で、重複なしノードを一気にスキップし、その後重複が現れる限り一つ先につなぎなおすという方法
- 以下を参考にした
  - https://github.com/5ky7/arai60/pull/5/files#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R160-R179
- 感想
  - 自らの無意識の選択をメタ認知した上で別の視点はないか探すという意識は常に持っておきたいと思った

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode dummyHead = new ListNode(0, head);
        ListNode node = dummyHead;

        while (node.next != null) {
            if (node.next.next == null || node.next.val != node.next.next.val) {
                node = node.next;
                continue;
            }

            int duplicateVal = node.next.val;
            while (node.next != null && node.next.val == duplicateVal) {
                node.next = node.next.next;
            }
        }

        return dummyHead.next;
    }
}
```

### その他参考にした PR

- https://github.com/shintaro1993/arai60/pull/7/files
- https://github.com/h1rosaka/arai60/pull/6/files
- https://github.com/shintaroyoshida20/leetcode/pull/7/files

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

Approach 1

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode node = head;
        ListNode dummyHead = new ListNode(0, node);
        ListNode lastUnique = dummyHead;

        while (node != null) {
            if (node.next != null && node.val == node.next.val) { // Node is duplicate
                while (node.next != null && node.val == node.next.val) { // Skip to last duplicated node
                    node = node.next;
                }
                lastUnique.next = node.next; // Next of last duplicated node
            } else { // Node is not Duplicate
                lastUnique = lastUnique.next;
            }
            node = node.next;
        }
        return dummyHead.next;
    }
}
```

## Step 4

> dummyHead 置かないと分岐が増えるけれども、それでも解くことはできます。

というコメントをいただいたので実装してみた。やはり

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        // Skip duplicated Node
        while (head != null && head.next != null && head.val == head.next.val) {
            int dupVal = head.val;
            while (head != null && head.val == dupVal) {
                head = head.next;
            }
        }

        if (head == null) return null;

        ListNode unique = head;
        ListNode node = head.next;

        while (node != null && node.next != null) {
            if (node.val == node.next.val) {
                int dupVal = node.val;
                while (node != null && node.val == dupVal) {
                    node = node.next;
                }
                unique.next = node;
            } else {
                unique.next = node;
                unique = node;
                node = node.next;
            }
        }

        return head;
    }
}
```

再帰の実装

- 面白い。思った以上にシンプルにいけた

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        return deleteDuplicatesHelper(head, -101); // -101: out of val
    }

    private ListNode deleteDuplicatesHelper(ListNode head, int duplicatedValue) {
        if (head == null) {
            return null;
        }

        // Start or Continue of Duplicate
        if ((head.next != null && head.val == head.next.val) || head.val == duplicatedValue) {
            return deleteDuplicatesHelper(head.next, head.val);
        }

        head.next = deleteDuplicatesHelper(head.next, -101); // No duplicatedValue
        return head;
    }
}
```
