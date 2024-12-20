## 問題
https://leetcode.com/problems/add-two-numbers/

## Step 1
### 試行1
* 正しく動作しないコード。少し粘ってみたがListNodeの末尾に余計な[0]が追加される
    * ex) 期待返り値[7,0,8]に対し実際返り値[7,0,8,0]

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummyNode = new ListNode(0);
        ListNode sumNode = dummyNode;

        int carry = 0;
        while(l1 != null || l2 != null) {
            int tempSum = carry;
            if (l1 != null) tempSum += l1.val;
            if (l2 != null) tempSum += l2.val;
            
            carry = tempSum / 10;
            sumNode.val = tempSum % 10;
            sumNode.next = new ListNode(0);
            sumNode = sumNode.next;

            if (l1 != null) l1 = l1.next;
            if (l2 != null) l2 = l2.next;
        }

        // The tail of the list
        if (carry > 0) {
            sumNode.val = carry;
        }

        return dummyNode;

    }
}
```

* 末尾に0が追加されてしまう原因は末尾に桁上りがない場合にもListNode(0)を生成してしまっているため。この条件チェックをするには、以下の条件式を入れなければならず非常に読みづらくなる
```java 
if (l1 != null && l1.next != null || l2 != null && l2.next != null || carry != 0) {
    sumNode.next = new ListNode();
    sumNode = sumNode.next;
}
```    

### 試行2
* お手本のコードをじっくり読んで再トライ。
* 最終的にdummyHeadではなくdummyHead.nextをreturnするのがポイント。
    * リストのcurrentNodeではなく常にnextに値を格納するとう考え方。このやり方であれば試行0のようにtailに不要なNodeが作成されることがなくなる
    * 試行1では結果を格納するために作成したNodeをHeadとしたListを返さないといけないという先入観がありうまくいかなかった
* なぜ.nextでreturnする必要があるのか？
    * dummyHeadのval自体は常に `0` のため。`[0 -> 7 -> 0 -> 8 -> null]` といった形で先頭に余分な0が含まれるリストになってしまっている
    * dummyHeadを使わずにリストを直接返す方法には、リストが空

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {

        ListNode dummyHead = new ListNode(0);
        ListNode current = dummyHead;

        int carry = 0;
        while (l1 != null || l2 != null || carry != 0) {
            int sum = carry;

            if (l1 != null) {
                sum += l1.val;
                l1 = l1.next;
            }

            if (l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }

            carry = sum / 10;
            current.next = new ListNode(sum % 10);
            current = current.next;
        }

    return dummyHead.next;

    }
}
```


## Step 2
* dummyHeadという変数名でなくsentinelとしているコードを見て命名候補として参考になった
* ただ、個人的には以下の理由でdummyHeadの方がわかりやすいかと思い、そのままにした
    * 先頭がdummyであることを明示的にするため
    * LinkedListを用いることが明らかな文脈ではheadの方がわかりやすいため

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {

        ListNode dummyHead = new ListNode(0);
        ListNode current = dummyHead;

        int carry = 0;
        while (l1 != null || l2 != null || carry != 0) {
            int sum = carry;

            if (l1 != null) {
                sum += l1.val;
                l1 = l1.next;
            }

            if (l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }

            current.next = new ListNode(sum % 10);
            current = current.next;
            carry = sum / 10;
        }

        return dummyHead.next;

    }
}
```

### 参考にしたPR
* https://github.com/Hurukawa2121/leetcode/pull/5/files
* https://github.com/YukiMichishita/LeetCode/pull/2/files

## Step 3
Step2と同一

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        
        ListNode dummyHead = new ListNode(0);
        ListNode current = dummyHead;

        int carry = 0;
        while (l1 != null || l2 != null || carry != 0) {
            int sum = carry;
            
            if (l1 != null) {
                sum += l1.val;
                l1 = l1.next;
            }

            if (l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }

            current.next = new ListNode(sum % 10);
            current = current.next;
            carry = sum / 10;
        }

        return dummyHead.next;
    }
}
```

> https://github.com/seal-azarashi/leetcode/pull/5/files#r1641826126
> dummy を使わないこともできますね。(番兵とかいいます。)
> この問題、番兵を使うか使わないかはあまり本質的ではないです。
> 
> 手でやるならばどうするか、仕事を複数人に依頼するならばどうするか、が見えていないのかなと思います。

レビューコメントを受けて、dummy（番兵）を使わない方法も検討してみたところ以下の方法で解決した

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        
        ListNode head = null, current = null;

        int carry = 0;
        while (l1 != null || l2 != null || carry != 0) {
            int sum = carry;

            if (l1 != null) {
                sum += l1.val;
                l1 = l1.next;
            }

            if (l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }

            ListNode tail = new ListNode(sum % 10);

            if (head == null) {
                head = tail;
                current = head;
            } else {
                current.next = tail;
                current = current.next;
            }

            carry = sum / 10;
        }

        return head; 
    }
}
```

結果的に処理が増えたので、番兵を使った方が読み手の認知負荷としては低そう