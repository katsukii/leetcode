## Problem
https://leetcode.com/problems/paint-fence/

## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### Approach
* 手書きでフェンスを書きながら考えてみたが、思いつかなかったので解法を見た

```java
class Solution {
    public int numWays(int n, int k) {
        if (n == 0) return 0;
        if (n == 1) return k;

        // Ways for n = 2
        int sameAsPrevious = k;
        int diffFromPrevious = k * (k - 1);
        int total = sameAsPrevious + diffFromPrevious;

        // Ways for n >= 3
        for (int i = 3; i <= n; i++) {
            sameAsPrevious = diffFromPrevious;
            diffFromPrevious = total * (k - 1);
            total = sameAsPrevious + diffFromPrevious;
        }
        return total;
   }
}
```

## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### 参考しにしたPR
* https://github.com/Mike0121/LeetCode/pull/48
* https://github.com/kazukiii/leetcode/pull/31
* https://github.com/Ryotaro25/leetcode_first60/pull/33
* https://github.com/TORUS0818/leetcode/pull/32
* https://github.com/Yoshiki-Iwasa/Arai60/pull/44

* 行列

### 解法1. 動的計画法
時間計算量: O(n)
空間計算量: O(1)

* フェンスを一本ずつ走査する中で、一つ前までのフェンスの塗り方の組み合わせの総数を保存しておき利用する動的計画法

```java
class Solution {
    public int numWays(int n, int k) {
        if (n == 0) return 0;
        if (n == 1) return k;

        // Ways for n = 2
        int sameAsPrevious = k;
        int diffFromPrevious = k * (k - 1);
        int total = sameAsPrevious + diffFromPrevious;

        // Ways for n >= 3
        for (int i = 3; i <= n; i++) {
            sameAsPrevious = diffFromPrevious;
            diffFromPrevious = total * (k - 1);
            total = sameAsPrevious + diffFromPrevious;
        }
        return total;
   }
}
```

### 解法2. 動的計画法（配列を使用）
時間計算量: O(n)
空間計算量: O(n)

* 配列を使用するため解法1より空間計算量が多い
* 個人的にはDPとしてはこちらの方が馴染みやすさがあった

```java
class Solution {
    public int numWays(int n, int k) {
        if (n == 0) return 0;
        if (n == 1) return k;
        
        int[] ways = new int[n + 1];
        ways[1] = k;  // Can choose from k colors
        ways[2] = k * k;
        
        // Ways for n >= 3
        //   1. Same as prev: ways[i - 2] * (k - 1)
        //   2. Different from prev: ways[i - 1] * (k - 1)
        //   Thus, Total = (ways[i - 1] + ways[i - 2]) * (k - 1)
        for (int i = 3; i <= n; i++) {
            ways[i] = (ways[i - 2] + ways[i - 1]) * (k - 1);
        }
        
        return ways[n];
    }
}
```

### 解法3. 行列累乗
時間計算量: O(log n)
空間計算量: O(1) (2×2の固定の行列サイズ)

* 動的計画法(DP)で用いた再帰関係を、行列で表現して解くというコンセプト
* DPの計算量O(n)に対し、行列累乗であればO(log n)で解けるため指数的に高速化できる


```java
class Solution {
    public int numWays(int n, int k) {
        if (n == 0) return 0;
        if (n == 1) return k;
        if (n == 2) return k * k;

        long[][] base = {
            {k - 1, k - 1},
            {1, 0}
        };

        long[][] result = matrixPower(base, n - 2);
        
        long dp1 = k * k;  // dp(2)
        long dp2 = k;      // dp(1)

        return (int)(result[0][0] * dp1 + result[0][1] * dp2);
    }

    private long[][] matrixPower(long[][] base, int exp) {
        long[][] result = {{1, 0}, {0, 1}};

        while (exp > 0) {
            if (exp % 2 == 1) {
                result = multiplyMatrix(result, base);
            }
            base = multiplyMatrix(base, base);
            exp /= 2;
        }

        return result;
    }

    private long[][] multiplyMatrix(long[][] a, long[][] b) {
        long[][] c = new long[2][2];
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                c[i][j] = a[i][0] * b[0][j] + a[i][1] * b[1][j];
            }
        }
        return c;
    }
}
```


## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。

```java
class Solution {
    public int numWays(int n, int k) {
        if (n == 0) return 0;
        if (n == 1) return k;

        // Ways for n = 2
        int sameAsPrevious = k;
        int diffFromPrevious = k * (k - 1);
        int total = sameAsPrevious + diffFromPrevious;

        // Ways for n >= 3
        for (int i = 3; i <= n; i++) {
            sameAsPrevious = diffFromPrevious;
            diffFromPrevious = total * (k - 1);
            total = sameAsPrevious + diffFromPrevious;
        }
        return total;
   }
}
```
