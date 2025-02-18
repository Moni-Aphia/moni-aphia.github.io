!!! note "Note"
    具体介绍[课程记录](../暑假课程记录.md#ucb-data100)

## Pandas 

在 Pandas 的语言中，我们称一个表格（table） 为 DataFrame 。我们认为 DataFrames 是由一系列已经命名的列组成，这些列也被称为 Series .

一个 Series 类似于一维数组形状的对象，包含

- 具有相同类型的值的序列
- 一系列数据标签，称为索引（Index）

我们可以向 Series 中的对象提供索引

```python
s = pd.Series([-1,10,2],index = ["a","b","c"])
```

Series 中的索引也可以被更改

```python
s.index= ["firts","second","third"]
```

通过如下方法选取 Seires 中的一个或一组值：

- 单一标签 
```python
s["a"]
```
- 标签列表
```python
s[ ["a","c"] ]
```
- 过滤条件（filtering condition）
```python
s[s>0]
```
如果需要挑选 Series 满足特定条件的值，我们需要做以下两个步骤：
1. 在 Series 中引用布尔条件，这个会创建一个布尔类型的 Series ，如 `s>0`
2. 将这个布尔条件运用到 Series 中，pandas 会挑选出满足布尔条件的 Series 如`s[s>0]`

因为我们处理的 Series 一般源自 DataFrame ，在处理 Series 时，我们通常将其作为 DataFrame 中的列。基于这个思想，我们可以将 DataFrame 视作一系列 Series 的组合，他们有着相同的索引。

生成 DataFrame 的方法为
```python 
pandas.DataFrame(data,index,columns)
```
下面列出了几种生成 DataFrame 比较热门的方式：

- CSV 文件；
```python 
elections = pd.read_csv("data/elections.csv")
elections = pd.read_csv("data/elections.csv".index_col = "Year")
```
第二种方式可以指定 DataFrame 对应的索引 （Index）

- 利用列表和列名
```
pd.DataFrame([[1,"one"],[2,"two"]],columns = ["Number","Description])
```
- 利用字典
```python 
pd.DataFrame({"Fruit":["Strawberry","Orange"],
			"Price":[5.49,3.99]})
pf.DataFrame({"Fruit":"Strawberry","Price":5.49}
			{"Fruit":"Orange","Price":3.99})
```
- 利用Series 
```python 
s_a = pd.Series(["a1", "a2", "a3"], index = ["r1", "r2", "r3"])
s_b = pd.Series(["b1", "b2", "b3"], index = ["r1", "r2", "r3"])

pd.DataFrame({"A-column":s_a, "B-column":s_b})
```
