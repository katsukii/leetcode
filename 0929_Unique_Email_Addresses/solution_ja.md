## Problem

https://leetcode.com/problems/unique-email-addresses/description/

## Step 1

5 分程度答えを見ずに考えて、手が止まるまでやってみる。
何も思いつかなければ、答えを見て解く。ただし、コードを書くときは答えを見ないこと。
動かないコードも記録する。
正解したら一旦 OK。思考過程もメモする。

### Approach

- とりあえず思いついたのは、emails の要素を一つずつみていきながら、無駄な部分をカットし正規化された文字列として Set に格納していく方法
- 正規化ルール:
  - local name 内の最初の+以降は無視
  - local name 内の.は除去

```java
class Solution {
    public int numUniqueEmails(String[] emails) {

        Set<String> normalizedEmails = new HashSet<>();
        for (String email : emails) {
            String[] splitedEmail = email.split("@");
            String localName = splitedEmail[0];
            String domainName = splitedEmail[1];

            int indexBeforePlus = localName.indexOf('+');
            if (indexBeforePlus != -1) {
                localName = localName.substring(0, indexBeforePlus);
            }
            localName = localName.replace(".", "");

            String normalizedEmail = localName + "@" + domainName;
            if (!normalizedEmails.contains(normalizedEmail)) {
                normalizedEmails.add(normalizedEmail);
            }
        }
        return normalizedEmails.size();
    }
}
```

- 調べたところ、Set は要素追加時に以下のチェックは不要

```Java
        if (!normalizedEmails.contains(normalizedEmail)) {
            normalizedEmails.add(normalizedEmail);
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
