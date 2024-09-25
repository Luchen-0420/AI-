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

对只有url地址的分享链接，只做提取平台处理，复制```第六步配置过的大模型节点```，做提示词修改和```删除```title输出变量。

![节点配置](https://github.com/user-attachments/assets/a718a4e4-ef7a-49f2-8332-8bc343cf81d6)

**1. 提示词**

```
## 指令：
1. 仔细分析给定的{{input}}，识别出平台名称，使用最常见、最正式的名称。例如：
   - 使用平台的官方中文名称（如果有）
   - 避免使用缩写或非正式的别称
   - 保持名称的一致性，每次对同一平台使用相同的名称
2. 直接返回平台名称，不要添加任何解释、格式或额外的评论。
```

**2. 测试大模型节点**

![youtube分享链接测试](https://github.com/user-attachments/assets/418cd5ca-08ff-49f0-87d3-1170f875961c)


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

## 十、图文类--收集日期插件

不管是图文还是视频，我们都要记录时间，所以插件位置放在if判断前，如下图所示。

![添加日期插件](https://github.com/user-attachments/assets/2cdaa433-4e77-42d9-a59f-5c2303d8b91a)

![放置位置](https://github.com/user-attachments/assets/d6c6cd4d-1a28-443e-8975-c98878c990ce)

## 十一、数据格式化并写入飞书多维表格

**1. 新建飞书多维表格插件**

插件市场找到```飞书多维表格```添加```add_records```。可以看到2个必须填写参数。

**app_token：** 路径参数

**records：** 要插入的内容，json格式

![多维表格add_records](https://github.com/user-attachments/assets/875da969-153f-43f7-b055-c51df5da3413)

