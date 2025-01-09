## Problem
https://leetcode.com/problems/zigzag-conversion/

## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### Approach
* 時間計算量O(n^2)未満で問題を解くにはどうすればいいかと考えながら解いてみた
1. 引数で与えられた行数（numRows）と同一の要素数を持つ、StringBuilder型のArrayListを用意する
2. 文字列を走査しながら各文字に行番号(ArrayListのindex番号)を振る
    * `s = "PAYPALISHIRING"`, `numRows = 3` であれば `01210121012101`
3. 各文字を、行番号ごとにStringBuilderのArrayListに格納していく
    * `0 -> "PAHN"`, `1 -> "APLSIIG"`, `2 -> "YIR"`
4. 最後に各要素のStringBuilderをつなぎあわせればOK
* この方法であれば時間計算量O(n)で解けそう

```java
class Solution {
    public String convert(String s, int numRows) {
        int length = s.length();
        if (length <= numRows || numRows <= 1) return s;
        
        ArrayList<StringBuilder> sbList = new ArrayList<>();
        for (int i = 0; i < numRows; i++) {
            StringBuilder sb = new StringBuilder();
            sbList.add(sb);
        }

        boolean goingDown = true;
        int row = 0;        
        for (int i = 0; i < length; i++) {
            char c = s.charAt(i);
            StringBuilder sb = sbList.get(row);
            sb.append(c);
            row = goingDown ? row + 1 : row - 1;
            if (row == 0 || row == numRows - 1) goingDown = !goingDown;
        }

        StringBuilder result = new StringBuilder();
        for (int i = 0; i < numRows; i++) {
            result.append(sbList.get(i));
        }

        return result.toString();
    }
}
```

## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。


### Solution 1. 進行方向と行番号を動的に管理する方法（Step1の改善）
* ArrayListの名称をシンプルにrowsに修正
    * https://github.com/Mike0121/LeetCode/pull/26/files#r1632031603
* 最後のfor文はforEachの方が可読性が高いため修正
* ArrayListを使わずとも固定長の配列を使えば空間計算量が少なくて済む
* 結果のStringBuilder生成時にサイズを指定することでresizeのオーバーヘッドを防ぐことができる

```java
class Solution {
    public String convert(String s, int numRows) {
        int length = s.length();
        if (length <= numRows || numRows <= 1) return s;
        
        StringBuilder[] rows = new StringBuilder[numRows];
        for (int i = 0; i < numRows; i++) {
            rows[i] = new StringBuilder();
        }

        boolean goingDown = true;
        int row = 0;
        for (char c : s.toCharArray()) {
            rows[row].append(c);
            row = goingDown ? row + 1 : row - 1;
            if (row == 0 || row == numRows - 1) goingDown = !goingDown;
        }

        StringBuilder result = new StringBuilder(length);
        for (StringBuilder sb : rows) result.append(sb);

        return result.toString();
    }
}
```

上記と同じ考え方で、事前にすべての空のStringBuilderをインスタンス化せずに動的に用意していく方法もある。for文の数が一つ減るため、個人的には以下の方が認知不可が低い気がする

```java
class Solution {
    public String convert(String s, int numRows) {
        int length = s.length();
        if (length <= numRows || numRows <= 1) return s;
        
        StringBuilder[] rows = new StringBuilder[numRows];
        boolean goingDown = true;
        int row = 0;
        for (char c : s.toCharArray()) {
            if (rows[row] == null) rows[row] = new StringBuilder();
            rows[row].append(c);
            row = goingDown ? row + 1 : row - 1;
            if (row == 0 || row == numRows - 1) goingDown = !goingDown;
        }

        StringBuilder result = new StringBuilder(length);
        for (StringBuilder sb : rows) result.append(sb);

        return result.toString();
    }
}
```

### Solution 2. 各文字の行番号を事前に計算する方法
* numRowsがわかっていれば、各文字の行番号が事前に分かるという考え方
* cycleLengthという1サイクルの長さを表す変数を用意しnumRowsから計算する
    * `int cycleLength = 2 * numRows - 2;`
    * ex) numRows = 3 であれば1サイクル4
* ジグザグの往路における行番号は文字位置をサイクル長で割った余り(i % cycleLength), 復路はさらにそれをサイクル長から引いたものとなる

```java
class Solution {
    public String convert(String s, int numRows) {
        int length = s.length();
        if (length <= numRows || numRows <= 1) return s;
        
        StringBuilder[] rows = new StringBuilder[numRows];
        int cycleLength = numRows * 2 - 2;
        
        for (int i = 0; i < length; i++) {
            int row = i % cycleLength;
            if (row >= numRows) row = cycleLength - row;
            if (rows[row] == null) rows[row] = new StringBuilder();
            rows[row].append(s.charAt(i));
        }

        StringBuilder result = new StringBuilder(length);
        for (StringBuilder sb : rows) result.append(sb);

        return result.toString();
    }
}
```

### 各ソリューションの評価
* 1は人間にとって直感的だが条件分岐が2より多い
* 2の場合、計算のみで行番号を決定できるため方向の制御が不要になる。ただし計算が間接的なため人間が理解するには1よりも理解が少し難しくなる
* 結論、パフォーマンスを重視する場合は2で問題なさそうだが、処理の規模が小さい場合は1で良さそうと感じた

### 参考コード
* https://github.com/Yoshiki-Iwasa/Arai60/pull/65
* https://github.com/fhiyo/leetcode/pull/58
* https://github.com/rihib/leetcode/pull/9
* https://github.com/Mike0121/LeetCode/pull/26

## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。

個人的に理解に時間を要したSolution 2 を採用した

```java
class Solution {
    public String convert(String s, int numRows) {
        int length = s.length();
        if (length <= numRows || numRows <= 1) return s;
        
        StringBuilder[] rows = new StringBuilder[numRows];
        int cycleLength = numRows * 2 - 2;
        
        for (int i = 0; i < length; i++) {
            int row = i % cycleLength;
            if (row >= numRows) row = cycleLength - row;
            if (rows[row] == null) rows[row] = new StringBuilder();
            rows[row].append(s.charAt(i));
        }

        StringBuilder result = new StringBuilder(length);
        for (StringBuilder sb : rows) result.append(sb);

        return result.toString();
    }
}
```