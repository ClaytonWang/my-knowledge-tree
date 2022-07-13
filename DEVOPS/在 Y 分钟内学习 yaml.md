# 在 Y 分钟内学习 yaml
YAML 是一种数据序列化语言，旨在供人类直接读写。

它是 JSON 的严格超集，增加了语法上重要的换行符和缩进，如 Python。然而，与 Python 不同的是，YAML 不允许用于缩进的文字制表符。

\---   # 文件开始
\# YAML 中的注释看起来像这样。
\################
标量类型
\################
\# 我们的根对象（贯穿整个文档）将是一个映射，
\# 相当于字典、散列或其他语言的对象。
key :  value 
another_key : 另一个值在这里。
a_number_value :  100 
science_notation :  1e+12 
\# 数字 1 将被解释为数字，而不是布尔值。如果你想
\# 它被解释为布尔值，使用 true 
boolean :  true 
null_value :  null 
key with spaces :  value 
\# 注意字符串不需要被引用。但是，它们可以。
但是： '一个  字符串，用引号括起来。' “钥匙也可以引用。” ： “如果你想在你的钥匙里放一个'：'很有用。” 单引号：'有'一个''转义模式'双引号：“有很多：\\”、\\0、\\t、\\u263A 、\\x0d\\x0a \\== \\r\\n 等等。” # UTF- 8/16/32 个字符需要编码上标二：      

 \\u00B2
\# 多行字符串可以写成 “文字块”（使用 |）、
\# 或 “折叠块”（使用 “>”）。
文字块： | 
    这整个文本块将是 “literal_block”键的值，
    并保留换行符。
    文字一直持续到 de-dented，并且前导缩进被
    剥离。
        任何 “更多缩进” 的
        行都会保留其余的缩进 - 这些行将缩进 4 个空格。
folded_style :  >
    这整个文本块将是'folded_style' 的值，但这
    一次，所有换行符都将替换为一个空格。
    像上面一样，空行被转换为换行符。
        “更多缩进” 的行也保留它们的换行符 -
        此文本将出现在两行中。
\####################
收藏类型
\####################
\# 嵌套使用缩进。2 空格缩进是首选（但不是必需的）。
a_nested_map ：
  键： 值
  another_key ： 另一个值
  another_nested_map ：
    你好： 你好
\# 地图不必有字符串键。
0.25 : 浮动键
\# 键也可以很复杂，比如多行对象
\# 我们使用 ? 后跟一个空格来指示复杂键的开始。
?  | 
  这是一个
  有多行的键
： 这是它的值
\# YAML 还允许在具有复杂键语法的序列之间进行映射
\# 一些语言解析器可能会抱怨
\# 一个例子
？ - 曼联
  \- 皇家马德里
:  \[ 2001-01-01 ,  2002-02-02 ]
\# 序列（相当于列表或数组）看起来像这样
\# （注意 “-” 算作缩进）：
a_sequence : 
  \-  Item 1 
  \-  Item 2 
  \-  0.5   # 序列可以包含不同的类型。
  \- 项目 4 
  \-  key :  value 
    another_key :  another_value 
  \- 
    \- 这是一个序列
    \- 在另一个序列内
  \-  -  - 嵌套序列指示器
      \- 可以折叠
\# 由于 YAML 是 JSON 的超集，您还可以编写 JSON 样式的地图和
\# 序列：
json_map :  {"key" :  "value"} 
json_seq :  \[ 3 ,  2 ,  1 ,  "takeoff" ]
和引号是可选的： {键:  \[ 3 ,  2 ,  1 , 起飞]}
\#######################
额外的 YAML 功能
\####################### #
\# YAML 还有一个方便的功能，称为 “锚点”，它可以让您轻松地
在文档中复制 # 内容。这两个键将具有相同的值： anchored_content 
: &anchor_name 此字符串将显示为两个键的值。other_anchor : \*anchor_name  

\# 锚点可用于复制 / 继承属性
base :  &base 
  name : 每个人都有相同的名字
\# 正则表达式 &lt;&lt; 称为 Merge Key Language-Independent Type。它用于
\# 表示一个或多个指定映射的所有键都应该插入
到当前映射中。
foo : 
  &lt;&lt; :  \*base 
  age :  10
bar : 
  &lt;&lt; :  \*base 
  age :  20
\# foo 和 bar 也有名字：每个人都有相同的名字
\# YAML 也有标签，可以用来显式声明类型。
explicit_string :  !!str  0.5 
\# 一些解析器实现了特定于语言的标签，例如 Python 的
\# 复数类型。
python_complex_number :  !!python/complex  1+2j
\# 我们也可以使用带有特定语言标签的 yaml 复杂键
？ !!python/tuple  \[ 5 ,  7 ] 
:  57 
\# 在 Python 中将是 {(5, 7): '57'}
\#####################
额外的 YAML 类型
\####################
\# 字符串和数字不是 YAML 可以理解的唯一标量。
\# ISO 格式的日期和日期时间文字也被解析。
日期时间： 2001-12-15T02:59:43.1Z 
datetime_with_spaces ： 2001-12-14 21:59:43.10 -5
日期： 2002-12-14
\# !!binary 标签表示一个字符串实际上是
二进制 blob 的 base64 编码 # 表示。
gif_file :  !! 二进制 | 
  R0lGODlhDAAMAIQAAP//9/X17unp5WZmZgAAAOfn515eXvPz7Y6OjuDg4J+fn5 
  OTk6enp56enmlpaWNjY6Ojo4SEhP/++f/++f/++f/++f/++f/++f/++f/++f/+ 
  \+f/++f/ ++f/++f/++f/++SH+Dk1hZGUgd2l0aCBHSU1QACwAAAAADAAMAAAFLC 
  AgjoEwnuNAFOhpEMTRiggcz4BNJHrv/zCFcLiwMWYNG84BwwEeECcgggoBADs=
\# YAML 也有一个集合类型，看起来像这样：
set : 
  ?  项目 1 
  ？ 项目 2 
  ？ 第 3 项
或： {第 1 项， 第 2 项， 第 3 项}
\# 集合只是具有空值的映射；以上等价于：
set2 : 
  item1 :  null 
  item2 :  null 
  item3 :  null
...   # 文件结束

### 更多资源

-   [YAML 官网](https://yaml.org/)
-   [在线 YAML 验证器](http://www.yamllint.com/)

* * *

有建议吗？也许是更正？在 Github Repo 上[打开一个问题，或自己提出](https://github.com/adambard/learnxinyminutes-docs/issues/new)[拉取请求](https://github.com/adambard/learnxinyminutes-docs/edit/master/yaml.html.markdown)！

最初由 Leigh Brenecki 贡献，并由[11 个贡献者](https://github.com/adambard/learnxinyminutes-docs/blame/master/yaml.html.markdown)更新。 
 [https://learnxinyminutes.com/docs/yaml/](https://learnxinyminutes.com/docs/yaml/)
