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

![功能清单](https://github.com/user-attachments/assets/f542ca83-8a4f-4cb6-b0ee-8a2bb288f477)


### 思路拆解：

```1张多维表格```：1张整理具体指向内容的分享链接。

```2个工作流```：1个写入多维表格的工作流+1个推荐阅读工作流。

```1个bot设置```：设定bot的人设与回复逻辑

# 准备工作     

## 一、飞书多维表格建立 

可以根据自己的需求，进行修改。以下仅个人在设计字段时的一些思路。

**1. 表格设计前思考为什么要收藏这篇文章：**

① 属于什么领域，属于什么性质（科普、纯技术、实操....），什么阶段（初级、中级、进阶....）等等......

② 文章传递的什么，如：解决什么问题？提出什么观点？介绍什么新技术？让你觉得值得细看的点在于？等等......

③ 别人推荐的，但是现在没空细看，先收录一下


**2. 表格设计**

```字段说明```如下：

| 字段名     | 飞书字段类型 | 字段说明                                                                 |
|------------|---------------|--------------------------------------------------------------------------|
| 链接       | 文本        | 分享的链接，点击即可跳转阅读                                               |
| 标题       | 文本        | 标题                                                                     |
| 摘要       | 文本        | 通篇大概讲了什么，可以由jina-reader插件自动生成                               |
| 来源       | 单选          | 来自哪个平台，如：b站、小红书、网站等等。后续如果扩展做数据统计时可作为一项分类指标   |
| 体裁       | 单选         | 图文、视频                                                                |
| tag       | 多选         | 按领域、功能等做一个划分，也可以称它为某个合集                                |
| 状态       | 单选         | 已阅读、未阅读                                                             |
| 收集日期   | 文本        | 写入表格日期，因为飞书时间格式和插件时间格式有出入，我太懒了不想做处理，直接设置文本型 |
| 收藏原因   | 文本        | 为什么要收录这篇                                                          |
| 读后感     | 文本        | 读后感                                                                   |
| 价值等级   | 单选         | 这篇文章含金量怎么样，可自己设置值，比如：低、中、高                          |

**3. 新建多维表格注意事项**

1. 个人用户直接从"多维表格"新建。企业用户在云文档内新建也没关系。

2. 表格链接在浏览器中一定不能是wiki

![新建多维表格](https://github.com/user-attachments/assets/b402335e-b5fa-47fd-9b7c-3af8b6829e34)

![wiki](https://github.com/user-attachments/assets/4752ef80-29f1-46ca-a480-c256f1d28de4)

![base](https://github.com/user-attachments/assets/e2325b1c-3fad-4a16-822b-eb92ad911639)

![空表示例](https://github.com/user-attachments/assets/e4e83bee-4e3e-4f7b-8066-d0f213d1f0ec)

## 二、coze新建bot、工作流的位置

注册并登录```https://www.coze.cn/home```，```个人空间```或者```团队空间```都可以。先新建一个bot。

![coze](https://github.com/user-attachments/assets/769a3297-0d00-4741-b7d7-67b35e61f3a4)

![新建bot：AI·个人知识管理小助手](https://github.com/user-attachments/assets/a9c23827-06b8-49cd-a7e1-04e2a93db26a)

## 三、关于分享链接处理

首先我设定收藏的类型分为：图文（纯文包含图文内）、视频。

收藏时我想要爬虫来给我写概要，选择jina-reader插件来测试下以上分类，发现视频类进行爬取其实是不太符合我想要的，所以```视频类只取标题```。

**另外注意，知乎链接是无法用jina-reader爬取的，本教程不处理知乎类链接**


![jina-reader对视频的概要](https://github.com/user-attachments/assets/298b82b9-b454-4d1f-81ef-6bf5b7aa2d6f)

再仔细看各个平台的分享链接形式，有些平台分享链接需要做一个提取```https链接```处理，比如：

```小红书分享链接```：

```
93 【大连｜4天21顿❣️人少嘎嘎好吃，本地人推荐‼️ - 马溜达 | 小红书 - 你的生活指南】 😆 lR3gT9A0eAQ5XWn 😆 https://www.xiaohongshu.com/discovery/item/667aacb8000000001e010eb2?source=webshare&xsec_token=AB1e31D0BGnaywnmwX7C37dGKIbrOsxGltreE-BRB1aeE=&xsec_source=pc_share
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

## 四、自定义InternLM插件

### 1. 新建插件

![新建插件1](https://github.com/user-attachments/assets/70cb5ed0-f1eb-4cf1-8849-660584294576)

![新建插件2](https://github.com/user-attachments/assets/6ba9f832-e4a3-4ac3-b36b-e9870d94bd7d)

![新建并进入代码编辑页面](https://github.com/user-attachments/assets/b8818b9a-dd99-4b56-b993-7aa6b4d91bf0)

### 2. 编辑插件

**2.1 配置元数据**

仔细阅读[书生浦语的API文档](https://internlm.intern-ai.org.cn/api/document)。其中模型、请求地址、api_key可以固定，我们设置输入prompt和user_request，输出为answer。

![配置元数据](https://github.com/user-attachments/assets/bda7d31b-d2ae-4657-bc1d-319ddfb6ef6a)

**2.2 源码**

修改第14行为你自己的API_KEY

```
import requests
from typing import TypedDict, Optional

# 入参
class Input(TypedDict):
    prompt: str
    user_request: str

# 输出
class Output(TypedDict):
    answer: str

# 固定的API配置
API_KEY = "" # 固定API密钥
API_URL = "https://internlm-chat.intern-ai.org.cn/puyu/api/v1/chat/completions"  # 固定请求地址

def handler(args) -> Output:
    # 获取输入变量
    prompt = args.input.prompt
    user_request = args.input.user_request

    # 调用merge_prompt函数合并提示词和用户要求
    full_prompt = merge_prompt(prompt, user_request)

    # 调用ask_question函数获取answer
    answer = ask_question(full_prompt)
    return {"answer": answer if answer else ""}

def merge_prompt(prompt: str, user_request: str) -> str:
    """提示词和用户需求拼接处理函数"""
    # 拼接提示词和用户需求
    full_prompt = f'{prompt}, 【用户提供的描述】 - {user_request}'
    return full_prompt

def ask_question(question: str) -> Optional[str]:
    try:
        headers = {
            "Content-Type": "application/json",
            "Authorization": f"Bearer {API_KEY}"
        }

        data = {
            "model": "internlm2.5-latest",
            "messages": [{
                "role": "user",
                "content": question
            }],
            "n": 1,
            "temperature": 0.8,
            "top_p": 0.9
        }
        response = requests.post(API_URL, headers=headers, json=data)
        message = response.json()

        if 'error' not in message:
            return message['choices'][0]['message']['content']
        else:
            print("无法获取模型回答:", message.get('error', 'Unknown error'))
            return None

    except requests.exceptions.RequestException as e:
        print(f"请求错误：{e}")
        return None
    except Exception as e:
        print(f"发生错误: {str(e)}")
        return None
```

**2.3 测试**

测试之前先在左下侧安装requests的包。

![测试结果](https://github.com/user-attachments/assets/9d177ce5-8457-435f-bd83-494e5971cb41)

**2.4 发布**

![发布](https://github.com/user-attachments/assets/791e585b-ba92-4b7a-8fbd-79a9d29216e2)

**2.5 在工作流中测试**

新建工作流，并添加我们自定义的插件。

![添加插件](https://github.com/user-attachments/assets/94d0018a-bb9c-4c44-be75-4a3d819ead4e)

进行输入参数设置。

![工作流设置](https://github.com/user-attachments/assets/566c3e49-821b-4f4a-8fca-808431501aaf)

点击右上角测试，进行工作流测试。

![测试结果](https://github.com/user-attachments/assets/d596fadb-eca6-4e39-a8d9-acdb25a2620c)

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

**4. 添加internlm_api插件**

![配置图](https://github.com/user-attachments/assets/665a4001-d19f-4e62-a359-b3d7d3cbda39)

```提示词```

```
# 任务
\n
根据用户输入， 提取出最能概括内容主题的句子或短语。
\n
# 输出格式要求
\n
直接输出原始JSON格式，不要使用任何包装或代码块。严格按照以下格式输出，不添加任何其他内容：
\n
{
"title": "在这里填写标题",
"siteName": "在这里填写平台名称"
}
\n
# 输出内容要求
\n
title：捕捉内容的核心主题，应该简洁明了，能够概括整体内容。不要使用"标题："等前缀
\n
siteName：仅回答URL归属的平台名称，不需要额外解释,不要使用"平台:"等前缀。使用最常见、最正式的名称。具体要求：
\n
- 使用平台的官方中文名称（如果有）
\n
- 避免使用缩写或非正式的别称
\n
- 保持名称的一致性，每次对同一平台使用相同的名称
\n
# 重要提醒
\n
- 直接输出内容，不要加```json或任何其他代码块标记
\n
- 不要将输出包装在任何其他字段中
\n
- 确保输出的是可以直接被解析的原始JSON
\n
- 除了指定的JSON结构外，不要输出任何其他内容
```

**5. 测试大模型节点**

分别对b站和抖音链接进行测试。

![b站](https://github.com/user-attachments/assets/92eb70da-f17c-4ac8-8596-c8ed610f19d1)

![抖音](https://github.com/user-attachments/assets/df939e16-d698-49d6-9921-59b9c9f10ce9)

接下来进行第2个```if选择器```的```else```情况处理————处理只有Url地址的分享。

**6. 提取参数**

添加代码节点，对大模型返回的结果进行提取。

![配置图](https://github.com/user-attachments/assets/d01d3f2f-41c2-4a00-b73a-ecbf503cc827)

```代码```

```
import json
async def main(args: Args) -> Output:
    text = args.params.get("input", "")

    # 如果有代码块标记，去除它们
    if "```json" in text:
        cleaned_text = text.replace("```json\n", "").replace("\n```", "").strip()
    else:
        cleaned_text = text.strip()


    # 尝试解析JSON
    try:
        data = json.loads(cleaned_text)
        title = data.get("title", "")
        site_name = data.get("siteName", "")
    except json.JSONDecodeError:
        title = ""
        site_name = ""

    ret: Output = {
        "title": title,
        "sitename": site_name
    }
    return ret
```

运行结果如下：

![运行结果](https://github.com/user-attachments/assets/74fee6b1-a5f6-4f52-8748-bf0b3cdd7c01)

## 七、"视频"分支---只有url地址的分享处理

对只有url地址的分享链接，做提取平台处理和对收藏理由进行提炼大概标题，复制```第六步配置过的大模型节点```，做提示词修改。

我们需要先将url和reason拼接一起传递给插件。



**1. 参数拼接**

添加文本处理节点并设置拼接参数，点击节点测试按钮，测试结果如下：

![测试结果](https://github.com/user-attachments/assets/60d7ccde-c11c-415b-8cba-98078f09c263)

**2. 提示词**

```
# 任务
\n
根据用户输入， 提取出最能概括内容主题的句子或短语、识别出平台。
\n
# 输出格式要求
\n
严格按照以下格式输出，不添加任何其他内容，直接输出原始JSON格式，不要使用任何包装或代码块：
\n
{
"title": "在这里填写标题",
"siteName": "在这里填写平台名称"
}
\n
# 输出内容要求
\n
title：根据用户传递的reason参数内容捕捉内容的核心主题，应该简洁明了，能够概括整体内容。不要使用"标题："等前缀
\n
siteName：仅回答url归属的平台名称，不需要额外解释,不要使用"平台:"等前缀。使用最常见、最正式的名称。具体要求：
\n
- 使用平台的官方中文名称（如果有）
\n
- 避免使用缩写或非正式的别称
\n
- 保持名称的一致性，每次对同一平台使用相同的名称
\n
# 重要提醒
\n
- 直接输出内容，不要加```json或任何其他代码块标记
\n
- 不要将输出包装在任何其他字段中
\n
- 确保输出的是可以直接被解析的原始JSON
\n
- 除了指定的JSON结构外，不要输出任何其他内容

```

**3. 测试大模型节点**



**4. 返回结果参数提取**

![配置](https://github.com/user-attachments/assets/bd277623-9dc3-4c66-af2f-3368cc1954f4)

```代码```

```
import json
async def main(args: Args) -> Output:
    text = args.params.get("input", "")

    # 如果有代码块标记，去除它们
    if "```json" in text:
        cleaned_text = text.replace("```json\n", "").replace("\n```", "").strip()
    else:
        cleaned_text = text.strip()


    # 尝试解析JSON
    try:
        data = json.loads(cleaned_text)
        title = data.get("title", "")
        site_name = data.get("siteName", "")
    except json.JSONDecodeError:
        title = ""
        site_name = ""

    ret: Output = {
        "title": title,
        "sitename": site_name
    }
    return ret
```

![测试结果](https://github.com/user-attachments/assets/b6da171e-4f40-40a0-b209-349a2c496424)



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
5. 添加jina-reader提取的 URL 到结果中

```
import re
from typing import List, Tuple, Dict, Any
async def main(args: Args) -> Output:
    params = args.params
    ret: Output = {
        "content": []
    }

    # 1. 确保获取的参数是字符串

    raw_content = str(params.get("content", {}).get("content", ""))
    title = str(params.get("content", {}).get("title", ""))
    url = str(params.get("content", {}).get("url", ""))

    original_char_count = len(raw_content)

    # 2. 使用正则表达式将 Markdown 超链接和图片部分进行处理
    def markdown_to_text(md: str) -> Tuple[str, List[str]]:
        urls = re.findall(r'\[.*?\]\((.*?)\)', md)
        md = re.sub(r'\[([^\]]+)\]\([^\)]+\)', r'\1', md)
        md = re.sub(r'!\[.*?\]\(.*?\)', '', md)
        md = re.sub(r'[#*`-]', '', md)
        md = re.sub(r'\n+', '\n', md)
        return md.strip(), urls

    plain_text, extracted_urls = markdown_to_text(raw_content)

    # 3. 合并换行符处理、去除首尾空白字符、去除连续的横线与等号
    stripped_content = re.sub(r'-{2,}', '', plain_text)
    stripped_content = re.sub(r'={2,}', '', stripped_content)
    stripped_content = re.sub(r'\n+', '\n', stripped_content).strip()

    # 4. 去除 Markdown 后的字符统计
    clean_char_count = len(plain_text)

    # 5. 截取前5000字
    stripped_content = stripped_content[:5000]

    # 6. 添加提取的 URL 到结果中
    ret["content"].append({
        "original_char_count": original_char_count,
        "clean_char_count": clean_char_count,
        "plain_text": stripped_content,
        "url": url 
    })

    return ret
```

**2. 测试结果**

![长文章处理结果](https://github.com/user-attachments/assets/692a8ac2-d861-464e-9df4-ef5b192d8560)

## 十一、图文类--大模型进行标题、平台提炼

**1. 大模型提炼**

添加我们发布的internlm_api的插件。prompt输入如下：

```
# 任务
\n
根据用户输入的plain_text，生成对应信息 
\n
# 输出格式要求
\n
直接输出原始JSON格式，不要使用任何包装或代码块。严格按照以下格式输出，不添加任何其他内容：
\n
{
\n
  "summary": "在这里填写内容摘要",
\n
  "siteName": "在这里填写平台名称"
\n
}
\n
# 输出内容要求
\n 
summary：捕捉内容主题、阅读价值，生成一段简洁而全面的摘要。不要使用"摘要："或类似前缀。
\n
siteName：仅回答URL归属的平台名称，不需要额外解释。使用最常见、最正式的名称。具体要求：
\n
- 使用平台的官方中文名称（如果有）
\n
- 避免使用缩写或非正式的别称
\n
- 保持名称的一致性，每次对同一平台使用相同的名称
\n
# 重要提醒
\n
- 不要使用```json或任何其他代码块标记
\n
- 不要将输出包装在"answer"或任何其他字段中
\n
- 确保输出的是可以直接被解析的原始JSON
\n
- 除了指定的JSON结构外，不要输出任何其他内容
```

**2. 大模型提炼测试结果**

![测试结果](https://github.com/user-attachments/assets/74bebd8d-4f12-47be-b7aa-a44ae0ee2573)

**3. 代码节点提取**

由测试结果可以看出，summary和sitename被包裹在一起，所以我们添加一个代码节点进行参数提取输出。

![代码节点配置](https://github.com/user-attachments/assets/d3ea65c2-85f1-4519-9350-1f3633766e87)

```
import json

async def main(args: Args) -> Output:
    # 从输入中提取文本
    text = args.params.get("input", "")
    
    # 如果有代码块标记，去除它们
    if "```json" in text:
        cleaned_text = text.replace("```json\n", "").replace("\n```", "").strip()
    else:
        cleaned_text = text.strip()

    # 解析JSON
    try:
        data = json.loads(cleaned_text)
        summary = data.get("summary", "")
        site_name = data.get("siteName", "")
    except json.JSONDecodeError:
        summary = ""
        site_name = ""

    ret: Output = {
        "summary": summary,
        "sitename": site_name
    }
    return ret
```

**4. 代码节点测试结果**

![测试结果](https://github.com/user-attachments/assets/fd3946c5-2577-4cf4-bbbf-306b9be663fc)

## 十二、图文类--收集日期插件

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
        "tag": params.tag.split(/[，、,]/),
        "状态": "未阅读",
        "收集日期": params.date,
        "收藏原因": params.reason,
    };

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

**测试结果**

![测试结果](https://github.com/user-attachments/assets/8ee3b4a7-f37b-4145-b2a1-69d5641978b7)

**5. 输出到飞书表格**

与第一步的飞书多维表格插件连线，配置完成后，连接结束节点。测试看是否输出到表格。

![连线图](https://github.com/user-attachments/assets/fbed34ab-dd52-45f0-a857-5464b4d2f3a8)

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

复制```json序列化```、```写入飞书表格节点```。这里我们要把提取的信息（平台、标题）传到```json序列化节点```。

![复制后图](https://github.com/user-attachments/assets/7260232c-f6fe-496d-872a-7de289669db3)

**1. 对前面节点的输出进行处理**

增加```代码节点```用来统一if选择器分支出来的输出。

![先前的整体图](https://github.com/user-attachments/assets/7a5a6eb0-2158-4c8b-a6b5-d7bed95c7c8f)

![配置图](https://github.com/user-attachments/assets/0b89602b-6154-441d-9cba-e1097442415c)

```代码节点```如下：

```
async function main({ params }: Args): Promise<Output> {

    let ret; // 在外部声明 ret 变量

    if(params.handle){
        ret = {
            "title": params.t_title || '',
            "sitename": params.t_sitename || '' ,
            "url": params.t_url || ''
        };
    } else {
        ret = {
            "title": params.f_title || '',
            "sitename": params.f_sitename || '',
            "url": params.f_url || ''
        };
    }
    console.log('ret:', ret); // 打印 ret 变量查看最终结果
    return ret;
}
```

**2. json序列化**

```复制```图文类的json序列化代码节点进行修改。代码修改后如下：

```
async function main({ params }: Args): Promise<Output> { 
    const fieldsMap = {
        "标题": params.title,
        "链接": params.url,
        "摘要": params.summary,
        "来源": params.siteName,
        "体裁": params.genre,
        "tag": params.tag.split(/[，、,]/),
        "状态": "未阅读",
        "收集日期": params.date,
        "收藏原因": params.reason,
    };

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

![写入飞书表格](https://github.com/user-attachments/assets/853a93d2-715a-46be-907c-43ae5e32e18b)

![写入表格成功](https://github.com/user-attachments/assets/17b34b5a-dbca-40ac-a8d9-6d8f394df9c5)

![画册视图](https://github.com/user-attachments/assets/c284eb94-8e81-449e-ad7e-eba157263040)

## 十三、多平台测试

1. 小红书图文链接
2. 小红书视频链接
3. 抖音视频链接
4. b站视频链接
5. 公众号文章链接
6. YouTube视频链接
7.  网站具体内容链接

![表格视图](https://github.com/user-attachments/assets/d8e1dd85-3112-4bf7-ba76-b17750a30bd6)

![画册视图](https://github.com/user-attachments/assets/3ad43dbf-c90c-46e8-9c59-651d35453bff)


# 内容推荐工作流

根据用户输入，在多维表格中检索并匹配，输出符合用户兴趣的内容记录。

## 一、新建search_recommend工作流并配置



