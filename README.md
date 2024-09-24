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

## 功能流程图

![先判断类型，再判断链接是否标准](https://github.com/user-attachments/assets/d3792829-5081-4847-8f86-f83ec16207bd)

## 新建content_arrange工作流

根据弹窗，一步步填写完成后就能看到图下页面。

![新建工作流](https://github.com/user-attachments/assets/b065f99d-92a9-40d9-b6ce-2a1599859665)

## 定义输入变量

点击```开始节点```的```新增```

![新增变量](https://github.com/user-attachments/assets/759325bb-c48f-4057-b64c-c5a7116d8261)

依次新增几个输入变量

![变量分类](https://github.com/user-attachments/assets/9b1206b2-b9e0-4956-a8af-4dc35085059a)


我们要新增的变量分别有```链接```、```体裁```、```tag```、```收藏原因```

![新增输入变量](https://github.com/user-attachments/assets/0d298f20-4f4e-468a-8b81-e02e5a8d92e1)

## 判断体裁

点击左侧```if选择器```节点，并配置。

![新增if选择器节点](https://github.com/user-attachments/assets/d4e61291-a1ff-4eb4-a1e2-4438e7b1e6ab)

![节点配置](https://github.com/user-attachments/assets/bbac71ee-223d-448a-b7bd-45f222769a7e)

## 链接提取

**Python代码进行链接提取**

添加```代码```节点，设置url输出变量后，点击```在IDE中编辑```，选择```python```，复制以下代码

![1727185118040](https://github.com/user-attachments/assets/98a32be5-445c-488b-87cc-eff8e31b5308)

![IDE编辑页面](https://github.com/user-attachments/assets/c0e97646-2a65-4e57-9370-11762d5c369d)

```
async def main(args: Args) -> Output:
    params = args.params
    share_link = params['input']
    
    url = ""
    start = share_link.find("https")
    if start != -1:
        end = share_link.find(" ", start)
        if end == -1:
            end = len(share_link)
        url = share_link[start:end]

    ret: Output = {
        "url": url
    }
    return ret
```





