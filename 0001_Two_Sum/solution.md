## 問題
https://leetcode.com/problems/two-sum/description/

## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### 思考メモ
*  自力ではO(n^2)の全探索の方法しか思いつかなかった

### 全探索O(n^2)で解く方法
何度かreturn忘れによりエラー

#### 完成版
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) 
            for (int j = i + 1; j < nums.length; j++) 
                if (nums[i] + nums[j] == target)   
                    return new int[] {i, j};
        
        return new int[] {}; 
    }
}
```

### Hashmapで解く方法
* お手本をChatGPTで生成し、そのとおりに回答を作成
* 時間計算量: O(n)
    * HashMapの存在確認はO(1)
* HashMapのkeyとvalに配列の各valとindexをそれぞれ保存し、配列要素で補数のインデックスを検索できるようにしている

#### つまづいたポイント
* 一度HashMapに保存するのではなく、一度のループで補数の検索と追加を同時に実行している点がメリットだが、同時に反直感的で理解に時間がかかったポイント
* また最後のreturn忘れがち
* `if (map.containsKey(complement))` の条件を`nums[i]`と間違いがち

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement))
                return new int[] {map.get(complement), i};
            map.put(nums[i], i);
        }
        
        return new int[] {}; 
    }
}
```

## Step 2
* Googleのコーディング規約に従いbraceを省略しない形に変更（[参考](https://google.github.io/styleguide/javaguide.html)）
* mapということはわかるので、変数名を変更 map → numToIndex（[参考](https://github.com/aoshi2025s/leetcode-review/pull/1/)）
* 例外時、空配列をreturnするのではなくExceptionをthrowする方法があったので参考にした（[参考](https://github.com/seal-azarashi/leetcode/pull/11/files#diff-529ec7ca2437e31acab1481ea9ca5df7680f35a10544ce877ce28279305fa739R60)）

### 参考コード
* https://github.com/aoshi2025s/leetcode-review/pull/1/
* https://github.com/seal-azarashi/leetcode/pull/11/


## Step 3
以下完成版

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> numToIndex = new HashMap<>();
        
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (numToIndex.containsKey(complement)) {
                return new int[] {numToIndex.get(complement), i};
            }
            numToIndex.put(nums[i], i);
        }
        
        throw new IllegalArgumentException("No two sum pair found.");
    }
}
```