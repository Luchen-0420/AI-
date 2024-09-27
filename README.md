# AI·个人知识收藏小助手
手把手带你用coze从0实现一个AI·收藏助手，并接入飞书文档+微信公众号，让你随时随地都能无感收藏+推荐阅读+关键词灵感记录。

# 前情概要                                                          

## 背景

1. 太懒，从没统一整理过
2. 看过就忘，或者收藏=看过，期间产生的思考与灵感未能及时以文本形式保存
3. 浏览器收藏夹懒得建文件夹按类分类，乱糟糟的，再也不会翻阅，一般只搜索浏览器记录
4. 公众号、博主关注了一大堆，再相见就是随缘。下次遇到相关问题又需要重新搜索一遍，做重复工作，有点低效。

## 设计思路

### 想要的功能：

![image](https://github.com/user-attachments/assets/f542ca83-8a4f-4cb6-b0ee-8a2bb288f477)


### 思路拆解：

```3个多维表格```：1张整理具体指向内容的分享链接、1张收藏各个公众号+博主+工具网站等、1张记录关键词+灵感+随手记。

```4个工作流```：分别写入3张多维表格的工作流+1个推荐阅读工作流。

```1个bot设置```：设定bot的人设与回复逻辑

# 准备工作     

## 一、飞书多维表格建立 

可以根据自己的需求，进行修改。以下仅个人在设计字段时的一些思路。

### 1. 具体指向内容的分享链接（内容收藏表）

**1.1 表格设计前思考为什么要收藏这篇文章：**

① 属于什么领域，属于什么性质（科普、纯技术、实操....），什么阶段（初级、中级、进阶....）等等......

② 文章传递的什么，如：解决什么问题？提出什么观点？介绍什么新技术？让你觉得值得细看的点在于？等等......

③ 别人推荐的，但是现在没空细看，先收录一下


**1.2 表格设计**

```字段说明```如下：

| 字段名     | 飞书字段类型 | 字段说明                                                                 |
|------------|---------------|--------------------------------------------------------------------------|
| 链接       | 文本        | 分享的链接，点击即可跳转阅读                                               |
| 标题       | 文本        | 标题                                                                     |
| 摘要       | 文本        | 通篇大概讲了什么，可以由jina-reader插件自动生成                               |
| 来源       | 单选          | 来自哪个平台，如：b站、小红书、网站等等。后续如果扩展做数据统计时可作为一项分类指标   |
| 体裁       | 单选         | 图文、视频                                                                |
| tag       | 单选         | 按领域、功能等做一个划分，也可以称它为某个合集                                |
| 状态       | 单选         | 已阅读、未阅读                                                             |
| 收集日期   | 文本        | 写入表格日期，因为飞书时间格式和插件时间格式有出入，我太懒了不想做处理，直接设置文本型 |
| 收藏原因   | 文本        | 为什么要收录这篇                                                          |
| 读后感     | 文本        | 读后感                                                                   |
| 价值等级   | 单选         | 这篇文章含金量怎么样，可自己设置值，比如：低、中、高                          |

![空表示例](https://github.com/user-attachments/assets/e4e83bee-4e3e-4f7b-8066-d0f213d1f0ec)


### 2. 收藏各个公众号、博主、工具网站等

```字段说明```如下：

| 字段名     | 飞书字段类型 | 字段说明                                                                 |
|------------|---------------|--------------------------------------------------------------------------|
| 账号id      | 文本        | 作为平台的唯一标识（微信公众号好像没有），在名称修改过的情况下可以通过ID找到 |
| 账号名称    | 文本         | 账号名称                                                                    |
| 所属领域    | 单选         | 比如农业、AI等等，一个大的领域范畴                               |
| 看点关键词  | 多选         | 细分小的领域，账号自己标注的tag或者你赋予它的tag 。比如它属于AI大领域中的文生图、视频生成等等 |
| 来源       | 单选          | 来自哪个平台，如：b站、小红书、网站等等。后续如果扩展做数据统计时可作为一项分类指标   |
| 备注       | 文本         | 其他觉得有有必要的理由                                                                |

![空表示例](https://github.com/user-attachments/assets/c072290a-e586-4101-a156-e9757b9ed5f0)


### 3. 记录关键词、灵感、随手记

```字段说明```如下：

| 字段名     | 飞书字段类型 | 字段说明                                                                 |
|------------|---------------|--------------------------------------------------------------------------|
| 日期       | 文本          | 记录的时间，和第一张表一样，不想做日期处理，才设置文本型 |
| 记录       | 文本          | 记录脑中所想 或者提醒自己有空要根据什么关键词来查找什么                       |
| 类型       | 单选          | 打tag，比如产品设计、职业规划、稍后查询等等                              |
| 灵感来源    | 文本         | 比如来自看到的什么场景，经历的什么事情引发的思考 |    

![空表示例](https://github.com/user-attachments/assets/f8bae530-f268-4223-adcc-27dfa63e97e7)

## 二、coze新建bot、工作流的位置

注册并登录```https://www.coze.cn/home```，```个人空间```或者```团队空间```都可以。先新建一个bot。

![coze](https://github.com/user-attachments/assets/769a3297-0d00-4741-b7d7-67b35e61f3a4)

![新建bot：AI·个人知识管理小助手](https://github.com/user-attachments/assets/a9c23827-06b8-49cd-a7e1-04e2a93db26a)

## 三、关于分享链接处理

首先我设定收藏的类型分为：图文（纯文包含图文内）、视频。

收藏时我想要爬虫来给我写概要，选择jina-reader插件来测试下以上分类，发现视频类进行爬取其实是不太符合我想要的，所以```视频类只取标题```。

![jina-reader对视频的概要](https://github.com/user-attachments/assets/298b82b9-b454-4d1f-81ef-6bf5b7aa2d6f)

再仔细看各个平台的分享链接形式，有些平台分享链接需要做一个提取```https链接```处理，比如：

```小红书分享链接```：

```
93 【大连｜4天21顿❣️人少嘎嘎好吃，本地人推荐‼️ - 马溜达 | 小红书 - 你的生活指南】 😆 lR3gT9A0eAQ5XWn 😆 https://www.xiaohongshu.com/discovery/item/667aacb8000000001e010eb2?source=webshare&xsec_token=AB1e31D0BGnaywnmwX7C37dGKIbrOsxGltreE-BRB1aeE=&xsec_source=pc_share
```

```知乎分享链接```：

```
手搓一个最小的 Agent 系统 — Tiny Agent - RedHerring的文章 - 知乎
https://zhuanlan.zhihu.com/p/699732624
```

```抖音分享链接```：

```
9.99 Eus:/ 12/21 J@i.Ch 大连千万级博主总结的大连旅游注意事项，看这一个就足够足够了 # 大连旅游攻略 # 大连 # 大连美食   https://v.douyin.com/iksNQqte/ 复制此链接，打开Dou音搜索，直接观看视频！
```

```公众号文章分享链接```：

```
https://mp.weixin.qq.com/s/d8mvSU77l_xAHKo3gqyPGQ
```

```B站分享链接```：

```
【SOLIDWORKS 教学 精品教程 | 2024年新修 | B站 点赞 播放 收藏 NO.1】 https://www.bilibili.com/video/BV1iw411Z7HZ/?share_source=copy_web&vd_source=93b749d0faa4f6da4b3a9891caf73e4d
```

```youtube分享链接```：

```
https://youtu.be/siHNmXLZ8y4?si=2KwcS5wElJBK4Eva
```

# 内容收藏工作流搭建

## 一、功能流程图

![先判断类型，再判断链接是否标准](https://github.com/user-attachments/assets/d3792829-5081-4847-8f86-f83ec16207bd)

## 二、新建content_arrange工作流

根据弹窗，一步步填写完成后就能看到图下页面。

![新建工作流](https://github.com/user-attachments/assets/b065f99d-92a9-40d9-b6ce-2a1599859665)

## 三、定义输入变量

点击```开始节点```的```新增```

![新增变量](https://github.com/user-attachments/assets/759325bb-c48f-4057-b64c-c5a7116d8261)

依次新增几个输入变量

![变量分类](https://github.com/user-attachments/assets/9b1206b2-b9e0-4956-a8af-4dc35085059a)


我们要新增的变量分别有```链接```、```体裁```、```tag```、```收藏原因```。

判断是否要进行链接提取，我们需要加一个boolean值的变量作为判断。

![新增输入变量](https://github.com/user-attachments/assets/3fa3bf3e-8314-4f18-b753-d3d947aafd03)


## 四、判断体裁

点击左侧```if选择器```节点，并配置。

![新增if选择器节点](https://github.com/user-attachments/assets/d4e61291-a1ff-4eb4-a1e2-4438e7b1e6ab)

![节点配置](https://github.com/user-attachments/assets/bbac71ee-223d-448a-b7bd-45f222769a7e)

## 五、视频类--选择是否需要提取url地址

没有直接默认全部提取是因为，有的分享链接包含了视频标题信息，有的只有url地址（比如youtube)。

针对包含其他信息的链接，需要做一个url+标题+平台提取。针对只有url地址的，只提取平台。

新增一个```if选择器```节点，并配置。

![节点配置](https://github.com/user-attachments/assets/503c6a62-db14-42d9-af89-4878f4f7b22b)

## 六、"视频"分支---包含其他信息的分享处理

```
9.99 Eus:/ 12/21 J@i.Ch 大连千万级博主总结的大连旅游注意事项，看这一个就足够足够了 # 大连旅游攻略 # 大连 # 大连美食   https://v.douyin.com/iksNQqte/ 复制此链接，打开Dou音搜索，直接观看视频！
```
可以看出分享链接里除了包含标题部分，还包含了tag。我不采集tag的原因是太多了，自媒体打tag是为了入池，自己做知识整理的时候，不想打太多tag。有兴趣想直接提取tag写入的，可以自己改造下。

针对以上分享链接，我们要做2件事：

1. 提取出有效url地址
2. 提取出标题和平台名称

```提取有效url地址```：用到```代码节点```来保证提取的准确性。

```提取标题、平台名称```：用到```大模型节点```，设定提示词，让大模型来提取出我们想要的信息。

**1. Python代码进行链接提取**

选择用代码节点的原因是大模型节点有时候并不能100%能按照要求提取，用代码节点更可靠些。

添加```代码```节点，设置url输出变量后，点击```在IDE中编辑```，选择```python```，复制以下代码

![添加代码节点](https://github.com/user-attachments/assets/98a32be5-445c-488b-87cc-eff8e31b5308)

![IDE编辑页面](https://github.com/user-attachments/assets/c0e97646-2a65-4e57-9370-11762d5c369d)

**2. 代码思路**

1. 查找 URL 起始位置：使用 find() 方法查找字符串中 http:// 或 https:// 的起始位置。如果找到其中一个，就记录这个位置作为 URL 的起始位置。如果没有找到任何有效的 URL 起始位置，返回 "没有找到有效url"。
2.  查找 URL 结束位置：初始化一个变量 end 为 URL 的起始位置。使用 while 循环遍历字符串，逐个检查字符，继续向后移动 end 位置，直到遇到空格、换行符、逗号、句号或其他标志着 URL 的结束标点符号。
3.  截取 URL：使用确定的起始和结束位置，从输入字符串中截取 URL。去掉可能存在的前后空白。
4.  特殊处理抖音短链接：如果提取的 URL 是来自于抖音短链接（v.douyin.com），且没有以 / 结尾，则尝试将 URL 截取到最后一个 /。这样可以确保抖音的短链接以 / 结尾。

```
async def main(args):
    params = args['params']
    share_link = params['input']

    # 查找 "http://" 或 "https://" 的起始位置
    start = share_link.find("http://")
    if start == -1:
        start = share_link.find("https://")
    
    if start != -1:
        # 查找 URL 末尾的空格、换行符、逗号、句号等作为终止位置
        end = start  # 初始化结束位置为起始位置
        while end < len(share_link) and share_link[end] not in [" ", "\n", ",", "。", "，", "？"]:
            end += 1

        # 截取 URL
        url = share_link[start:end].strip()
        
        # 针对抖音短链接，确保以 "/" 结尾
        if "v.douyin.com" in url and not url.endswith("/"):
            last_slash = url.rfind("/")
            url = url[:last_slash + 1] if last_slash != -1 else url
    else:
        url = "没有找到有效链接"
    
    return {"url": url}
```

**3. 测试代码**

![小红书移动端分享链接](https://github.com/user-attachments/assets/59289a69-3f2f-4133-bc9a-d494036153c7)

![小红书网页端分享链接](https://github.com/user-attachments/assets/16d2ccea-f645-4b6b-9120-ea1059bb3ccc)

![抖音移动端分享链接](https://github.com/user-attachments/assets/3412396b-e792-47b0-ae23-dcb8d85a87f8)

**4. 新建大模型节点**

![大模型节点配置](https://github.com/user-attachments/assets/22268a1b-263d-44ac-9c89-591804aa3958)

1. 选择你想用的```大模型```，我这里选的kimi。
2. 定义输入变量：变量值引用的是原始分享链接的url（开始节点的url）
3. 设置提示词：引用输入参数"input"。
4. 定义输出变量：我需要输出一个标题、一个平台名称。

```提示词```

```
# 分享链接标题提取提示词

你是一个专门用于从任何社交媒体分享链接中提取标题的AI助手。你的任务是从给定的文本中识别并提取出最相关的标题或主要内容描述，并直接返回结果，不添加任何额外的格式或说明。

## 指令：

1. 仔细分析给定的{{input}}，寻找表示视频或文章主要内容的关键句子或短语。
2. 如果能识别出平台名称，使用最常见、最正式的名称。例如：
   - 使用平台的官方中文名称（如果有）
   - 避免使用缩写或非正式的别称
   - 保持名称的一致性，每次对同一平台使用相同的名称
3. 提取出最能概括内容主题的句子或短语。
4. 如果存在多个可能的标题，选择最完整、最有信息量的一个。
5. 直接返回提取出的标题，不要添加任何解释、格式或额外的评论。
6. 不要使用引号或其他标点符号包裹提取的标题。

现在，请根据以上指令处理给定的文本，并直接提取出最相关的标题或主要内容描述。
```

**5. 测试大模型节点**

**测试单个节点时，可以把"引用"改为"输入"，下面涉及的所有单个节点测试，我就不赘述这句了。**

分别拿抖音、B站2个视频链接测试下。

![抖音链接提取](https://github.com/user-attachments/assets/de224f15-000d-4f14-9a48-e612ee2eddd2)

![哔哩哔哩链接提取](https://github.com/user-attachments/assets/8d4734f6-ace0-43b8-a5de-78adb771cb69)

截止这里，我们的工作流应该如图所示。
![整体图](https://github.com/user-attachments/assets/6e75a4f8-12c6-4945-af07-ced93058f330)

接下来进行第2个```if选择器```的```else```情况处理————处理只有Url地址的分享。


## 七、"视频"分支---只有url地址的分享处理

对只有url地址的分享链接，做提取平台处理和对收藏理由进行提炼大概标题，复制```第六步配置过的大模型节点```，做提示词修改。

![配置信息](https://github.com/user-attachments/assets/4e3f67d5-8eba-46b9-884a-0685e80603ad)

**1. 提示词**

```
## 指令：
1. 仔细分析给定的{{input}}，识别出平台名称，使用最常见、最正式的名称。例如：
   - 使用平台的官方中文名称（如果有）
   - 避免使用缩写或非正式的别称
   - 保持名称的一致性，每次对同一平台使用相同的名称
2. 直接返回平台名称，不要添加任何解释、格式或额外的评论。
3.仔细分析给定的 {{reason}}，提炼出不大于10个字的标题
```

**2. 测试大模型节点**

![youtube测试1](https://github.com/user-attachments/assets/3975c5f7-d948-4b6a-ba3b-49c380f7df1b)

![youtube测试2](https://github.com/user-attachments/assets/15a1b71b-b624-4651-8825-fae583c0e55b)


截止这里，对```视频类```链接处理情况都已经完成。工作流整体如图所示：

![整体图](https://github.com/user-attachments/assets/53722d53-f047-465f-a12a-e960787da7c8)

## 八、图文类--是否需要提取url地址

对图文类的处理主要有：

1. 提取出有效url地址
2. 通过插件爬取有效内容进行对应表格列填充

同样，图文类分享链接格式也分两种：标准、不标准。插件可以对图文类进行有效内容爬取。所以这里我偷个懒**不写if选择器**，所有的链接输入进来后统一进行```提取有效url地址```操作。你也可以自己加一个```if选择器```，像```视频类```部分处理。


## 九、图文类--提取有效url地址

复制```第六步```的**代码节点**，```删除```代码中**针对抖音链接处理**的代码如下：

```
async def main(args):
    params = args['params']
    share_link = params['input']

    # 查找 "http://" 或 "https://" 的起始位置
    start = share_link.find("http://")
    if start == -1:
        start = share_link.find("https://")
    
    if start != -1:
        # 查找 URL 末尾的空格、换行符、逗号、句号等作为终止位置
        end = start  # 初始化结束位置为起始位置
        while end < len(share_link) and share_link[end] not in [" ", "\n", ",", "。", "，", "？"]:
            end += 1

        # 截取 URL
        url = share_link[start:end].strip()
    else:
        url = "没有找到有效链接"
    
    return {"url": url}

```

**测试节点**

![小红书图文链接测试](https://github.com/user-attachments/assets/25707da5-6b81-4caf-872a-d41b77df2e59)

## 九、图文类--插件爬取内容

**1. 添加jina-reader插件**

![添加插件](https://github.com/user-attachments/assets/2dae318a-f550-425d-b71a-ac9fcd435d41)

![连线](https://github.com/user-attachments/assets/d7a221dc-133c-4556-bf6e-e612e201a9a1)

**2. 测试插件**

![内容](https://github.com/user-attachments/assets/53137da1-a784-432e-9e27-9612459aed4d)

![标题](https://github.com/user-attachments/assets/7dec405c-561f-48d3-bf1f-1629e5ac0d75)

回过头去看我们设计的表元素，还差一个收集日期。

![变量分类](https://github.com/user-attachments/assets/8baf5243-1045-4a42-b957-e6f27cdf48cc)

## 十、图文类--对爬取内容进行长度处理

大模型设置输入输出长度是有一定的限制的，爬取的内容太长作为输入会出现报错，我们要做一个处理。

找了一篇长篇幅公众号文章：```万字长文：OpenAI 发展史```

jina-reader插件返回结果如下（只截取一部分内容贴出），可以看出它是一个markdown格式：

```
Weixin Official Accounts Platform\n===============\n\n             \n\n \n\n![Image 1: cover_image](https://mmbiz.qpic.cn/mmbiz_jpg/90Kxd0FAJJdtFreOP5nfzhN65PIZZYpCbceEJ5AbLJGMeMBCNeVZibTVycngkY5JvnsHauiaRdWTJxecxLB5SyVg/0?wx_fmt=jpeg)\n\n万字长文：OpenAI 发展史\n===============\n\nOriginal lencx [浮之静](javascript:void\\(0\\);)\n\n![Image 2](https://mmbiz.qpic.cn/mmbiz_png/90Kxd0FAJJdtFreOP5nfzhN65PIZZYpCkILtaL8ibxJ27uG6y0hbQFMWeSW2rsu2PsQtdSzmLxGAicwN9ZPu7gZw/640?wx_fmt=png&from=appmsg)\n\n在这篇文章正式开始前，我想先给个相关阅读，主要是我也没想到《[OpenAI 系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzIzNjE2NTI3NQ==&action=getalbum&album_id=2788323337876799489#wechat_redirect)》我已经写这么多内容。写得越多，了解越深，感慨也越多。话又说回来，\n\n*   [一文读懂 OpenAI](http://mp.weixin.qq.com/s?__biz=MzIzNjE2NTI3NQ==&mid=2247485662&idx=1&sn=b9a9f198b5536c84d94134e0b33c012e&chksm=e8dd492adfaac03c5ec3455926e9597b633c1f2c6ee2dc3e159c72ed070ff7ab237a068615c4&scene=21#wechat_redirect)\n    \n*   [OpenAI 大地震：Sam Altman 和 Greg Brockman 离职，微软加强与 OpenAI 合作！](http://mp.weixin.qq.com/s?__biz=MzIzNjE2NTI3NQ==&mid=2247488090&idx=1&sn=9fa295ec7ecf15e2070cfe53bbbe591c&chksm=e8dd53aedfaadab88941d58c9143e148f4358210f4c52498134f4d9bef50776c297e831cc519&scene=21#wechat_redirect)\n    \n*   [没有员工，OpenAI 什么也不是！](http://mp.weixin.qq.com/s?
```

**1. 思路**

1. 去除markdown格式（包括超链接、图片、符号等）
2. 合并换行符处理、去除首尾空白字符、去除连续的横线与等号
3. 输出原始字符数和去除后的字符是，看下对比
4. 截取前5000字（也可以少放点）

```
async def main(args: Args) -> Output:
    import re
    params = args.params
    ret: Output = {
        "content": []
    }

    # 1. 统计爬取内容的字符数
    raw_content = params.get("content", "")
    original_char_count = len(raw_content)

    # 2. 使用正则表达式将 Markdown 超链接和图片部分进行处理
    def markdown_to_text(md: str) -> str:
        # 转换 Markdown 中的超链接格式 [title](url) 为 title
        md = re.sub(r'\[([^\]]+)\]\([^\)]+\)', r'\1', md)

        # 删除 Markdown 中的图片格式 ![alt](url)
        md = re.sub(r'!\[.*?\]\(.*?\)', '', md)

        # 删除多余的 Markdown 标记，比如 `#`, `*`, `-` 等
        md = re.sub(r'[#*`-]', '', md)

        # 替换多重换行符为单个换行符
        md = re.sub(r'\n+', '\n', md)

        return md.strip()

    plain_text = markdown_to_text(raw_content)

    # 3. 合并换行符处理、去除首尾空白字符、去除连续的横线与等号
    stripped_content = re.sub(r'-{2,}', '', plain_text)
    stripped_content = re.sub(r'={2,}', '', plain_text)  # 去除连续的等号
    stripped_content = re.sub(r'\n+', '\n', plain_text).strip()

    # 4. 去除 Markdown 后的字符统计
    clean_char_count = len(plain_text)

    # 5. 截取前5000字

    stripped_content = plain_text[:5000] 

    # 将结果封装到列表中
    ret["content"].append({
        "original_char_count": original_char_count,
        "clean_char_count": clean_char_count,
        "plain_text": stripped_content
    })

    return ret


```

**2. 测试结果**

![长文章处理结果](https://github.com/user-attachments/assets/692a8ac2-d861-464e-9df4-ef5b192d8560)

## 十、图文类--收集日期插件

不管是图文还是视频，我们都要记录时间，所以插件位置放在if判断前，如下图所示。

![添加日期插件](https://github.com/user-attachments/assets/2cdaa433-4e77-42d9-a59f-5c2303d8b91a)

![放置位置](https://github.com/user-attachments/assets/d6c6cd4d-1a28-443e-8975-c98878c990ce)

## 十一、数据格式化并写入飞书多维表格

**1. 新建飞书多维表格插件**

插件市场找到```飞书多维表格```添加```add_records```。可以看到2个必须填写参数。

```app_token：``` 路径参数

```records：``` 插入记录，json格式

![插入add_records插件](https://github.com/user-attachments/assets/bdf1e598-b430-46c5-8d51-bf62a6adb88f)

![records字段](https://github.com/user-attachments/assets/332651b6-311f-48b8-84c5-e837594b69e4)

**2. 设置app_token**

为了更方便```切换```文档，这里不采取在工作流内固定，选择从外部bot定义一个变量传递给工作流。

**外部bot设置变量**

先复制飞书表格地址，再添加变量```article_url```，设置默认值为飞书表格地址。

![添加变量1](https://github.com/user-attachments/assets/4427172a-ea71-4dbb-9034-61b9d0f36703)

![添加变量2](https://github.com/user-attachments/assets/ae5dae32-bd15-4ca9-8ab4-2f3c19470a98)

**返回工作流并引入**

![添加变量节点](https://github.com/user-attachments/assets/24620777-acf9-4977-9ecb-64c11be6b077)

设置```从bot获取变量值```

![从bot获取变量值](https://github.com/user-attachments/assets/1eb61745-4f3d-48a1-ab9f-ef09ae4d5e4f)

**连线**

因为它和日期一样，无论是图文还是视频都要用到，所以我们把它放到```判断体裁节点的前面```。

![连线](https://github.com/user-attachments/assets/93c08e3f-47a5-4521-9bd0-f2e7bc83b7f0)

**3. 对内容提炼**

经过jina-reader+python代码处理过的内容还需要一轮大语言模型来帮我们用精简、通顺的文字来总结。

**大模型配置**

添加大模型节点并配置。虽然截取5000字，篇幅覆盖大多数公众号文章和小红书图文，但是不乏万字长文，加上，我们是要自己对文章进行阅读思考，不要过分依赖AI来阅读总结。所以用到大模型处理的只有2个部分内容：

1. 生成摘要
2. 辨别平台名称，保持一致性

![配置](https://github.com/user-attachments/assets/c5aac88b-8aa6-4515-a8f2-79bb1a93d6de)

```提示词```

```
# 任务
根据{{input}}，生成对应信息
 
# 输出要求
summary：捕捉内容主题、阅读价值，生成一段简洁而全面的摘要
siteName：只需要回答{{url}}归属什么平台，不用额外解释。使用最常见、最正式的名称。例如：
   - 使用平台的官方中文名称（如果有）
   - 避免使用缩写或非正式的别称
   - 保持名称的一致性，每次对同一平台使用相同的名称
```

**节点测试**

![公众号长文](https://github.com/user-attachments/assets/95387e19-adfa-42de-a1c9-a2704aa11333)

![小红书图文](https://github.com/user-attachments/assets/2bba4fb4-4e91-4785-8ed6-f34ec04a70a0)


**4. 拼接表格所需信息并json序列化**

我们需要拼接的信息如下：

1. 标题：jina-reader输出
2. 链接：jina-reader输出
3. 摘要：大模型节点输出
4. 来源：大模型节点输出
5. 体裁：开始节点输入
6. tag：开始节点输入
7. 状态：固定值（无需定义输入）
8. 收集日期：日期插件
9. 收藏原因：开始节点输入

**代码节点**

这里就没什么逻辑了，选择```javascript```来写而不是python，代码比较简单。

```js代码```

```
async function main({ params }: Args): Promise<Output> {
    const fieldsMap = {
        "标题": params.title,
        "链接": params.url,
        "摘要": params.summary,
        "来源": params.siteName,
        "体裁": params.genre,
        "tag":  params.tag,
        "状态": "未阅读",
        "收集日期": params.date,
        "收藏原因": params.reason,
    };

    const fields = JSON.stringify(fieldsMap);
    const ret = {
        "output": {
            "fileds": fields
        }
    };

    return ret;
}
```

**测试结果**

![测试结果](https://github.com/user-attachments/assets/8ee3b4a7-f37b-4145-b2a1-69d5641978b7)

**5. 输出到飞书表格**

与第一步的飞书多维表格插件连线，配置完成后，连接结束节点。测试看是否输出到表格。

![连线图](https://github.com/user-attachments/assets/63254d9c-be6c-48c3-9926-4f04db17a5f8)

**测试**

```小红书图文测试```

![输入](https://github.com/user-attachments/assets/3cd066bc-1635-459b-979e-953f37c1ea2d)

第一次需要授权，复制里面完整的网址到浏览器内进行授权

![授权](https://github.com/user-attachments/assets/3ef21292-67ba-4c9a-b6bb-97c834bb696b)

![授权页面](https://github.com/user-attachments/assets/3f92be86-0073-4fbc-af96-cf90801f537d)

**记录新增成功**

![添加成功](https://github.com/user-attachments/assets/68b53a79-fcdc-4b44-8a5f-66dd92780681)

![飞书表格](https://github.com/user-attachments/assets/192e611e-65e6-48c8-a915-25ff53707e9c)

**画册视图**

可以设置画册视图来更好地查看

![新建画册视图](https://github.com/user-attachments/assets/d3578f93-81f6-4468-b3ba-cb33c8d7e19c)

![画册](https://github.com/user-attachments/assets/2c007f57-1a84-41ff-b84f-ac737108bbfc)

## 十二、对视频类进行写入飞书表格

```截止第七步```我们对视频类的情况已经做了处理，现在整个工作流的页面应该如图所示，接下来我们要对视频类的完成json序列化、写入飞书表格操作：

![整体图](https://github.com/user-attachments/assets/f1d21861-994f-4727-9fae-2ac7a962a3c7)

**1. json序列化**

```复制```图文类的json序列化代码节点进行修改。

```
async function main({ params }: Args): Promise<Output> {
    const fieldsMap = {
        "标题": params.title,
        "链接": params.url,
        "摘要": "",
        "来源": params.siteName,
        "体裁": params.genre,
        "tag":  params.tag,
        "状态": "未阅读",
        "收集日期": params.date,
        "收藏原因": params.reason,
    };

    // 构建 fields 对象，符合你的格式要求
    const fields = Object.entries(fieldsMap).reduce((acc, [key, value]) => {
        acc[key] = value;
        return acc;
    }, {});

    const ret = {
        output: [
            { fields }
        ]
    };

    return ret;
}

```

**2. 写入飞书表格**

复制图文类的```写入飞书表格```节点，并连接```结束```节点。

![配置](https://github.com/user-attachments/assets/1c1b9601-ffcb-4c54-8735-bced0d4992d3)

**测试**

视频类逻辑需要修改，做标题抽取的大模型节点只能使用一个
