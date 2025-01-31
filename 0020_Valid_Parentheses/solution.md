## Problem
https://leetcode.com/problems/valid-parentheses/

## Step 1
5分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦OK。思考過程もメモする。

### Approach
* 文字列を一つずつ走査。Stackに開きカッコためていき、閉じカッコが出現したらStackと突合し、適切でなければfalseを返す
    * 文字列が適切な並び順の場合、対応する開カッコがStackの一番上にあるはず。そうでなければfalse
    * もしくは、閉じカッコが来ているのにStackが空の場合はfalseを返す
* 走査完了後、Stackが空であればtrue,そうでなければfalse

```java
class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (c == '(' ||c == '[' ||c == '{') {
                stack.push(c);
            }
            if (c == ')' ||c == ']' ||c == '}') {
                if (stack.isEmpty()) return false;
                if (c == ')' && stack.pop() != '(') return false; 
                if (c == ']' && stack.pop() != '[') return false; 
                if (c == '}' && stack.pop() != '{') return false; 
            }
        }
        return stack.isEmpty();
    }
}
```

## Step 2
他の方が描いたコードを見て、参考にしてコードを書き直してみる。
参考にしたコードのリンクは貼っておく。
読みやすいことを意識する。
他の解法も考えみる。

### 参考にしたPR
* https://github.com/hajimeito1108/arai60/pull/4
* https://github.com/SanakoMeine/leetcode/pull/6
* https://github.com/SanakoMeine/leetcode/pull/7
* https://github.com/olsen-blue/Arai60/pull/6
* https://github.com/canisterism/leetcode/pull/7



### 解法1. カッコを定数化した方法
* 処理の流れはStep1と同様。カッコを定数化したことにより条件分岐の数を減らせている

#### 所感
* メソッドの行数は減ったものの、Step 1とどちらを選択すべきかは悩ましい
* 個人的な感覚としては定数とメソッドとの間の目線の移動量が増えたことによって結果的に認知不可を上げてしまっている気がする
* 何らかの事情でinputデータが変更になる可能性がある場合を考慮すると、こちらの方が管理しやすそうではある

```java
class Solution {
    public static final Set<Character> OPEN_BRACKETS = Set.of(
        '(', '[', '{'
    );
    
    public static final Map<Character, Character> BRACKET_CLOSE_OPEN = Map.of(
        ')', '(', 
        ']', '[', 
        '}', '{'
    );

    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();

        for (char c : s.toCharArray()) {
            if (OPEN_BRACKETS.contains(c)) {
                stack.push(c);
            }
            if (BRACKET_CLOSE_OPEN.containsKey(c)) {
                if (stack.isEmpty()) {
                    return false;
                }
                if (BRACKET_CLOSE_OPEN.get(c) != stack.pop()) {
                    return false;
                }
            }
        }
        return stack.isEmpty();
    }
}
```

### 番兵(Sentinel) を利用する方法


```java
class Solution {
    public static final Set<Character> OPEN_BRACKETS = Set.of(
        '(', '[', '{'
    );
    
    public static final Map<Character, Character> BRACKET_CLOSE_OPEN = Map.of(
        ')', '(', 
        ']', '[', 
        '}', '{'
    );

    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        stack.push('*'); // Add the sentinel

        for (char c : s.toCharArray()) {
            if (OPEN_BRACKETS.contains(c)) {
                stack.push(c);
            }
            if (BRACKET_CLOSE_OPEN.containsKey(c)) {
                if (BRACKET_CLOSE_OPEN.get(c) != stack.pop()) {
                    return false;
                }
            }
        }
        return stack.size() == 1; // Check whether only the sentinel remains
    }
}
```

## Step 3
今度は、時間を測りながら、もう一回書く。
アクセプトされたら消すを3回連続できたら問題はOK。

```java
class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (c == '(' ||c == '[' ||c == '{') {
                stack.push(c);
            }
            if (c == ')' ||c == ']' ||c == '}') {
                if (stack.isEmpty()) return false;
                if (c == ')' && stack.pop() != '(') return false; 
                if (c == ']' && stack.pop() != '[') return false; 
                if (c == '}' && stack.pop() != '{') return false; 
            }
        }
        return stack.isEmpty();
    }
}
```
