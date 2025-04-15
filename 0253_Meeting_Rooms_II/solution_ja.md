## Problem

https://leetcode.com/problems/meeting-rooms-ii/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach

- 頭の中でなんとなく直感が働き starts と ends に分けて 2 つに分けてソートするところまでは自力でいったが、for 文の中身が思いつかず
- 答えを見た後に時系列で図解したら理解できた
- ロジックの詳細は Step2 の Approach 1 に記載

```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if (intervals == null || intervals.length == 0) {
            return 0;
        }

        int[] starts = new int[intervals.length];
        int[] ends = new int[intervals.length];

        for (int i = 0; i < intervals.length; i++) {
            starts[i] = intervals[i][0];
            ends[i] = intervals[i][1];
        }
        Arrays.sort(starts);
        Arrays.sort(ends);

        int count = 0;
        int endIndex = 0;
        for (int i = 0; i < intervals.length; i++) {
            if (starts[i] < ends[endIndex]) {
                count++;
                continue;
            }
            endIndex++;
        }

        return count;
    }
}
```

## Step 2

他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### Approach 1. 開始・終了時刻それぞれのソート済配列を使用

時間計算量: O(n log n) ※ 配列のソート
空間計算量: O(n)

- すべての会議の開始時刻を配列 starts に格納し、昇順でソート。終了時刻も同様に配列 ends に格納し、昇順でソート
- 開始時刻配列をループで走査。新しい会議が始まる前に終わる会議があれば、その会議室を再利用。そうでなければ、新しい会議室が必要
- 感想
  - Step1 ですんなりいけるかと思ったが意外と頭がこんがらがっててこずった
  - 開始時刻配列と終了時刻配列それぞれをソートした時点で、それぞれのインデックスが必ずしも同一の会議を指していない事を直感で理解できていなかったことが原因
  - 時系列に図解してそれぞれの時刻にインデックスをナンバリングしたらようやく理解できた

```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if (intervals == null || intervals.length == 0) {
            return 0;
        }

        // Separate intervals array into starts and ends
        int[] starts = new int[intervals.length];
        int[] ends = new int[intervals.length];

        for (int i = 0; i < intervals.length; i++) {
            starts[i] = intervals[i][0];
            ends[i] = intervals[i][1];
        }
        Arrays.sort(starts);
        Arrays.sort(ends);

        int roomCount = 0;
        int endIndex = 0;
        for (int i = 0; i < intervals.length; i++) {
            if (starts[i] < ends[endIndex]) {
                roomCount++;
                continue;
            }
            endIndex++;
        }

        return roomCount;
    }
}
```

### Approach 2. 最小ヒープ（PriorityQueue）を用いる方法

時間計算量: O(n log n)
空間計算量: O(n)

- 会議を開始時刻でソート
- 最小ヒープを使って**現在進行中の**会議の終了時刻を追跡
- 新しい会議が始まる時、ヒープから終了済会議を取り除く
- ヒープのサイズが必要な会議室の数になる

```java
public class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if (intervals == null || intervals.length == 0) {
            return 0;
        }

        // Sort by the start time of each meeting
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

        // Trace the end-time as minimum rooms
        PriorityQueue<Integer> minEndTimeHeap = new PriorityQueue<>();
        minEndTimeHeap.add(intervals[0][1]); // Add first mtg end-time

        // Second and later meetings
        for (int i = 1; i < intervals.length; i++) {
            // If current mtg starts after earliest ending meeting
            if (intervals[i][0] >= minEndTimeHeap.peek()) {
                minEndTimeHeap.poll(); // Release the meeting room to reuse
            }
            // Assign the current end-time as a newly necessary meeting room
            minEndTimeHeap.add(intervals[i][1]);
        }

        return minEndTimeHeap.size();
    }
}
```

### Approach 3. スイープラインを利用する方法

時間計算量: O(n log n)
空間計算量: O(n)

- イベント変換: 各会議の開始時刻と終了時刻をそれぞれイベントとして配列に変換する
  - 開始イベント: +1 カウント（会議室使用開始）
  - 終了イベント: -1 カウント（会議室リリース）
- イベントソート: 両イベント配列を 2D 配列として一つの配列にマージして時刻順（昇順）にソートする。同時刻にイベントが複数存在する場合、終了イベント(-1)を優先する
- スイープ処理: ソート済のイベントをループで順番に処理し、イベントごとにカウントを更新する。カウントの最大数が必要な会議室の最小数となる

```java
public class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if (intervals == null || intervals.length == 0) {
            return 0;
        }

        // Store into an array for each start and end event
        int[][] events = new int[intervals.length * 2][2];
        int index = 0;
        for (int[] interval : intervals) {
            // Events [Start: +1], [End: -1]
            events[index++] = new int[]{interval[0], 1};  // Start
            events[index++] = new int[]{interval[1], -1}; // End
        }

        // Sort by time (if the times are the same, the end time takes priority)
        Arrays.sort(events, (a, b) -> {
            if (a[0] != b[0]) {
                return Integer.compare(a[0], b[0]); // by time
            }
            return Integer.compare(a[1], b[1]);
        });

        int roomsInUse = 0;
        int maxRooms = 0;
        // Process events sequentially
        for (int[] event : events) {
            roomsInUse += event[1]; // Start:+1, End:-1
            maxRooms = Math.max(maxRooms, roomsInUse);
        }

        return maxRooms;
    }
}
```

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

```java

```
