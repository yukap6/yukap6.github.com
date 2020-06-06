# URL 路径的多单词连接规则

![](http://www.iseeit.cn/wp-content/uploads/2020/06/url.jpg)

目前已知的 `URL` 路径多单词连接大概有如下几种形式：

1. 中横线连接 `https://www.xx.com/basket/fruit/banana-milk`；
2. 下划线连接 `https://www.xx.com/basket/fruit/banana_milk`;
3. 全小写 `https://www.xx.com/basket/fruit/bananamilk`;
4. 小驼峰 `https://www.xx.com/basket/fruit/bananaMilk`;
5. 大驼峰 `https://www.xx.com/basket/fruit/BananaMilk`;

采用哪一种方式最优呢？顺势证明难度较大，可以反向思维，采取排除法来筛选。在这之前，需要澄清一个概念就是，URL 的大小写敏感问题。

URL 的结构是：`[协议]://[域名]/[路径]`，在上述示例中，协议为 `https`；域名为 `www.xx.com`；路径则为 `/basket/fruit/*` 这一部分。

按照规则，协议和域名不区分大小写，但是路径部分则根据服务器平台而定。Windows 和 Mac OS X 系统不区分大小写，而 UNIX 和 Linux 系统是区分大小写的。

准备工作就绪，开始排除法。

## 大小写切换增加输入难度

根据这条规则，采用小驼峰和大驼峰都是不建议的，常规域名一般都是小写，单个单词的路径也是小写，而多个单词的路径却要进行大小写切换才能正确拼写，增加了路径拼写难度，所以首先排除上述列表中的 `4. 小驼峰` 和 `5. 大驼峰`。

## 全小写可读性较差

`/basket/fruit/bananamilk` 这段路径中，多个单词部分 `bananamilk` 采用了全小写的写法，导致单词 `banana` 和 `milk` 边界不够清晰，可读性较差，暂时排除。

## 中横线连接和下划线连接，Who is the one?

剩下的两种方案中，貌似都没有上述已排除方案的明显缺点，即没有大小写切换的门槛，可读性也都较高，那么，哪个是最佳实践呢？

1. 中横线 `/fruit/banana-milk`
2. 下划线 `/fruit/banana_milk`

一般认为是：中横线更优。原因有2：
1. 中横线输入只需要一次键盘输入，按下中横线符号即可；而下划线需要组合键 `shift -` 才能输入；输入便捷性上，中横线略胜一筹；
2. 对于默认使用中文输入法的人来说，输入中横线也是一次按键即可；而下划线需要先将输入法切换为拼音输入，然后按下组合键 `shift -` 才能输入；输入便捷性上，又增加了一道门槛，鉴于此，中横线又胜一筹。

> 原来认为如此简单的一个多单词路径拼写都有这么多门道，继续加油探索……😂

以上。

> 本文首发于：[http://www.iseeit.cn/index.php/2020/06/06/url-multiple-word-connect-rule/](http://www.iseeit.cn/index.php/2020/06/06/url-multiple-word-connect-rule/)
