## Problem

https://leetcode.com/problems/subarray-sum-equals-k/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach

- すぐに思いついたのは尺取り法的な Two-Pointer Approach
- 開始点と終了点の 2 つのポインタを用意し、それらの間にある要素の合計値が k と合致したらカウントアップ → ポインタをずらすというやり方
- ※ 以下エラーになったコード

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int result = 0;
        int left = 0;
        int right = 0;
        while (left < nums.length) {
            int sum = 0;
            for (int i = left; i <= right; i++) {
                sum += nums[i];
            }
            if (sum == k) {
                result++;
            }
            right++;
            if (right == nums.length) {
                left++;
                right = left;
            }
        }
        return result;
    }
}
```

- この方法だと要素数が 10,000 のテストケースで Time Limit Exceeded エラーとなった

- もっとうまくやれる方法がありそうと思い、もしかして DP なのではないか？と考える
- 部分配列の和の配列として、i 番目から j 番目までの部分配列の和を dp[i][j] = dp[i][j-1] + nums[j]という形で保存すれば計算過程でメモを活用できる
- しかし、書き進めるうちにメモリ効率が悪すぎることに気づく
- 動的計画法では、同じ部分問題の解をメモ化して何度も参照することで効率化を図る。この問題でも確かに dp[i][j] = dp[i][j-1] + nums[j]として前の結果を参照は可能
- しかし根本的な誤りとして、各部分配列[i, j] の和は独立した問題であることに気づく。つまり、部分問題の「重複」が存在しない
- 結果として O(n^2)個の異なる部分配列すべてを計算する必要があるため効率化できていない

## Step 2

他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### Approach 1. 累積和 + HashMap

- 時間計算量: O(n)
- 空間計算量: O(n)

- 参考: https://note.com/toppy_taiwan/n/n5ac3c6e3bd87
- 累積和（Prefix sum）とは、配列の先頭から各位置までの合計を記録したもの
  - [3, 1, 4, 2] の累積和は[3, 4, 8, 10]
- 部分配列の和を求める方法: 当該部分配列の「① index 0 から終点までの累積和」 - 「② 始点の一つ前までの累積和」
- つまり、① - ② = k が成立する部分配列の数をカウントする必要がある
- すなわち、所与の配列の各 index を部分配列の終点としたときに当該終点にて ① - k = ② が成立する、これまで出現済の ② の数をカウントアップすればよい
- 各累積和の出現回数を Map に保管しておけば、走査の際に取り出してチェックが可能
  - 出現したことのある累積和を key とし、その出現回数を value とする Map を用意（初期値は {0 : 1} 。空の部分配列の和は 0）。
  - この初期値がないと、始点が index=0 の部分配列をうまくカウントできない

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        Map<Integer, Integer> prefixSumToCount = new HashMap<>();
        prefixSumToCount.put(0, 1);

        int sum = 0;
        int count = 0;
        for (int num : nums) {
            sum += num;
            if (prefixSumToCount.containsKey(sum - k)) {
                count += prefixSumToCount.get(sum - k);
            }
            int tmpCount = prefixSumToCount.getOrDefault(sum , 0);
            prefixSumToCount.put(sum, tmpCount + 1);
        }

        return count;
    }
}
```

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        prefix_sum_to_count = {0: 1}
        count = 0
        sum = 0
        for num in nums:
            sum += num
            if sum - k in prefix_sum_to_count:
                count += prefix_sum_to_count[sum - k]

            prefix_sum_to_count[sum] = prefix_sum_to_count.get(sum, 0) + 1

        return count
```

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

```java

```
