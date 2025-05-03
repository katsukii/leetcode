## Problem

https://leetcode.com/problems/top-k-frequent-elements/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach. Map と max-heap を使った解法

時間計算量: O(n log k)
空間計算量: O(n)

- 解法自体はすぐ思いついたが、Map での for 文、Max-heaap の書き方が分からず検索した

- Map に番号と出現回数のペアを保存
- 空の max-heap を用意し、Map に保存されているペアを出現回数を軸として挿入
- Max-heap から k 回 poll()する

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        HashMap<Integer, Integer> numToCount = new HashMap<>();
        for (int num : nums) {
            int count = numToCount.getOrDefault(num, 0) + 1;
            numToCount.put(num, count);
        }
        PriorityQueue<int[]> countMaxHeap = new PriorityQueue<>(
            (a, b) -> b[1] - a[1] // Max-heap
        );

        numToCount.forEach((num, count) -> {
            countMaxHeap.offer(new int[] {num, count});
        });

        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = countMaxHeap.poll()[0];
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

- https://github.com/plushn/SWE-Arai60/pull/9/files#diff-291784abce91292492a5acad677268bfd565410367cfc5e4dc916da5c37556a3R60
  - > heap の代わりに sort を使用してソートしていく。key 関数でソートしたい要素を指定できる。
- https://github.com/nittoco/leetcode/pull/48/files#diff-013dde8937de27960afd2d09c6325ebf53eb00c65d9d2772bc598e798bcdb877R37
  - > Counter を使う方法がある。特に、most_common を使うと楽
  - Python にある most_common()メソッドは、list から 共通要素とその個数をペアにして tuple の list を返してくれる
  - 参考
    - 説明: https://www.geeksforgeeks.org/python-most_common-function/
    - 実装: https://github.com/python/cpython/blob/3d4ac1a2c2b610f35a9e164878d67185e4a3546f/Lib/collections/__init__.py#L625
- https://github.com/fhiyo/leetcode/pull/12/files#diff-1e3421714509a1abfec6bd79b5deb941602373f6b8c4cdc4c5c129a2a697c800R62
  - > quick select による解法
  - quick select を知らなかった。未整列の配列から k 番目に小さい（大きい）要素を取り出すアルゴリズムらしい。Quick Sort アルゴリズムの一種だが、配列全体をソートする訳ではなく、配列を分割して目的の k 番目の要素を見つけることのみに重点を置く

### Approach 1. Max-Heap

- Step1 の改良版。解法は同じ。
- Step1 では heap に[数値, 回数]ペアを表す int[]を丸ごと入れる、一方で改良版は key（Integer）だけを入れ、heap 作成時の設定で Map を参照する事で頻度を取得している
- 配列でペアを作る方法は暗黙的な使い道の解釈を読み手に強制する点で読み手側の負担が大きいと感じる

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> numToCount = new HashMap<>();
        for (int num : nums) {
            int count = numToCount.getOrDefault(num, 0) + 1;
            numToCount.put(num, count);
        }

        PriorityQueue<Integer> countMaxHeap = new PriorityQueue<>(
            (a, b) -> numToCount.get(b) - numToCount.get(a)
        );
        countMaxHeap.addAll(numToCount.keySet());

        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = countMaxHeap.poll();
        }
        return result;
    }
}
```

### Approach 2. Sort を使った解法

時間計算量: O(n log n)
空間計算量: O(n)

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> numToCount = new HashMap<>();
        for (int num : nums) {
            int count = numToCount.getOrDefault(num, 0) + 1;
            numToCount.put(num, count);
        }

        List<Integer> uniqueNums = new ArrayList<>(numToCount.keySet());
        Collections.sort(uniqueNums, (a, b) -> numToCount.get(b) - numToCount.get(a));

        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = uniqueNums.get(i);
        }
        return result;
    }
}
```

### Approach 3. Quick Select を使った解法

時間計算量: 平均 O(n)、最悪（毎回偏った pivot 選択をした場合） O(n^2)
空間計算量: O(n)

- まず各数字の出現回数を HashMap に記録する。その後、Map の key（= ユニークな数字の set）を配列に格納する
- Quick Select アルゴリズムを用いて、出現回数に基づいて配列をパーティショニングする
  - 任意の pivot 要素 を用意し pivot より大きいグループと小さいグループに配列を分割する
- 上位 k 個の要素がパーティションより右側に来るように繰り返す
  - 探索対象となるサブ配列の開始/終了インデックス（left/right）をループのたびに更新し、上位 k 要素が右半分に集まるまで範囲を狭めていく
  - pivotIndex < targetIndex のときは、「右側に上位 k 個がまとまる可能性がある」ので、left = pivotIndex + 1 として探索範囲を右半分に絞る。pivotIndex > targetIndex のときは逆に左半分に絞る

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // Step 1: Count the frequency of each number
        Map<Integer, Integer> numToCount = new HashMap<>();
        for (int num : nums) {
            int count = numToCount.getOrDefault(num, 0) + 1;
            numToCount.put(num, count);
        }

        // Step 2: Create a list of unique numbers
        List<Integer> uniqueNums = new ArrayList<>(numToCount.keySet());

        // Step 3: Use Quick Select to find the k most frequent elements
        int left = 0;
        int right = uniqueNums.size() - 1;
        int targetIndex = uniqueNums.size() - k;

        while (left < right) {
            int pivotIndex = partition(uniqueNums, left, right, numToCount);

            if (pivotIndex == targetIndex) {
                break;
            } else if (pivotIndex < targetIndex) {
                left = pivotIndex + 1;
            } else {
                right = pivotIndex - 1;
            }
        }

        // Step 4: Return the k most frequent elements
        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = uniqueNums.get(uniqueNums.size() - 1 - i);
        }

        return result;
    }

    private int partition(List<Integer> nums, int left, int right, Map<Integer, Integer> numToCount) {
        // Using the rightmost element as pivot
        int pivotFrequency = numToCount.get(nums.get(right));
        int i = left;

        for (int j = left; j < right; j++) {
            if (numToCount.get(nums.get(j)) <= pivotFrequency) {
                // Swap elements
                Collections.swap(nums, i, j);
                i++;
            }
        }

        // Swap the pivot to its final position
        Collections.swap(nums, i, right);

        return i;
    }
}
```

いただいたコメント

- > 個人的には、i の生存期間はある程度長いし重要な変数なので、もうちょっとしっかりした変数名をつけたい気持ちになります。
  - pivotIndex とか storeIndex とかもう少ししっかりした名前にした方がいいかもと思った

## Step 3

今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを 3 回連続できたら問題は OK。

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> numToCount = new HashMap<>();
        for (int num : nums) {
            int count = numToCount.getOrDefault(num, 0) + 1;
            numToCount.put(num, count);
        }

        PriorityQueue<Integer> countMaxHeap = new PriorityQueue<>(
            (a, b) -> numToCount.get(b) - numToCount.get(a)
        );
        countMaxHeap.addAll(numToCount.keySet());

        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = countMaxHeap.poll();
        }
        return result;
    }
}
```
