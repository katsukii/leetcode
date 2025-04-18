## Problem

https://leetcode.com/problems/meeting-rooms/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach

- 所与の配列を会議の開始時刻で昇順にソートする
- 会議を走査し、次の会議が、現在の会議終了前に始まる場合 false を返す

```java
class Solution {
    public boolean canAttendMeetings(int[][] intervals) {
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        for (int i = 0; i < intervals.length - 1; i++) {
            int currentEnd = intervals[i][1];
            int nextStart = intervals[i + 1][0];
            if (nextStart < currentEnd) {
                return false;
            }
        }
        return true;
    }
}
```

## Step 2

他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### Approach 1. 会議を開始時刻で Sort し、次と被ってないかチェックする方法

時間計算量: O(n log n) - sorting
空間計算量: O(n) - copy of intervals

- https://github.com/Mike0121/LeetCode/pull/27/files#r1633298146
  - > 入力データを破壊するのは、やや違和感があります
  - たしかによくよく考えると呼び出し元のコードにも影響を及ぼす可能性がありよくないなと思った

```java
class Solution {
    public boolean canAttendMeetings(int[][] intervals) {
        int[][] sortedIntervals = Arrays.copyOf(intervals, intervals.length);
        Arrays.sort(sortedIntervals, Comparator.comparingInt(a -> a[0]));
        for (int i = 0; i < sortedIntervals.length - 1; i++) {
            int currentEnd = sortedIntervals[i][1];
            int nextStart = sortedIntervals[i + 1][0];
            if (nextStart < currentEnd) {
                return false;
            }
        }
        return true;
    }
}
```

### Approach 2. Priority Queue(Heap) を使った方法

時間計算量: O(n log n)
空間計算量: O(n)

- https://github.com/goto-untrapped/Arai60/pull/60/files?diff=unified&w=0#diff-b9d4ab739b4c1ab8035480d9417c186addd4d06b0f1fd82d5ecaa373998694c9R109-R125
- Sort を優先度キュー（最小ヒープ）を使って行う
- 最も早く始まる会議から順に取り出して比較する
- 前の会議の終了時間が次の会議の開始時間よりも遅い場合（重複がある場合）、false を返す

```java
class Solution {
    public boolean canAttendMeetings(int[][] intervals) {
        PriorityQueue<int[]> intervalHeap = new PriorityQueue<>(
            Comparator.comparingInt(a -> a[0])
        );
        for (int[] interval : intervals) {
            intervalHeap.offer(interval);
        }

        int[] prevMeeting = intervalHeap.poll();
        while (!intervalHeap.isEmpty()) {
            int[] currentMeeting = intervalHeap.poll();
            if (prevMeeting[1] > currentMeeting[0]) {
                return false;
            }
            prevMeeting = currentMeeting;
        }
        return true;
    }
}
```

### Approach 3. 出席時刻を刻んで他の会議と突合する方法

時間計算量: O(n \* m) - 会議数 × 会議の時間の長さ
空間計算量: O(n \* m)

- https://github.com/shining-ai/leetcode/pull/55/files#diff-e4aecb29a1e99485619ccf14730156b6e561b761159773644fde2a6000c54c6bR2-R11
- https://github.com/goto-untrapped/Arai60/pull/60/files?diff=unified&w=0#diff-b9d4ab739b4c1ab8035480d9417c186addd4d06b0f1fd82d5ecaa373998694c9R49-R63
- 会議の経過時間を start から end まで increment しながら刻んでいき Set に保存
- 同時に Set から取り出し被りがあった時点で false を返す
- 感想
  - 実用的ではないが、こういう方法もあると参考のかと参考になった

```java
class Solution {
    public boolean canAttendMeetings(int[][] intervals) {
        HashSet<Integer> attendTimes = new HashSet<>();
        for (int[] interval : intervals) {
            for (int i = interval[0]; i < interval[1]; i++) {
                if (attendTimes.contains(i)) {
                    return false;
                }
                attendTimes.add(i);
            }
        }
        return true;
    }
}
```

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

```java
class Solution {
    public boolean canAttendMeetings(int[][] intervals) {
        int[][] sortedIntervals = Arrays.copyOf(intervals, intervals.length);
        Arrays.sort(sortedIntervals, Comparator.comparingInt(a -> a[0]));
        for (int i = 0; i < sortedIntervals.length - 1; i++) {
            int currentEnd = sortedIntervals[i][1];
            int nextStart = sortedIntervals[i + 1][0];
            if (nextStart < currentEnd) {
                return false;
            }
        }
        return true;
    }
}
```
