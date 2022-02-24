# スッキリわかる SQL 入門 第 2 版

## 第 Ⅱ 部　 SQL を使いこなそう

### 第 7 章　副問い合わせ

#### SELECT をネストする

- SQL 文の中に別の SELECT 文を記述でき、それを副問い合わせ/副照会/サブクエリと呼ぶ
- SQL 文ではカッコ書きの副問い合わせから先に処理される

##### select 費目, 出金額 from 家計簿 where 出金額 = (select max(出金額) from 家計簿)

- 処理 ①：(select max(出金額) from 家計簿)
- 処理 ②：select 費目, 出金額 from 家計簿 where 出金額 = 処理 ①

#### 単一行副問い合わせ

- 検索結果が 1 行 1 列の値
- 1 つの値との判定を行う WHERE 句の条件式などに記述できる

##### update 家計簿集計 set 平均 = (select avg(出金額) from 家計簿アーカイブ where 出金額 > 0 and 費目 = '食費') where 費目 = '食費'

##### select 日付, メモ, 出金額, (select 合計 from 家計簿集計 where 費目 = '食費') as 過去の合計額 from 家計簿アーカイブ where 費目 = '食費'

#### 複数行副問い合わせ

- 検索結果が複数行 1 列の値
- 複数の値を列挙する場所などに記述できる

##### select \* from 家計簿集計 where 費目 in('食費', '水道光熱費', '教育娯楽費', '給料')

##### select \* from 家計簿集計 where 費目 in(select distinct 費目 from 家計簿)

##### select \* from 家計簿 where 費目 = '食費' and 出金額 < any (select 出金額 from 家計簿アーカイブ where 費目 = '食費')

##### select \* from 家計簿アーカイブ where 費目 in (select 費目 from 家計簿 where 費目 is not null)

##### select \* from 家計簿アーカイブ where 費目 in (select coalesce(費目, '不明') from 家計簿)

#### 表の代わりとなる副問い合わせ

- 検索結果が複数行と複数列になる値
- 表を記述できる箇所に記述できる

##### select sum(SUB.出金額) as 出金額合計 from (select 日付, 費目, 出金額 from 家計簿 union<br> select 日付, 費目 出金額 from 家計簿アーカイブ where 日付 >= '2018-01-01' and '2018-01-31') as SUB

##### insert into 家計簿集計(費目, 合計, 平均, 回数) select 費目, sum(出金額), avg(出金額), 0 from 家計簿 where 出金額 > 0 group by 費目
