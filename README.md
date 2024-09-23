# AI·个人知识收藏小助手
手把手带你用coze从0实现一个AI·收藏助手，并接入飞书文档+微信公众号，让你随时随地都能无感收藏+调取阅读。

# 前情概要                                                          

## 背景

1. 太懒，从没统一整理过
2. 看过就忘，或者收藏=看过，期间产生的思考与灵感未能及时以文本形式保存
3. 浏览器收藏夹懒得建文件夹按类分类，乱糟糟的，再也不会翻阅，一般只搜索浏览器记录

## 设计思路

首先确定我们的分类：图文、视频、​网站。

视频类的进行爬取其实是不太符合我们想要的
![image](https://github.com/user-attachments/assets/298b82b9-b454-4d1f-81ef-6bf5b7aa2d6f)

小红书分享链接```19 koi发布了一篇小红书笔记，快来看吧！ 😆 ljsBbljsT1fHKNQ 😆 http://xhslink.com/a/aZJu0tZH8EmW，复制本条信息，打开【小红书】App查看精彩内容！```，不符合url规范

所以我们对要收藏的内容做第一次判断：是否能摘要内容&&是否要对链接格式化可以并为一类，设置一个bool变量作为判断

​比如现在我有以下4个链接需要收藏,分别是
1. 小红书一篇经验贴（疑似软广）···
2. 搜电子书超好用的实用网站
3. b站一个讲课视频
4. 微信公众号一篇文章
   
```
1. 19 koi发布了一篇小红书笔记，快来看吧！ 😆 ljsBbljsT1fHKNQ 😆 http://xhslink.com/a/aZJu0tZH8EmW，复制本条信息，打开【小红书】App查看精彩内容！

2. https://zh.annas-archive.org/

3. https://www.bilibili.com/video/BV1iw411Z7HZ/?spm_id_from=333.999.list.card_archive.click&vd_source=74ec5b85d8597ce8ecc8e71db65f9830

4. https://mp.weixin.qq.com/s/tuSt4R8AhsdKSICMrLWtpA


```



# 准备工作                                                              



