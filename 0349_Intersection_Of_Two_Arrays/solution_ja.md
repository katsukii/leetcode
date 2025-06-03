## Problem

https://leetcode.com/problems/intersection-of-two-arrays/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach

- 所与の 2 つの配列を Set に変換。片方の Set を走査しながらもう片方に当該要素が含まれるかどうかをチェックし、含まれた場合のみ結果格納用の Set に保管する
- 最後に結果格納用の Set を int[]に変換する

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> uniqueNums1 = new HashSet<>();
        for (int num : nums1) {
            if (!uniqueNums1.contains(num)) {
                uniqueNums1.add(num);
            }
        }
        Set<Integer> uniqueNums2 = new HashSet<>();
        for (int num : nums2) {
            if (!uniqueNums2.contains(num)) {
                uniqueNums2.add(num);
            }
        }

        Set<Integer> intersections =  new HashSet<>();
        for (int num : uniqueNums1) {
            if (uniqueNums2.contains(num)) {
                intersections.add(num);
            }
        }

        int[] result = new int[intersections.size()];
        int i = 0;
        for (Integer intersection : intersections) {
            result[i] = intersection;
            i++;
        }
        return result;
    }
}
```

上記でも動くが、少し冗長なため以下のように修正。具体的には、nums2 の Set 化はスキップし、走査して直接結果 Set に格納

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> uniqueNums1 = new HashSet<>();
        for (int num : nums1) {
            uniqueNums1.add(num);
        }

        Set<Integer> intersections =  new HashSet<>();
        for (int num : nums2) {
            if (uniqueNums1.contains(num)) {
                intersections.add(num);
            }
        }

        int[] result = new int[intersections.size()];
        int i = 0;
        for (Integer intersection : intersections) {
            result[i] = intersection;
            i++;
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
