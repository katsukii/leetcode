## Problem
https://leetcode.com/problems/generate-parentheses/

## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### Approach
* DPでいけるかと思って考えてみたが、解法を思いつかず。 "20. Valid Parentheses" のように Stackかと思い少し考えてみたがどうも筋が悪そう
* 調べてみて、バックトラッキングという解法を初めて知った
    * 再帰的に解を構築していきながら、すべての可能な解の組み合わせを探索する手法
    * 条件を満たさない解候補は早期に枝刈りして探索を打ち切る
    * 行き詰まったら一つ前の状態に戻って別の可能性を試す
* DPとごっちゃになりそうで少し混乱したが、DPが「部分問題の解を記録（メモ化）して再利用する」のに対し、バックトラッキングは「行き詰まったら戻って別の道を探す」と理解した
* また、DPにはトップダウン（再帰+メモ化）と、ボトムアップ（base caseからスタートし大きな解を解いていく）が、バックトラッキングは基本的にトップダウンに似ており、再帰という点で共通している
    * DP（トップダウン）は再帰+メモ化、バックトラッキングは再帰+枝刈り

```java
public class Solution {
    public static List<String> generateParenthesis(int n) {
        List<String> list = new ArrayList<>();
        StringBuilder s = new StringBuilder();
        func(list, s, n, n);
        return list;
    }

    public static void func(List<String> list, StringBuilder s, int left, int right) {
        if (left == 0 && right == 0) list.add(s.toString());
        
        if (left > 0) {
            s.append("(");
            func(list, s, left - 1, right);
            s.setLength(s.length()-1);
        }

        if (right > left) {
            s.append(")");
            func(list, s, left, right - 1);
            s.setLength(s.length()-1);
        }
    }
}

```

## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### 解法 1. バックトラッキング（再帰＋文字列連結）
#### コンセプト
* 現在の文字列に対してまだ使用可能な左括弧 "(" と右括弧 ")" の個数を管理しながら再帰的に生成する
* 括弧の組み合わせの生成については、同一のStringBuilder変数に対して追加、除去を繰り返し管理する

#### 処理の流れ
* 括弧の組み合わせを生成する再帰関数を定義し実行
    * 引数は、`完成した括弧のリスト`、`作成中括弧の文字列`、`残りの左括弧`、`残りの右括弧`
* 再帰関数の流れ
    * 残りの左括弧がまだ 0 個に達していなければ追加する → `残りの左括弧を1つ減らして次の再帰呼び出し` → 作成中括弧の文字列の末尾を削る
    * 残りの右括弧は、残りの左括弧の数より多い場合に追加 → `残りの右括弧を1つ減らして次の再帰呼び出し` → 作成中括弧の文字列の末尾を削る
    * 両括弧の残りがともに0になった時に正しい組み合わせとして完成リストに追加し、現在の関数処理を終了→呼び出し元に戻る
* 上記処理を繰り返すことで枝刈りにより無駄な処理を防ぎながら括弧の組み合わせを全探索する
* バックトラッキングにおける「枝狩り」がこの解法のどこに該当するか？
    * `remainingRight > remainingLeft` これにより右括弧が左括弧より多くなることを防いでいる
    * 不正な部分解になる可能性のある枝（つまり、右括弧が先行してしまう枝）は探索の前に排除（枝刈り）される

```java
public class Solution {
    public static List<String> generateParenthesis(int n) {
        List<String> combinations = new ArrayList<>();
        StringBuilder currentCombination = new StringBuilder();
        generateCombination(combinations, currentCombination, n, n);
        return combinations;
    }

    public static void generateCombination(List<String> combinations, StringBuilder currentCombination, int remainingLeft, int remainingRight) {
        if (remainingLeft == 0 && remainingRight == 0) {
            combinations.add(currentCombination.toString());
            return;
        }
        
        if (remainingLeft > 0) {
            currentCombination.append("(");
            generateCombination(combinations, currentCombination, remainingLeft - 1, remainingRight);
            currentCombination.setLength(currentCombination.length() - 1);
        }

        if (remainingRight > remainingLeft) {
            currentCombination.append(")");
            generateCombination(combinations, currentCombination, remainingLeft, remainingRight - 1);
            currentCombination.setLength(currentCombination.length() - 1);
        }
    }
}
```

### 解法 2. 動的計画法（ボトムアップ）
調べてみたら動的計画法での解法もあったので記載

時間計算量: O(4^n)
空間計算量: O(4^n)

* 最初、四重ループだから素直にO(n^4)かなと考えたが、違うらしい
* n組の正しい括弧列の総数はC_n（n番目のカタラン数）であり、カタラン数は大まかにc_N〜4^nで増加する
    * combinations[k]のサイズはk番目のカタラン数になる。つまり内側の2つのループは固定のn回ではなくカタラン数に依存した回数実行される
* カタラン数とは
    * https://stchopin.hatenablog.com/entry/2023/06/18/201048

#### コンセプト
* combinations[i] をi組の括弧を使った正しい組み合わせのリストとする
* 正しい括弧列は、、「ある括弧のペアで囲まれた部分」と「そのペアの後に続く部分」の組み合わせで構成される。すなわち、任意のi組の括弧列は次のような形式で構成できる: `"(" + A + ")" + B`
    * A（内側括弧列）は、j 組分（`combinations[j]` の各組み合わせ）
        * j は 0 から i - 1 まで変化し、Aの括弧の組合せ数を決定する
    * B（外側括弧列）は、残りの `i - j - 1` 組分（`combinations[i - j - 1]` の各組み合わせ）
        * i（全体の括弧数）とj（内側括弧数）が決まることで必然的にBも決定する

#### 処理の流れ
* 結果格納用のArrayListの二重配列を用意
* ループ1: 1組からn組までforループ（イテレータ i）で「i組の括弧を使った正しい括弧の組み合わせ」の配列を生成する
    * i組の文字列格納用の、空の文字列用ArrayListを作成
    * ループ2: 0 から i - 1までの 内側括弧列A に使う保存済のcombinations[j]を呼び出す
        * ループ3: combinations[j]から各括弧文字列を取り出す
            * ループ4: 外側括弧列Bに使う保存済のcombinations[i - j - 1]配列を取り出す
                * 処理: `"(" + A + ")" + B` となる文字列を生成し、文字列用ArrayListに格納
    * ループ2が完了したら文字列用ArrayListを結果格納用ArrayListにadd
* 結果格納用ArrayListのn番目の要素をreturn

* DPの流れ
    * combinations[0] = `[""]` // Base Case
    * combinations[1]: 
        * j = 0, i - j - 1 = 0
        * よって `["()"]`
    * combinations[2]:
        * j = 0, i - j - 1 = 1
        * j = 1, i - j - 1 = 0
        * よって `["()()", "(())"]`
    * combinations[3]:
        * j = 0, i - j - 1 = 2
            * A: `[""]`, B: `["()()", "(())"]`
        * j = 1, i - j - 1 = 1
            * A: `["()"]`, B: `["()"]`
        * j = 2, i - j - 1 = 0
            * A: `["()()", "(())"]`, B: `[""]`
        * よって `["()()()","()(())","(())()","(()())","((()))"]`

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<List<String>> combinations = new ArrayList<>();
        combinations.add(Arrays.asList(""));  // combinations[0] = [""]
        
        for (int i = 1; i <= n; i++) {
            List<String> currentCombination = new ArrayList<>();
            for (int j = 0; j < i; j++) {
                for (String leftPart : combinations.get(j)) {
                    for (String rightPart : combinations.get(i - j - 1)) {
                        currentCombination.add("(" + leftPart + ")" + rightPart);
                    }
                }
            }
            combinations.add(currentCombination);
        }
        return combinations.get(n);
    }
}
```

* 感想: 四重ループは可読性と処理の重さの観点から引数が大きい場合はあまり現実的な解ではなさそう

## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。

```java
public class Solution {
    public static List<String> generateParenthesis(int n) {
        List<String> combinations = new ArrayList<>();
        StringBuilder currentCombination = new StringBuilder();
        generateCombination(combinations, currentCombination, n, n);
        return combinations;
    }

    public static void generateCombination(List<String> combinations, StringBuilder currentCombination, int remainingLeft, int remainingRight) {
        if (remainingLeft == 0 && remainingRight == 0) {
            combinations.add(currentCombination.toString());
            return;
        }
        
        if (remainingLeft > 0) {
            currentCombination.append("(");
            generateCombination(combinations, currentCombination, remainingLeft - 1, remainingRight);
            currentCombination.setLength(currentCombination.length() - 1);
        }

        if (remainingRight > remainingLeft) {
            currentCombination.append(")");
            generateCombination(combinations, currentCombination, remainingLeft, remainingRight - 1);
            currentCombination.setLength(currentCombination.length() - 1);
        }
    }
}
```